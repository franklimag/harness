# 自动同步机制

> 文件监听、增量同步、防抖策略 — 保持知识图谱实时更新。

## 一、概述

Auto-Sync 是 CodeGraph 的"活力"所在 — 它让知识图谱始终与源代码保持同步，无需手动重建。当开发者修改代码时，知识图谱在 2 秒内自动更新。

### 核心特性

| 特性 | 说明 |
|------|------|
| 原生 OS 事件 | 使用平台原生 API，不轮询 |
| 2s 防抖 | 合并频繁变更，避免过度更新 |
| 增量更新 | 只处理变更文件，不重建全图 |
| WAL 兼容 | 更新不阻塞查询 |
| 无需重启 | MCP Server 持续运行 |

## 二、文件监听机制

### 2.1 三平台 API

| 平台 | API | 特点 |
|------|-----|------|
| macOS | FSEvents | 高效的目录级事件，自动合并 |
| Linux | inotify | 文件级事件，需递归注册目录 |
| Windows | ReadDirectoryChangesW | 目录级异步事件，需管理 buffer |

### 2.2 事件类型

| 事件 | 触发条件 | CodeGraph 响应 |
|------|----------|---------------|
| 文件创建 (CREATE) | 新文件被创建 | 解析并加入图 |
| 文件修改 (MODIFY) | 文件内容变更 | 重新解析该文件 |
| 文件删除 (DELETE) | 文件被删除 | 从图中移除 |
| 文件重命名 (RENAME) | 文件被重命名 | 等同于 DELETE + CREATE |
| 目录创建 | 新目录出现 | 开始监听该目录 |
| 目录删除 | 目录被删除 | 停止监听，删除相关节点 |

### 2.3 监听范围

```
项目根目录
├── src/           ← 监听 ✅
├── lib/           ← 监听 ✅
├── app/           ← 监听 ✅
├── tests/         ← 监听 ✅ (测试代码也有调用关系)
├── node_modules/  ← 忽略 ❌
├── .git/          ← 忽略 ❌
├── dist/          ← 忽略 ❌
├── build/         ← 忽略 ❌
└── .codegraph/    ← 忽略 ❌ (自身数据库)
```

**忽略规则** (推测):
- 遵循 `.gitignore` 规则
- 内置忽略列表: `node_modules`, `.git`, `dist`, `build`, `__pycache__`, `.venv` 等
- 只监听已知语言的文件扩展名

## 三、防抖 (Debouncing) 策略

### 3.1 为什么需要防抖？

```
不防抖的情况:
  保存文件 (t=0ms)     → 触发重建
  格式化保存 (t=50ms)  → 触发重建 (正在进行中!)
  自动导入 (t=100ms)   → 触发重建 (排队!)
  热重载 (t=200ms)     → 触发重建 (排队!)

  结果: 4 次重建，3 次是浪费的
```

### 3.2 2 秒防抖机制

```
事件 A (t=0)    ──┐
事件 B (t=500)  ──┤ 缓冲区收集
事件 C (t=1200) ──┤
                  │ 最后一个事件后等待 2s
                  │
                  └──▶ (t=3200) 批量处理 [A, B, C]
```

#### 防抖逻辑

```typescript
// 概念实现
class SyncDebouncer {
  private pendingFiles = new Set<string>();
  private timer: NodeJS.Timeout | null = null;
  private readonly DEBOUNCE_MS = 2000;

  onFileEvent(event: FileEvent) {
    this.pendingFiles.add(event.path);

    // 重置计时器
    if (this.timer) clearTimeout(this.timer);
    this.timer = setTimeout(() => {
      this.processBatch([...this.pendingFiles]);
      this.pendingFiles.clear();
    }, this.DEBOUNCE_MS);
  }
}
```

### 3.3 防抖的权衡

| 防抖时间 | 优点 | 缺点 |
|----------|------|------|
| 0s (无防抖) | 最快同步 | CPU 浪费，频繁 I/O |
| 1s | 较快同步 | 可能仍有多余更新 |
| **2s (CodeGraph)** | 平衡点 | 修改后需等待 2s |
| 5s | 最少更新 | 同步延迟感明显 |

## 四、增量更新流程

### 4.1 完整流程

```
防抖触发 → 收集待处理文件列表
    │
    ▼
┌──────────────────────────────────────┐
│  对每个变更文件:                       │
│                                      │
│  1. 检查变更类型                      │
│     ├── CREATE → goto step 3         │
│     ├── MODIFY → goto step 2         │
│     └── DELETE → goto step 5         │
│                                      │
│  2. 删除旧数据                        │
│     DELETE FROM nodes WHERE file = ?  │
│     DELETE FROM edges WHERE file = ?  │
│                                      │
│  3. Tree-sitter 重新解析              │
│     parser.parse(newContent)         │
│                                      │
│  4. 提取并写入新数据                  │
│     INSERT INTO nodes ...            │
│     INSERT INTO edges ...            │
│     UPDATE files SET hash = ...      │
│                                      │
│  5. (仅 DELETE) 清除数据              │
│     DELETE FROM nodes WHERE file = ?  │
│     DELETE FROM edges WHERE file = ?  │
│     DELETE FROM files WHERE path = ?  │
│                                      │
└──────────────────────────────────────┘
    │
    ▼
Resolution 阶段 (重新解析受影响的边)
    │
    ├── 删除的节点 → 标记引用它的边为 unresolved
    ├── 新增的节点 → 尝试解析之前 unresolved 的边
    └── 修改的节点 → 检查签名变化，更新引用
    │
    ▼
FTS5 索引更新
    │
    ▼
完成 (MCP 查询立即反映新状态)
```

### 4.2 事务保证

```sql
BEGIN TRANSACTION;
  -- 删除旧记录
  DELETE FROM nodes WHERE file = 'src/auth.ts';
  DELETE FROM edges WHERE file = 'src/auth.ts';

  -- 插入新记录
  INSERT INTO nodes VALUES (...);
  INSERT INTO nodes VALUES (...);
  INSERT INTO edges VALUES (...);

  -- 更新文件元数据
  UPDATE files SET last_modified = ?, hash = ? WHERE path = 'src/auth.ts';
COMMIT;
```

事务保证了：
- 原子性: 要么完全更新，要么不更新
- 一致性: 不会出现"旧节点已删、新节点未入"的中间状态
- WAL 模式: 事务提交期间，读查询不受影响

### 4.3 Resolution 重运行

当文件 A 被修改时，不仅 A 的节点/边需要更新，**其他文件中引用 A 中节点的边**也需要重新解析：

```
文件 A 修改 (函数 foo 重命名为 bar)
    │
    ├── A 的旧节点 "foo" 被删除
    ├── A 的新节点 "bar" 被创建
    │
    └── 其他文件中的边:
        ├── "文件B:10 calls foo" → 变为 unresolved ⚠️
        └── "文件C:5 calls bar" → 如果存在，可能被 resolved ✅
```

## 五、性能优化

### 5.1 文件哈希跳过

```typescript
// 如果文件内容未变（只是 touch/save without change），跳过重建
const currentHash = computeHash(fileContent);
const storedHash = db.getFileHash(filePath);
if (currentHash === storedHash) {
  return; // 跳过，内容未变
}
```

### 5.2 批量操作

```sql
-- 批量删除（一次性删除多个文件的数据）
DELETE FROM nodes WHERE file IN ('a.ts', 'b.ts', 'c.ts');
DELETE FROM edges WHERE file IN ('a.ts', 'b.ts', 'c.ts');

-- 批量插入
INSERT INTO nodes VALUES
  (...), (...), (...), ...;
```

### 5.3 并行解析

```
变更文件列表: [a.ts, b.ts, c.py, d.go]
    │
    ├── Worker 1: parse(a.ts) + parse(b.ts)  ← 同语言并行
    ├── Worker 2: parse(c.py)
    └── Worker 3: parse(d.go)
    │
    ▼
合并结果 → 批量写入 DB (串行，因为 SQLite 单写者)
```

### 5.4 增量 Resolution

```
不需要对全图重新 resolve。只需要:
1. 找到引用被修改节点的边
2. 只对这些边重新 resolve
```

## 六、边界情况处理

### 6.1 大规模重命名 (rename refactoring)

```
场景: IDE 重命名变量，修改了 20 个文件
    │
    ▼
20 个文件变更事件
    │ 2s 防抖
    ▼
合并为一次批量更新
    │ 20 个文件的节点/边一起更新
    ▼
Resolution 一次性运行
    │ 所有新引用在同一批中解析
    ▼
一致的最终状态
```

### 6.2 Git checkout / branch switch

```
场景: git checkout another-branch (大量文件变更)
    │
    ▼
可能上百个文件变更事件
    │ 2s 防抖
    ▼
大批量更新 (可能接近全量重建)
    │
    ▼
性能考量: 如果变更文件 > 50%，可能直接全量重建
```

### 6.3 文件保存 + 格式化

```
场景: 保存 → prettier 格式化 → 保存
    │
    ├── t=0:    保存原始修改
    ├── t=200:  prettier 修改同文件
    │           (2s 防抖期间)
    │
    └── t=2200: 只处理一次（最终状态）
```

### 6.4 高频文件变更 (watch mode build)

```
场景: webpack --watch，每次保存生成新 bundle
    │
    ▼
dist/ 目录变更 → 被忽略 (在 ignore 列表中) ✅
src/ 目录变更 → 正常处理
```

## 七、与首次索引对比

| 维度 | 首次索引 | 增量同步 |
|------|----------|----------|
| 触发条件 | 首次运行 / 手动重建 | 文件变更事件 |
| 范围 | 全部文件 | 仅变更文件 |
| 耗时 | 秒~分钟 (取决于项目大小) | 毫秒~秒 |
| Resolution | 全图解析 | 仅受影响的边 |
| 阻塞 | 索引完成前查询结果不完整 | WAL 模式下不阻塞查询 |
| CPU 使用 | 高 (全核) | 低 (单文件解析) |

## 八、监控和诊断

### 8.1 codegraph_status 输出

```json
{
  "indexed_files": 342,
  "total_nodes": 2847,
  "total_edges": 11293,
  "resolved_edges": 10856,
  "unresolved_edges": 437,
  "last_sync": "2026-05-24T10:32:15Z",
  "watching": true,
  "pending_events": 0,
  "languages": ["typescript", "python", "go"],
  "frameworks": ["express", "fastapi"]
}
```

### 8.2 常见问题排查

| 症状 | 可能原因 | 解决方案 |
|------|----------|----------|
| 修改后查询未更新 | 防抖期间 (等2s) | 等待 2s 后重试 |
| 某文件始终未索引 | 在 ignore 列表中 | 检查 .gitignore |
| unresolved_edges 很多 | 外部依赖调用 | 正常现象 (node_modules 中的代码不索引) |
| CPU 持续高 | 大量文件同时变更 | 等待处理完成 |
| watching = false | 文件监听器崩溃 | 重启 MCP Server |

## 九、与其他同步方案对比

| 方案 | 触发机制 | 延迟 | CPU 占用 | 准确性 |
|------|----------|------|----------|--------|
| **CodeGraph Auto-Sync** | OS 原生事件 + 2s 防抖 | 2s | 低 | 高 |
| 轮询 (polling) | 定期扫描文件时间戳 | 可配置 | 中-高 | 中 |
| Git hooks | commit/push 时触发 | commit 时 | 低 | 中 (延迟大) |
| 手动重建 | 用户命令触发 | 用户决定 | 按需 | 最高 |
| IDE 实时 | 编辑器事件 | 即时 | 高 | 最高 |

## 十、待深入研究

- [ ] 超大型 monorepo (>100k 文件) 的文件监听性能
- [ ] inotify watch limit 耗尽时的降级策略
- [ ] 防抖时间是否可配置？
- [ ] 网络文件系统 (NFS) 上的行为
- [ ] 多个 CodeGraph 实例同时监听同一目录的行为
- [ ] Docker/容器环境中文件监听的兼容性

---

*前置阅读: [04-framework-routes.md](./04-framework-routes.md)*
*总览: [architecture.md](../architecture.md)*
*最后更新: 2026-05-24*
