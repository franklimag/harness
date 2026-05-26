# Worker Service 学习笔记

> claude-mem 的处理层 — 后台压缩 Worker、Claude Agent SDK 和 HTTP API。

## 概述

Worker Service 是 claude-mem 的核心处理引擎，运行在 Bun runtime 上，负责异步压缩观察数据并提供 10 个搜索 API 端点。

### Worker 架构

```
Hook System (事件流入)
    │
    ▼
┌────────────────────────────────────────────────────┐
│             Worker Service (Bun)                    │
│                                                    │
│  ┌──────────────────┐  ┌──────────────────────┐   │
│  │  Compression     │  │    HTTP API Server   │   │
│  │  Engine          │  │                      │   │
│  │                  │  │  /search             │   │
│  │  Claude Agent    │  │  /search/recent      │   │
│  │  SDK 驱动        │  │  /search/relevant    │   │
│  │                  │  │  /search/semantic    │   │
│  │  观察 → 精华     │  │  /memories           │   │
│  └──────────────────┘  │  /compress           │   │
│                         │  /status             │   │
│                         │  /health             │   │
│                         │  /stats              │   │
│                         │  /config             │   │
│                         └──────────────────────┘   │
└────────────────────────────────────────────────────┘
```

## 压缩引擎

### Claude Agent SDK 集成

```
压缩流程:

1. 收集: 累积 10+ 条观察 或 2000+ tokens
2. 构建 prompt: "将以下观察压缩为简洁的知识要点"
3. 调用 Claude Agent SDK
4. 获取压缩结果
5. 评估质量 (保留关键信息?)
6. 存储压缩后的记忆

关键: 用 AI 做 AI 的记忆压缩 (meta-cognition)
```

### 压缩质量要求

| 要求 | 说明 |
|------|------|
| 信息保真 | 关键事实不能丢失 |
| 去冗余 | 重复信息合并 |
| 结构化 | 输出有组织 |
| 可检索 | 保留关键词用于 FTS5 |
| 紧凑 | 压缩比至少 3:1 |

## HTTP API (10 端点)

### 搜索端点

| 端点 | 参数 | 返回 | 场景 |
|------|------|------|------|
| GET /search?q= | 查询文本 | 匹配记忆列表 | 通用搜索 |
| GET /search/recent?n= | 数量 | 最近 N 条 | 回顾历史 |
| GET /search/relevant?ctx= | 当前上下文 | 相关记忆 | PreSession |
| GET /search/semantic?q= | 语义查询 | 语义匹配 | ChromaDB |

### 管理端点

| 端点 | 方法 | 功能 |
|------|------|------|
| /memories | GET/POST/DELETE | 记忆 CRUD |
| /compress | POST | 手动触发压缩 |
| /status | GET | Worker 状态 |
| /health | GET | 健康检查 |
| /stats | GET | 统计 (记忆数/压缩率) |
| /config | GET/PUT | 运行时配置 |

## Bun Runtime 选择

### 为什么从 PM2 迁移到 Bun

```
PM2 方案:
  Node.js → TypeScript 编译 → JavaScript → PM2 管理
  问题: 启动慢，内存大，依赖多

Bun 方案:
  TypeScript → Bun 直接运行 (无编译)
  优势: 启动快 (ms 级)，内存小，内置打包/测试
```

| 维度 | PM2 + Node.js | Bun |
|------|-------------|-----|
| 启动时间 | ~2s | ~50ms |
| 内存占用 | ~150MB | ~50MB |
| TypeScript | 需要编译 | 原生 |
| HTTP Server | Express/Fastify | Bun.serve |
| 包安装 | npm (慢) | bun install (快) |

## 渐进式披露搜索 (mem-search Skill)

```
mem-search Skill 的搜索流程:

Level 1: 概览
  "关于 API 认证，你之前了解了什么？"
  → 返回: 2-3 条精简摘要

Level 2: 详情
  "能展开说说 token 刷新机制吗？"
  → 返回: 相关记忆的完整内容

Level 3: 原始
  "给我看看原始的观察记录"
  → 返回: 未压缩的原始观察列表
```

## 问题与思考

- [x] Worker 的核心职责? → 压缩 + 搜索
- [x] 为什么用 Bun? → 性能 + 原生 TS + 轻量
- [ ] 压缩的 prompt 具体设计?
- [ ] 搜索结果的排序算法?
- [ ] Worker 崩溃的恢复策略?
- [ ] 高频 Session 的压缩积压处理?

---

*参考: [architecture.md](../architecture.md) | [03-database-layer.md](./03-database-layer.md)*
