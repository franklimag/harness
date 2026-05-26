# thedotmack/claude-mem 学习笔记工程

> 用于系统学习 [claude-mem](https://github.com/thedotmack/claude-mem) 跨 Session 持久化记忆系统的知识库。

## 项目简介

**claude-mem** 是由 Alex Newman (@thedotmack) 开发的 **Agent 跨 Session 持久化记忆**系统。通过 5 阶段 Hook 生命周期捕获每次 Session 中的工具使用和发现，用 AI 压缩后存储，在新 Session 开始时注入相关上下文。

### 核心数据

| 属性 | 值 |
|------|-----|
| GitHub Stars | 72K+ |
| 作者 | Alex Newman (@thedotmack) |
| 语言 | TypeScript (Bun runtime) |
| 文档 | https://docs.claude-mem.ai/ |
| 网站 | https://claude-mem.ai/ |
| 许可证 | MIT |
| 源码 | https://github.com/thedotmack/claude-mem |

### 支持的 Agent

| Agent | 支持状态 |
|-------|---------|
| Claude Code | ✅ 主要目标 |
| OpenClaw | ✅ |
| Codex | ✅ |
| Gemini | ✅ |
| Hermes | ✅ |
| Copilot | ✅ |
| OpenCode | ✅ |

### 核心工作原理

```
┌─────────────────────────────────────────────────────────────┐
│                    claude-mem 工作流                          │
│                                                             │
│  Session 开始                                               │
│    │                                                        │
│    ├── 1. PreSession: 搜索相关记忆 → 注入上下文             │
│    │                                                        │
│    ├── 2. ToolCall: 每次工具调用 → 记录观察                 │
│    │                                                        │
│    ├── 3. Observation: 重要发现 → 标记记忆点                │
│    │                                                        │
│    ├── 4. Compression: 累积观察 → AI 压缩为精华             │
│    │                                                        │
│    └── 5. PostSession: 压缩后的知识 → 持久化到 DB           │
│                                                             │
│  Session 结束                                               │
│                                                             │
│  下次 Session → PreSession 搜索 → 相关记忆注入             │
└─────────────────────────────────────────────────────────────┘
```

### 架构四层

```
┌─────────────────────────────────────────────────────────────┐
│  Layer 1: Hook 系统                                          │
│  5 阶段生命周期事件捕获                                       │
│  (PreSession → ToolCall → Observation → Compression → Post) │
└───────────────────────────────┬─────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────┐
│  Layer 2: Worker Service                                     │
│  后台压缩工作者 (Claude Agent SDK + HTTP API)                │
│  10 个搜索端点                                               │
└───────────────────────────────┬─────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────┐
│  Layer 3: Database Layer                                     │
│  SQLite + FTS5 (必选) + ChromaDB (可选，语义搜索)            │
└───────────────────────────────┬─────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────┐
│  Layer 4: mem-search Skill                                   │
│  Skill 式搜索接口 (渐进式披露)                               │
└─────────────────────────────────────────────────────────────┘
```

## 学习目录

```
.
├── README.md                  # 本文件
├── architecture.md            # 架构总览 (四层模型 + 工作流)
├── changelog.md               # 版本更新追踪
├── references.md              # 参考资料汇总
├── learning-guide.md          # 学习计划指南 (五阶段路线)
└── notes/
    ├── 01-hook-lifecycle.md   # 5 阶段 Hook 生命周期
    ├── 02-worker-service.md   # 后台压缩 Worker
    ├── 03-database-layer.md   # SQLite + FTS5 + ChromaDB
    ├── 04-context-injection.md # 上下文注入与 Token 管理
    └── 05-cross-agent-support.md # 7+ Agent 支持与架构演进
```

## 学习目标

- [x] 理解 claude-mem 的定位（跨 Session 持久化记忆）
- [ ] 深入 5 阶段 Hook 生命周期
- [ ] 理解 AI 压缩技术（session 信息浓缩）
- [ ] 学习 SQLite + FTS5 存储层设计
- [ ] 掌握上下文注入的 Token 预算管理
- [ ] 理解 7+ Agent 跨平台支持
- [ ] 对比 claude-mem vs hermes-agent 记忆系统

## 关键差异化

| 差异点 | 说明 |
|--------|------|
| 72K+ Stars | Agent 记忆领域最大社区 |
| 5 阶段 Hook | 完整的会话生命周期捕获 |
| AI 压缩 | 用 Claude Agent SDK 压缩观察 |
| 7+ Agent | 最广泛的 Agent 工具支持 |
| Token 预算 | 精确的注入 Token 管理 |
| Bun Runtime | PM2 → Bun 架构演进 |

## 相关链接

- 源码: https://github.com/thedotmack/claude-mem
- 文档: https://docs.claude-mem.ai/
- 网站: https://claude-mem.ai/
- Mintlify: https://claude-memai.mintlify.app/

---

*最后更新: 2026-07*
*项目信息见: [repos/thedotmack-claude-mem.md](../../repos/thedotmack-claude-mem.md)*
