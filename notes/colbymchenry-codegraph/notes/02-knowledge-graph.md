# 知识图谱存储

> SQLite + FTS5 — 代码知识图谱的持久化层。

## 一、概述

CodeGraph 使用 SQLite 数据库作为知识图谱的持久化存储，配合 FTS5 扩展提供全文搜索能力。数据库文件位于项目根目录的 `.codegraph/codegraph.db`。

### 设计选择

| 选择 | 原因 |
|------|------|
| SQLite (非 PostgreSQL/MongoDB) | 本地优先、零配置、无需外部服务 |
| WAL 模式 (非默认 journal) | 读写并发、消除 "database is locked" |
| FTS5 (非外部搜索引擎) | 内置于 SQLite、无额外依赖 |
| 自带 Node.js (非系统 Node) | 避免 native 编译失败 |

## 二、数据库架构

### 2.1 物理结构

```
.codegraph/
└── codegraph.db          # SQLite 数据库文件 (WAL 模式)
    ├── codegraph.db-wal  # WAL 日志文件 (运行时)
    └── codegraph.db-shm  # 共享内存文件 (运行时)
```

### 2.2 逻辑模型

```
┌─────────────────────────────────────────────────────┐
│                  Knowledge Graph                     │
│                                                     │
│  ┌─────────────┐       edges        ┌───────────┐ │
│  │   Nodes     │ ──────────────────▶ │   Nodes   │ │
│  │  (源节点)    │ ◀────────────────── │  (目标)   │ │
│  └─────────────┘                     └───────────┘ │
│        │                                     │      │
│        │ belongs_to                 belongs_to│      │
│        ▼                                     ▼      │
│  ┌─────────────────────────────────────────────┐   │
│  │              Files (文件元数据)               │   │
│  └─────────────────────────────────────────────┘   │
│                                                     │
│  ┌─────────────────────────────────────────────┐   │
│  │          FTS5 Index (全文搜索索引)           │   │
│  └─────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────┘
```

### 2.3 表结构（推测）

#### nodes 表

```sql
CREATE TABLE nodes (
    id          TEXT PRIMARY KEY,    -- 唯一标识 (通常: file:line:name)
    name        TEXT NOT NULL,       -- 节点名称
    kind        TEXT NOT NULL,       -- function/class/method/interface/module
    file        TEXT NOT NULL,       -- 文件路径 (相对项目根)
    line        INTEGER NOT NULL,    -- 起始行号
    end_line    INTEGER,             -- 结束行号
    language    TEXT NOT NULL,       -- 语言标识
    signature   TEXT,                -- 函数签名 / 类签名
    body        TEXT,                -- 代码体 (可选，用于上下文查询)
    exported    BOOLEAN DEFAULT 0    -- 是否导出
);
```

#### edges 表

```sql
CREATE TABLE edges (
    id          INTEGER PRIMARY KEY AUTOINCREMENT,
    source_id   TEXT NOT NULL,       -- 源节点 ID
    target_id   TEXT,                -- 目标节点 ID (Resolution 后填充)
    target_name TEXT NOT NULL,       -- 目标名称 (提取时的原始名称)
    kind        TEXT NOT NULL,       -- calls/imports/extends/implements
    file        TEXT NOT NULL,       -- 边所在文件
    line        INTEGER NOT NULL,    -- 边所在行号
    resolved    BOOLEAN DEFAULT 0,   -- 是否已解析
    FOREIGN KEY (source_id) REFERENCES nodes(id),
    FOREIGN KEY (target_id) REFERENCES nodes(id)
);
```

#### files 表

```sql
CREATE TABLE files (
    path        TEXT PRIMARY KEY,    -- 文件路径
    language    TEXT NOT NULL,       -- 语言
    last_modified INTEGER,           -- 最后修改时间戳
    hash        TEXT,                -- 文件内容哈希 (用于检测变更)
    node_count  INTEGER DEFAULT 0,   -- 节点数量
    edge_count  INTEGER DEFAULT 0    -- 边数量
);
```

### 2.4 FTS5 全文搜索索引

```sql
-- 为节点名称和签名创建 FTS5 虚拟表
CREATE VIRTUAL TABLE nodes_fts USING fts5(
    name,           -- 节点名称 (函数名/类名)
    signature,      -- 签名
    file,           -- 文件路径
    content=nodes,  -- 关联到 nodes 表
    content_rowid=rowid
);
```

#### FTS5 查询能力

| 查询类型 | 语法 | 示例 |
|----------|------|------|
| 精确匹配 | `"term"` | `"handleRequest"` |
| 前缀匹配 | `term*` | `handle*` |
| 短语匹配 | `"word1 word2"` | `"parse body"` |
| 布尔组合 | `term1 AND term2` | `request AND handler` |
| 否定 | `NOT term` | `handler NOT test` |
| 列限定 | `column:term` | `name:handleRequest` |

## 三、节点和边模型详解

### 3.1 节点类型 (Node Kinds)

| Kind | 说明 | 语言示例 |
|------|------|----------|
| `function` | 顶级函数 | `function foo()` / `def foo():` / `func foo()` |
| `method` | 类/结构体方法 | `class.method()` / `impl.method()` |
| `class` | 类定义 | `class Foo` / `struct Foo` |
| `interface` | 接口/协议 | `interface IFoo` / `protocol Foo` |
| `module` | 模块/包 | 文件级模块 |
| `type` | 类型别名 | `type Foo = ...` |
| `variable` | 导出变量/常量 | `export const X` (可选) |
| `route` | 框架路由 | `@app.route('/users')` |

### 3.2 边类型 (Edge Kinds)

| Kind | 含义 | 示例 |
|------|------|------|
| `calls` | 函数调用 | `foo()` → 调用了 foo |
| `imports` | 导入关系 | `import { X } from './m'` |
| `extends` | 类继承 | `class B extends A` |
| `implements` | 接口实现 | `class C implements I` |
| `uses_type` | 类型引用 | 参数/返回值中的类型 |
| `routes_to` | 路由映射 | URL → 处理函数 |

### 3.3 图关系示例

```
                         imports
  ┌────────────┐ ──────────────────▶ ┌────────────┐
  │ server.ts  │                     │ handler.ts │
  │ (module)   │                     │ (module)   │
  └────────────┘                     └────────────┘
       │ contains                         │ contains
       ▼                                  ▼
  ┌────────────┐        calls       ┌─────────────────┐
  │   main()   │ ─────────────────▶ │ handleRequest() │
  │ (function) │                    │   (function)     │
  └────────────┘                    └─────────────────┘
                                          │ calls
                                          ▼
                                    ┌─────────────────┐
                                    │   parseBody()   │
                                    │   (function)    │
                                    └─────────────────┘
```

## 四、WAL 模式详解

### 4.1 什么是 WAL？

WAL (Write-Ahead Logging) 是 SQLite 的一种日志模式：

```
传统 Rollback Journal 模式:
  写操作 → 锁定整个数据库 → 读操作阻塞

WAL 模式:
  写操作 → 写入 WAL 文件 → 读操作不阻塞
  定期 → WAL checkpoint → 合并到主数据库
```

### 4.2 为什么 CodeGraph 选择 WAL？

| 场景 | 无 WAL (v0.9.0 之前) | 有 WAL (v0.9.0+) |
|------|---------------------|-------------------|
| Auto-Sync 写入时 MCP 查询 | ❌ "database is locked" | ✅ 正常查询 |
| 大量文件变更 | ❌ 长时间锁定 | ✅ 读写并发 |
| 首次索引中途查询 | ❌ 等待索引完成 | ✅ 可查询已索引部分 |
| 多个 MCP 客户端同时查询 | ❌ 互相阻塞 | ✅ 多读者并发 |

### 4.3 WAL 的限制

| 限制 | 影响 | CodeGraph 的处理 |
|------|------|------------------|
| 单写者 | 同时只能有一个写入事务 | Auto-Sync 串行写入，2s 防抖合并 |
| -wal 文件增长 | 大量写入时 WAL 文件可能很大 | 定期 checkpoint |
| 不支持网络文件系统 | NFS/SMB 上不可用 | 明确为本地存储设计 |

## 五、查询模式

### 5.1 搜索查询 (codegraph_search)

```sql
-- FTS5 搜索
SELECT n.id, n.name, n.kind, n.file, n.line, n.signature
FROM nodes_fts fts
JOIN nodes n ON n.rowid = fts.rowid
WHERE nodes_fts MATCH ?
ORDER BY rank
LIMIT 20;
```

### 5.2 调用者查询 (codegraph_callers)

```sql
-- 谁调用了 target_id?
SELECT n.id, n.name, n.kind, n.file, n.line, e.line as call_line
FROM edges e
JOIN nodes n ON n.id = e.source_id
WHERE e.target_id = ?
  AND e.kind = 'calls';
```

### 5.3 被调用者查询 (codegraph_callees)

```sql
-- target_id 调用了谁?
SELECT n.id, n.name, n.kind, n.file, n.line, e.line as call_line
FROM edges e
JOIN nodes n ON n.id = e.target_id
WHERE e.source_id = ?
  AND e.kind = 'calls';
```

### 5.4 影响分析 (codegraph_impact)

```sql
-- 递归查找所有调用者 (影响链)
WITH RECURSIVE impact_chain AS (
    -- 基础: 直接调用者
    SELECT source_id, 1 as depth
    FROM edges
    WHERE target_id = ? AND kind = 'calls'

    UNION ALL

    -- 递归: 调用者的调用者
    SELECT e.source_id, ic.depth + 1
    FROM edges e
    JOIN impact_chain ic ON e.target_id = ic.source_id
    WHERE e.kind = 'calls'
      AND ic.depth < 5  -- 深度限制
)
SELECT DISTINCT n.id, n.name, n.kind, n.file, n.line, ic.depth
FROM impact_chain ic
JOIN nodes n ON n.id = ic.source_id
ORDER BY ic.depth;
```

### 5.5 调用链追踪 (codegraph_trace)

```sql
-- 从入口到目标的完整调用链
WITH RECURSIVE call_trace AS (
    SELECT id, name, 0 as depth, CAST(name AS TEXT) as path
    FROM nodes
    WHERE id = ?  -- 起始节点

    UNION ALL

    SELECT n.id, n.name, ct.depth + 1, ct.path || ' → ' || n.name
    FROM call_trace ct
    JOIN edges e ON e.source_id = ct.id
    JOIN nodes n ON n.id = e.target_id
    WHERE e.kind = 'calls'
      AND ct.depth < 10
)
SELECT * FROM call_trace
WHERE name = ?;  -- 目标节点名
```

## 六、索引统计

### 6.1 典型项目的图规模

| 项目 | 语言 | 估计节点数 | 估计边数 | DB 大小 |
|------|------|-----------|---------|---------|
| 小型 (1k LOC) | TypeScript | ~50 | ~100 | < 1MB |
| 中型 (10k LOC) | Python | ~500 | ~2,000 | ~5MB |
| 大型 (100k LOC) | 混合 | ~5,000 | ~20,000 | ~50MB |
| 巨型 (1M+ LOC, VS Code) | TypeScript | ~50,000+ | ~200,000+ | ~500MB+ |

### 6.2 查询性能

| 操作 | 典型耗时 | 说明 |
|------|----------|------|
| FTS5 搜索 | < 5ms | 全文索引加速 |
| 单节点查询 (by ID) | < 1ms | 主键查询 |
| 调用者/被调用者 | < 10ms | 索引列查询 |
| 影响分析 (depth=5) | < 50ms | 递归 CTE |
| 调用链追踪 | < 100ms | 图遍历 |

## 七、数据一致性

### 7.1 增量更新的一致性保证

```
文件 A 修改
    │
    ▼
事务开始 (BEGIN TRANSACTION)
    │
    ├── DELETE FROM nodes WHERE file = 'A'
    ├── DELETE FROM edges WHERE file = 'A'
    ├── 重新解析文件 A
    ├── INSERT 新节点
    ├── INSERT 新边
    └── UPDATE files SET last_modified = ...
    │
    ▼
事务提交 (COMMIT)
    │
    ▼
Resolution 重新运行
    │ (针对引用文件 A 中节点的边)
    ▼
完成
```

### 7.2 断电/崩溃恢复

- WAL 模式自动保证事务的原子性
- 崩溃后重启，SQLite 自动回放 WAL
- 最坏情况: 丢失最近 2s 内的增量更新（下次文件变更会重新触发）

## 八、与其他存储方案对比

| 方案 | 优势 | 劣势 | 适用场景 |
|------|------|------|----------|
| SQLite + FTS5 (CodeGraph) | 零配置、本地优先、快速 | 单机限制 | 个人开发 |
| 内存图 (Graphify) | 极快查询 | 不持久、内存占用大 | 小项目/临时分析 |
| Neo4j | 原生图查询、可视化 | 重量级、需要服务 | 团队/企业 |
| PostgreSQL + pg_trgm | 可共享、ACID | 需要 PostgreSQL 服务 | 团队协作 |
| 向量数据库 | 语义搜索 | 不精确、无图结构 | 模糊搜索 |

## 九、待深入研究

- [ ] 实际的 schema migration 策略（版本升级时如何处理旧数据库？）
- [ ] FTS5 分词器配置（是否支持 camelCase 分词？）
- [ ] 大型 monorepo 的 DB 大小和性能基准
- [ ] 是否有 VACUUM 或 DB 维护策略？
- [ ] edges 表的索引设计（哪些列有索引？）

---

*前置阅读: [01-tree-sitter-pipeline.md](./01-tree-sitter-pipeline.md)*
*后续阅读: [03-mcp-tools.md](./03-mcp-tools.md)*
*最后更新: 2026-05-24*
