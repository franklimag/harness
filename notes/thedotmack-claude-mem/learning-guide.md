# claude-mem 学习计划指南

> 系统化学习路径，从理解 Hook 生命周期到掌握记忆管理，按依赖关系编排。

## 笔记依赖关系图

```
architecture.md (总览，入口)
        │
        ├── notes/01-hook-lifecycle.md (事件捕获，基础)
        │       │
        │       └── notes/02-worker-service.md (压缩处理，依赖 Hook)
        │               │
        │               └── notes/03-database-layer.md (存储，依赖 Worker)
        │
        ├── notes/04-context-injection.md (注入，综合)
        │
        └── notes/05-cross-agent-support.md (多平台，独立)
```

## 推荐阅读顺序

| 顺序 | 文件 | 原因 |
|------|------|------|
| 1 | `architecture.md` | 四层架构全景 |
| 2 | `notes/01-hook-lifecycle.md` | 数据入口，理解来源 |
| 3 | `notes/02-worker-service.md` | 数据处理，理解压缩 |
| 4 | `notes/03-database-layer.md` | 数据存储，理解检索 |
| 5 | `notes/04-context-injection.md` | 数据应用，理解注入 |
| 6 | `notes/05-cross-agent-support.md` | 多平台扩展 |

---

## 阶段一: Hook 生命周期（Day 1）

| 任务 | 完成标准 |
|------|----------|
| 5 阶段 | 能列出并解释每个阶段 |
| 事件流 | 理解数据如何流过系统 |
| 工具调用捕获 | 理解什么被记录 |

---

## 阶段二: Worker 与压缩（Day 2）

| 任务 | 完成标准 |
|------|----------|
| Claude Agent SDK | 理解 AI 压缩机制 |
| HTTP API | 知道 10 个端点 |
| Bun runtime | 理解迁移原因 |

---

## 阶段三: 数据库设计（Day 3）

| 任务 | 完成标准 |
|------|----------|
| SQLite schema | 理解表结构 |
| FTS5 | 理解全文搜索 |
| ChromaDB | 理解语义搜索 |

---

## 阶段四: 上下文注入（Day 4）

| 任务 | 完成标准 |
|------|----------|
| Token 预算 | 理解注入控制 |
| 相关性排序 | 理解记忆选择 |
| Subagent 控制 | 理解 #1464 |

---

## 阶段五: 多 Agent 支持（Day 5）

| 任务 | 完成标准 |
|------|----------|
| 7+ Agent | 知道支持范围 |
| 适配方式 | 理解 Hook 集成 |
| 架构演进 | PM2 → Bun |

---

## 难度与时间预估

| 阶段 | 难度 | 时间 |
|------|------|------|
| 1 Hook | ⭐⭐ | 2h |
| 2 Worker | ⭐⭐⭐ | 3h |
| 3 Database | ⭐⭐ | 2h |
| 4 Injection | ⭐⭐⭐ | 3h |
| 5 Multi-Agent | ⭐⭐ | 2h |

**总计**: 约 12 小时

---

*最后更新: 2026-07*
