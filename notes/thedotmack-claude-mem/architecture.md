# claude-mem 架构总览

> 基于 GitHub 仓库 (thedotmack/claude-mem) 和公开文档整理。最后更新: 2026-07

## 零、项目基本信息

| 属性 | 值 |
|------|-----|
| 仓库 | https://github.com/thedotmack/claude-mem |
| 作者 | Alex Newman (@thedotmack) |
| Stars | 72K+ |
| 语言 | TypeScript (Bun runtime) |
| 文档 | https://docs.claude-mem.ai/ |
| 支持 Agent | 7+ (Claude Code, OpenClaw, Codex, Gemini, Hermes, Copilot, OpenCode) |

## 一、整体架构

### 四层架构

```
┌─────────────────────────────────────────────────────────────┐
│  Layer 1: Hook System (事件捕获层)                           │
│  5 阶段: PreSession → ToolCall → Observation                │
│         → Compression → PostSession                         │
│  功能: 捕获 Session 生命周期中的关键事件                      │
└───────────────────────────────┬─────────────────────────────┘
                                │ 事件流
                                ▼
┌─────────────────────────────────────────────────────────────┐
│  Layer 2: Worker Service (后台处理层)                         │
│  技术: Claude Agent SDK + HTTP API                           │
│  功能: 异步压缩观察、10 个搜索端点                            │
│  运行: Bun runtime (从 PM2 迁移)                            │
└───────────────────────────────┬─────────────────────────────┘
                                │ 读写
                                ▼
┌─────────────────────────────────────────────────────────────┐
│  Layer 3: Database Layer (存储层)                             │
│  必选: SQLite + FTS5 (全文搜索)                              │
│  可选: ChromaDB (向量语义搜索)                                │
│  功能: 持久化记忆、高效检索                                   │
└───────────────────────────────┬─────────────────────────────┘
                                │ 搜索接口
                                ▼
┌─────────────────────────────────────────────────────────────┐
│  Layer 4: mem-search Skill (查询层)                          │
│  模式: Skill 式接口 (渐进式披露)                             │
│  功能: 为 Agent 提供记忆搜索能力                              │
└─────────────────────────────────────────────────────────────┘
```

## 二、5 阶段 Hook 生命周期

### 完整流程

```
Session 开始
    │
    ▼
┌─────────────────┐
│ 1. PreSession   │  搜索相关记忆 → 注入上下文
│    (会话前)      │  Token 预算控制
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ 2. ToolCall     │  每次工具调用 → 记录使用情况
│    (工具调用)    │  文件读写 / 命令执行 / 搜索
└────────┬────────┘
         │ (多次循环)
         ▼
┌─────────────────┐
│ 3. Observation  │  重要发现标记 → 候选记忆点
│    (观察)       │  新发现 / 决策 / 问题解决
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ 4. Compression  │  累积观察 → AI 压缩为精华
│    (压缩)       │  Claude Agent SDK 驱动
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ 5. PostSession  │  压缩知识 → 持久化到 DB
│    (会话后)      │  更新索引 / 清理临时数据
└─────────────────┘
```

## 三、Worker Service

### 后台压缩架构

```
观察事件流入
    │
    ▼
┌────────────────────────────────────────┐
│          Worker Service                 │
│                                        │
│  ┌──────────────┐  ┌──────────────┐   │
│  │ 压缩引擎     │  │ HTTP API     │   │
│  │ Claude SDK   │  │ 10 端点      │   │
│  └──────────────┘  └──────────────┘   │
│                                        │
│  运行时: Bun (高性能 JS runtime)       │
└────────────────────────────────────────┘
```

### HTTP API 端点 (10 个)

| 端点 | 功能 |
|------|------|
| /search | 通用搜索 |
| /search/recent | 最近的记忆 |
| /search/relevant | 相关性搜索 |
| /search/semantic | 语义搜索 (ChromaDB) |
| /memories | 记忆 CRUD |
| /compress | 触发压缩 |
| /status | Worker 状态 |
| /health | 健康检查 |
| /stats | 统计信息 |
| /config | 配置管理 |

## 四、Database Layer

### SQLite + FTS5

```
SQLite:
  memories 表:
    id, content, compressed_content, 
    session_id, timestamp, tags, importance

FTS5 虚拟表:
    memories_fts (content, compressed_content, tags)
    支持: 全文搜索 / 前缀匹配 / 近义搜索
```

### ChromaDB (可选)

```
向量语义搜索:
  记忆文本 → embedding → 存入 ChromaDB
  查询文本 → embedding → 余弦相似度搜索

优势: 语义理解 (不依赖关键词精确匹配)
代价: 额外依赖 + embedding 计算
```

## 五、关键架构决策

### PM2 → Bun 迁移

| 维度 | PM2 (旧) | Bun (新) |
|------|---------|---------|
| 启动速度 | 慢 | 极快 |
| 内存 | 高 (Node.js) | 低 |
| 包管理 | npm | 内置 |
| TypeScript | 需编译 | 原生支持 |
| 稳定性 | 成熟 | 新但快速稳定 |

### Token 预算管理

```
PreSession 注入时的 Token 控制:

可用预算 = Agent 上下文窗口 × 注入比例 (如 10%)
记忆排序 = 相关性 × 重要性 × 时间衰减
逐条注入 = 直到达到 Token 预算

示例:
  上下文窗口: 200K tokens
  注入预算: 20K tokens (10%)
  记忆 1 (5K tokens): 注入 ✅ (累计 5K)
  记忆 2 (8K tokens): 注入 ✅ (累计 13K)
  记忆 3 (6K tokens): 注入 ✅ (累计 19K)
  记忆 4 (3K tokens): 跳过 ❌ (超出 20K)
```

### Subagent 上下文注入控制 (Issue #1464)

```
问题: Subagent 是否应该获得记忆注入?
  - 注入: Subagent 更有上下文 (但消耗 token)
  - 不注入: Subagent 轻量 (但可能缺少必要背景)

解决:
  配置项控制: 
    inject_to_subagents: true/false
    subagent_budget: (独立的 token 预算)
```

## 六、待深入研究

- [ ] 压缩算法的具体 prompt 设计
- [ ] FTS5 vs ChromaDB 的检索质量对比
- [ ] Token 预算的最优比例
- [ ] 记忆遗忘策略（时间衰减曲线）
- [ ] 多 Agent 同时写入的冲突处理
- [ ] Bun 在高并发下的稳定性

---

*参考来源见 [references.md](./references.md)*
*最后更新: 2026-07*
