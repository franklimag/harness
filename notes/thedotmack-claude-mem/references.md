# 参考资料

> 整理 claude-mem 相关的官方资源和技术参考。

## 官方资源

| 资源 | 链接 |
|------|------|
| 源码仓库 | https://github.com/thedotmack/claude-mem |
| 文档 | https://docs.claude-mem.ai/ |
| 网站 | https://claude-mem.ai/ |
| Mintlify 文档 | https://claude-memai.mintlify.app/ |
| 作者 | Alex Newman (@thedotmack) |

## 核心技术参考

| 技术 | 用途 | 文档 |
|------|------|------|
| Bun | 运行时 | https://bun.sh/ |
| SQLite | 主存储 | https://sqlite.org/ |
| FTS5 | 全文搜索 | SQLite FTS5 文档 |
| ChromaDB | 语义搜索 (可选) | https://www.trychroma.com/ |
| Claude Agent SDK | AI 压缩引擎 | Anthropic 文档 |

## 5 阶段 Hook 参考

| 阶段 | 事件 | 触发时机 |
|------|------|---------|
| PreSession | session_start | 新 Session 开始 |
| ToolCall | tool_use | 每次工具调用 |
| Observation | important_finding | 重要发现 |
| Compression | batch_ready | 观察累积到阈值 |
| PostSession | session_end | Session 结束 |

## API 端点参考

| 端点 | 方法 | 功能 |
|------|------|------|
| /search | GET | 通用搜索 |
| /search/recent | GET | 最近记忆 |
| /search/relevant | GET | 相关性搜索 |
| /search/semantic | GET | 语义搜索 |
| /memories | GET/POST | 记忆 CRUD |
| /compress | POST | 触发压缩 |
| /status | GET | Worker 状态 |
| /health | GET | 健康检查 |
| /stats | GET | 统计信息 |
| /config | GET/PUT | 配置管理 |

## 支持的 Agent

| Agent | 集成方式 |
|-------|---------|
| Claude Code | 原生 Hook |
| OpenClaw | Hook 适配 |
| Codex | Hook 适配 |
| Gemini | Hook 适配 |
| Hermes | Hook 适配 |
| Copilot | Hook 适配 |
| OpenCode | Hook 适配 |

## 对比项目

| 项目 | 对比维度 |
|------|---------|
| hermes-agent | 闭环学习 vs Hook 捕获 |
| ECC Instincts | 自进化 vs 被动记忆 |
| superpowers | 方法论 vs 记忆 |

## 术语表

| 术语 | 定义 |
|------|------|
| Hook | Session 生命周期事件拦截器 |
| Worker | 后台异步处理服务 |
| Compression | AI 驱动的信息压缩 |
| FTS5 | SQLite Full-Text Search v5 |
| Token Budget | 注入上下文的 Token 预算限制 |
| Nudge | 提示用户保存重要信息 |
| mem-search | 基于 Skill 的记忆搜索 |

---

*最后更新: 2026-07*
