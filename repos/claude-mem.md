# Claude-Mem

> 跨 Session 持久化记忆 — 自动捕获、压缩、注入 Agent 上下文

## 基本信息

| 属性 | 值 |
|------|-----|
| 仓库 | https://github.com/thedotmack/claude-mem |
| 作者 | @thedotmack |
| 语言 | TypeScript (Bun 运行时) |
| 定位 | Agent 持久化记忆系统 —— 自动捕获、AI 压缩、跨 session 注入 |
| Stars | 72k+ |
| 官网 | https://claude-mem.ai/ |
| 文档 | https://docs.claude-mem.ai/ |

## 核心特点

- **自动捕获**: Hook 到 session 生命周期事件，自动记录 Agent 所做的一切
- **AI 压缩**: 使用 Claude agent-sdk 将原始工具调用输出压缩为结构化观察
- **跨 session 注入**: 未来 session 启动时自动注入相关历史上下文
- **5 阶段 Hook 系统**: session start → tool call → observation → compression → session end
- **多 Agent 支持**: Claude Code, OpenClaw, Codex, Gemini, Hermes, Copilot, OpenCode 等
- **本地存储**: SQLite + FTS5 全文搜索 (可选 Chroma 语义搜索)

## 解决的问题

AI Agent 没有跨 session 的记忆 —— 每次新 session 都从零开始，忘记之前做过的一切。对于持续开发的项目，Agent 每次都要重新"学习"项目结构、已做的决策和约定。

Claude-Mem 在后台自动工作：观察 Agent 的行为，压缩关键信息，在下次 session 时自动注入相关上下文。

## 架构概述

```
Session 生命周期
├── Session Start → Hook 触发
├── Tool Call → 记录观察
├── ...
└── Session End → 触发压缩
         ↓
    Compression Layer
    ├── AI 语义压缩 (Claude agent-sdk)
    ├── 去重 & 合并
    └── 结构化观察 (observations)
         ↓
    Storage Layer
    ├── SQLite (主存储)
    ├── FTS5 (全文搜索)
    └── Chroma (可选, 语义搜索)
         ↓
    Injection Layer (下次 session)
    ├── 相关性匹配
    ├── Token 预算管理
    └── Context 注入
         ↓
    Agent (Claude Code / Codex / Gemini / etc.)
```

### 5 阶段 Hook 系统

| 阶段 | 触发点 | 行为 |
|------|--------|------|
| 1. PreSession | session 启动 | 查询相关历史，注入上下文 |
| 2. ToolCall | 每次工具调用 | 记录原始观察 |
| 3. Observation | 调用完成 | 结构化记录结果 |
| 4. Compression | 定期 / session 结束 | AI 压缩和去重 |
| 5. PostSession | session 结束 | 最终持久化 |

## 适用场景

- 长期维护的项目（Agent 需要记住历史决策）
- 多 session 复杂任务（分多次完成的大功能）
- 团队共享 Agent 记忆（共享上下文）
- 减少重复探索（Agent 不再重复"学习"已知信息）

## 学习价值

- ⭐⭐ 深入理解 Agent 记忆系统的完整实现
- 学习 Hook 系统设计（5 阶段生命周期）
- 理解 AI 压缩技术（如何有效浓缩 session 信息）
- 存储层设计（SQLite + FTS5 + 可选向量搜索）
- 对比 Hermes Agent 记忆系统 vs Claude-Mem 的不同策略
- 跨 Harness 兼容性设计模式

## 参考资料

- [claude-mem adds persistent memory to Claude Code: what developers should know](https://www.augmentcode.com/learn/claude-mem-persistent-memory-claude-code)
- [Claude-Mem Guide: Persistent Memory for Claude Code (DataCamp)](https://www.datacamp.com/tutorial/claude-mem-guide)
- [Architecture Overview](https://docs.claude-mem.ai/architecture/overview)
- [Hook Lifecycle (Mintlify)](https://claude-memai.mintlify.app/architecture/hooks)
- [claude-mem hits 46.1K stars](https://www.augmentcode.com/learn/claude-mem-46k-stars-persistent-memory-claude-code)
- [Context7 文档](https://context7.com/thedotmack/claude-mem)

---

*最后更新: 2026-05-26*
