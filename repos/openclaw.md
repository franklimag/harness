# OpenClaw

> 开源自托管 AI Agent 框架 — Local-first, 50+ 通道, 350k+ Stars

## 基本信息

| 属性 | 值 |
|------|-----|
| 仓库 | https://github.com/openclaw/openclaw |
| 组织 | OpenClaw (原 Moltbot → Clawdbot) |
| 语言 | JavaScript/TypeScript (Node.js) |
| 定位 | 自托管个人 AI Agent 框架 (Local-first daemon) |
| Stars | 350k+ |
| 用户数 | 3.2M+ |
| 当前版本 | 2026.5.22 (稳定) |
| 创始人 | Peter Steinberger (现任 OpenAI) |
| 官方文档 | https://docs.openclaw.ai/ |
| 官网 | https://openclaw.ai |
| 运行要求 | Node 24 (推荐) / Node 22.14+ (最低) |

## 核心特点

- **Local-first**: 作为 daemon 进程运行在本地设备，数据不出本地
- **多平台接入**: 50+ 消息平台（内置通道 + 通道插件）
- **Observe-Plan-Act 循环**: 自主完成复杂任务的 Agentic Loop
- **多模型 + Fallback**: GPT-5.x / Opus 4.6 / Grok / Gemini，不绑定单一供应商
- **Markdown-first 记忆**: SOUL.md / AGENTS.md / TOOLS.md，透明可审计
- **Skills + Plugins + MCP**: ClawHub 市场 1200+ skills
- **极活跃社区**: 350k+ Stars，版本迭代极快（每周多次）

## 架构概述

```
┌─────────────────────────────────────────────────────────┐
│              Channels (通道层, 50+ 平台)                  │
│  WhatsApp / Telegram / Slack / Discord / Signal / ...    │
└───────────────────────────┬─────────────────────────────┘
                            │ WebSocket / Webhook
                            ▼
┌─────────────────────────────────────────────────────────┐
│              Gateway (控制平面, port 18789)               │
│  Channel Adapters / Session Mgmt / Lane Queue / Hooks    │
│  Cron (Chronix) / Webhook Router / Plugin System         │
└───────────────────────────┬─────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────┐
│          Agent Runtime (Pi Agent Core)                    │
│  上下文组装 / 多模型调度 / Agentic Loop / Tool Execution  │
│  Compaction / Streaming / Runtime Fallbacks               │
└───────────────────────────┬─────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────┐
│          Execution (Tools/Skills/MCP)                     │
│  Shell / Browser / File / API / Canvas / Chronix         │
└───────────────────────────┬─────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────┐
│          Memory & Knowledge (记忆层)                      │
│  SOUL.md / AGENTS.md / TOOLS.md / IDENTITY.md            │
│  SQLite Index / Append-only Transcripts / Activation-Decay│
└─────────────────────────────────────────────────────────┘
```

## 核心组件

| 组件 | 说明 |
|------|------|
| Gateway | WebSocket 服务器 (port 18789)，中央控制平面 |
| Channel Adapters | 为 50+ 消息平台提供统一适配 |
| Session 管理 | append-only transcript + mutable index |
| Lane Queue | 同 session 内串行执行，防止竞态 |
| Pi Agent Core | Agentic Loop: Observe → Plan → Act → Result |
| Codex 集成 | 默认使用 OpenAI Codex app-server runtime |
| Memory | Markdown-first + SQLite + Activation/Decay |
| Skills | AgentSkills 格式，ClawHub 市场分发 |
| Plugins | defineToolPlugin API，通道/工具均可扩展 |

## 安全事件 (2026)

- **ClawJacked**: Agent 被静默接管（24h 内修复）
- **Claw Chain**: 四个链式漏洞（沙箱逃逸 + 凭证窃取）
- **ClawHub 恶意包**: 公共 Skills 注册表被投毒
- **Prompt Injection**: 通过 BOOTSTRAP.md 注入实施隐蔽操纵

## 适用场景

- 个人 AI Agent 自托管部署
- 多平台统一消息 AI 助手
- Agent 框架研究与学习
- Skills/Plugins 生态开发

## 学习价值

- ⭐⭐⭐ Agent Harness 标杆项目（最完整的开源实现之一）
- Gateway + Agent Runtime 完整架构
- Markdown-first 记忆系统设计
- 安全事件和防御策略的真实案例
- Plugin/Skills 生态设计
- 对比 Hermes Agent 的类似 Gateway 模式

## 已有学习笔记

本仓库 `notes/` 目录下有独立的 OpenClaw 学习笔记工程的引用：
→ 详见 [notes/openclaw.md](../notes/openclaw.md)

完整学习笔记仓库：https://github.com/franklimag/openclaw

## 参考资料

- [官方文档](https://docs.openclaw.ai/)
- [官方博客](https://openclaw.ai/blog)
- [GitHub Releases](https://github.com/openclaw/openclaw/releases)
- [CHANGELOG](https://github.com/openclaw/openclaw/blob/main/CHANGELOG.md)
- [中文版](https://github.com/jiulingyun/openclaw-cn)
- [Skills 市场](https://github.com/openclaw/skills)
- [版本追踪 (PatchBot)](https://patchbot.io/ai/openclaw)
- [Architecture Breakdown (ciela.ai)](https://www.ciela.ai/blogs/openclaw-architecture-breakdown)
- [Agent Harness Survey 论文中的分析](https://www.preprints.org/manuscript/202604.0428/v1)

---

*最后更新: 2026-05-26*
