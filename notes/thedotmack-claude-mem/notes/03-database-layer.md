# Database Layer 学习笔记

> claude-mem 的存储层 — SQLite + FTS5 全文搜索 + 可选 ChromaDB 语义搜索。

## 概述

claude-mem 使用 SQLite 作为主存储，配合 FTS5 全文搜索引擎。可选集成 ChromaDB 提供向量语义搜索能力。

## SQLite 主存储

### Schema 设计

```sql
-- 主记忆表
CREATE TABLE memories (
  id TEXT PRIMARY KEY,
  content TEXT NOT NULL,           -- 原始内容
  compressed_content TEXT,         -- AI 压缩后内容
  session_id TEXT NOT NULL,        -- 来源 Session
  timestamp INTEGER NOT NULL,      -- Unix 时间戳
  importance REAL DEFAULT 0.5,     -- 重要性 (0-1)
  tags TEXT,                       -- JSON array 标签
  source_type TEXT,                -- observation/tool_call/decision
  metadata TEXT                    -- JSON 扩展字段
);

-- 全文搜索虚拟表
CREATE VIRTUAL TABLE memories_fts USING fts5(
  content,
  compressed_content,
  tags,
  content=memories,
  content_rowid=rowid
);

-- Session 索引表
CREATE TABLE sessions (
  id TEXT PRIMARY KEY,
  start_time INTEGER,
  end_time INTEGER,
  working_dir TEXT,
  memory_count INTEGER DEFAULT 0
);
```

### 为什么选 SQLite

| 维度 | SQLite | PostgreSQL | MongoDB |
|------|--------|-----------|---------|
| 部署 | 零配置 (单文件) | 需要服务器 | 需要服务器 |
| 性能 | 本地极快 | 网络延迟 | 网络延迟 |
| 备份 | 复制文件 | pg_dump | mongodump |
| 事务 | ACID | ACID | 部分 |
| FTS | FTS5 内置 | 需要扩展 | 内置 |
| 适用 | 单用户/嵌入 | 多用户/生产 | 文档型 |

## FTS5 全文搜索

### 搜索能力

| 搜索类型 | 语法 | 示例 |
|---------|------|------|
| 精确匹配 | "关键词" | "API 认证" |
| 前缀匹配 | 词* | auth* (匹配 authentication, authorize) |
| AND | 词1 词2 | redis caching |
| OR | 词1 OR 词2 | redis OR memcached |
| NOT | NOT 词 | database NOT mysql |
| 近邻 | NEAR(词1 词2) | NEAR(api token) |

### FTS5 查询示例

```sql
-- 搜索相关记忆
SELECT m.*, rank
FROM memories_fts 
WHERE memories_fts MATCH 'api authentication token'
ORDER BY rank
LIMIT 5;

-- 最近相关
SELECT * FROM memories 
WHERE id IN (
  SELECT rowid FROM memories_fts 
  WHERE memories_fts MATCH 'deployment'
)
ORDER BY timestamp DESC
LIMIT 3;
```

## ChromaDB 语义搜索 (可选)

### 什么是语义搜索

```
FTS5 (关键词):
  查询 "用户登录" → 必须包含"用户"或"登录"
  无法匹配 "authentication flow" (同义但不同词)

ChromaDB (语义):
  查询 "用户登录" → embedding → 余弦相似度
  可以匹配 "authentication flow" (语义相近)
```

### 集成架构

```
新记忆存入:
  内容 → SQLite (精确存储)
  内容 → embedding → ChromaDB (语义索引)

搜索时:
  FTS5 结果 (关键词匹配) ──┐
                           ├── 合并排序 → 最终结果
  ChromaDB 结果 (语义匹配) ──┘
```

### 何时需要 ChromaDB

| 场景 | FTS5 够用? | 需要 ChromaDB? |
|------|-----------|---------------|
| 精确关键词搜索 | ✅ | ❌ |
| 同义词/近义词 | ❌ | ✅ |
| 概念相关搜索 | ❌ | ✅ |
| 多语言混合 | 部分 | ✅ |
| 简单项目 | ✅ | ❌ |
| 大型知识库 | 部分 | ✅ |

## 数据生命周期

```
观察产生 → 临时存储 (内存)
    │
    ▼
压缩完成 → 持久化 (SQLite + FTS5 + ChromaDB)
    │
    ▼
时间流逝 → 重要性衰减
    │
    ▼
过期/不重要 → 归档或删除

时间衰减公式:
  effective_importance = importance × decay(time_elapsed)
  decay(t) = e^(-λt)  (λ = 衰减系数)
```

## 问题与思考

- [x] 为什么 SQLite? → 零配置，嵌入式，性能够用
- [x] FTS5 vs ChromaDB? → 关键词 vs 语义，互补
- [ ] 数据库大小增长的管理?
- [ ] 多 Agent 同时写入的锁竞争?
- [ ] 衰减系数 λ 的最优值?
- [ ] 备份和恢复策略?

---

*参考: [architecture.md](../architecture.md) | [04-context-injection.md](./04-context-injection.md)*
