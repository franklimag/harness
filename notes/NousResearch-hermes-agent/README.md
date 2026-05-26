# NousResearch/hermes-agent 学习笔记工程

> 用于系统学习 [hermes-agent](https://github.com/NousResearch/hermes-agent) 架构、自进化机制与多平台设计的知识库。

## 项目简介

**hermes-agent** 是由 Nous Research 开发的**自进化 AI Agent**，标语为"The agent that grows with you"。它不仅是一个编码助手，更是一个具有闭环学习能力、多平台通信、多模型支持的完整 Agent 系统。

### 核心数据

| 属性 | 值 |
|------|-----|
| 仓库 | https://github.com/NousResearch/hermes-agent |
| 组织 | Nous Research |
| 语言 | Python + Rust |
| 文档 | https://hermes-agent.nousresearch.com/docs/ |
| 许可证 | MIT |
| 标语 | "The agent that grows with you" |
| 相关项目 | hermes-agent-self-evolution (DSPy + GEPA) |
| CLI | 完整 TUI (多行编辑, 斜杠命令, 流式输出) |

### 七大核心特性

| # | 特性 | 说明 |
|---|------|------|
| 1 | 闭环学习 | 从经验中创建 Skills，使用中改进，提示持久化知识 |
| 2 | 多平台通信 | Telegram/Discord/Slack/WhatsApp/Signal/CLI |
| 3 | 模型无关 | 10+ 提供商，200+ 模型 |
| 4 | 7 种终端后端 | Local/Docker/SSH/Singularity/Modal/Daytona/Vercel |
| 5 | 无服务器持久化 | Modal/Daytona 空闲休眠 |
| 6 | Subagent 委派 | 隔离子 Agent 并行工作流 |
| 7 | 定时自动化 | 内置 cron 调度器，自然语言配置 |

### 架构概览

```
┌─────────────────────────────────────────────────────────────┐
│                  通信网关 (Gateway)                           │
│  Telegram / Discord / Slack / WhatsApp / Signal / CLI        │
│  单一网关进程统一接入                                          │
└───────────────────────────────┬─────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────┐
│                  Agent Core (Python)                          │
│                                                             │
│  ┌──────────┐  ┌──────────┐  ┌──────────────┐              │
│  │ 学习循环  │  │ Subagent │  │ Cron 调度器  │              │
│  │ CL Loop  │  │ 委派系统  │  │ 定时任务     │              │
│  └──────────┘  └──────────┘  └──────────────┘              │
│                                                             │
│  ┌──────────────────────────────────────────┐               │
│  │  Memory: Agent 策展 + FTS5 + Honcho      │               │
│  └──────────────────────────────────────────┘               │
└───────────────────────────────┬─────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────┐
│              Terminal Backends (7 种)                         │
│  Local / Docker / SSH / Singularity / Modal / Daytona / Vercel│
└─────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────┐
│              Model Providers (10+)                            │
│  Nous Portal / OpenRouter / NovitaAI / NVIDIA NIM           │
│  Xiaomi MiMo / z.ai/GLM / Kimi / MiniMax / HuggingFace     │
│  OpenAI / ...                                               │
└─────────────────────────────────────────────────────────────┘
```

## 学习目录

```
.
├── README.md                  # 本文件
├── architecture.md            # 架构总览 (Gateway → Core → Backends)
├── changelog.md               # 版本更新追踪
├── references.md              # 参考资料汇总
├── learning-guide.md          # 学习计划指南 (五阶段路线)
└── notes/
    ├── 01-learning-loop.md    # 闭环学习 (Skills 自改进, 记忆, FTS5, Honcho)
    ├── 02-gateway-channels.md # 多平台通信 (单一网关, 6 个通道)
    ├── 03-terminal-backends.md # 7 种终端后端设计
    ├── 04-model-providers.md  # 模型无关 (10+ 提供商, 回退链)
    └── 05-research-trajectories.md # 研究功能 (轨迹生成, 压缩训练)
```

## 学习路线

1. **阶段一** — 理解整体架构（Gateway → Core → Backends）
2. **阶段二** — 深入闭环学习系统（Skills 自进化 + 记忆）
3. **阶段三** — 学习多平台通信和终端后端设计
4. **阶段四** — 理解模型无关设计和提供商切换
5. **阶段五** — 了解研究功能（轨迹生成与压缩）

详见 [learning-guide.md](./learning-guide.md)

## 学习目标

- [x] 理解 hermes-agent 的定位（自进化 Agent）
- [ ] 深入闭环学习循环的完整实现
- [ ] 理解 Gateway 模式（多通道 → 统一 Agent）
- [ ] 学习 7 种 Terminal Backend 的设计
- [ ] 理解模型无关设计和回退链
- [ ] 学习轨迹生成和压缩（研究方向）
- [ ] 对比 hermes-agent vs ECC (Instincts) vs claude-mem

## 关键差异化

| 差异点 | 说明 |
|--------|------|
| 自进化 | 闭环学习，Skills 在使用中自动改进 |
| 多平台通信 | 6 种通道统一接入 |
| 7 种后端 | 从本地到云无服务器完整覆盖 |
| 研究级 | 轨迹生成+压缩，可用于模型训练 |
| Honcho | 辩证法用户建模 |
| 200+ 模型 | 最广泛的模型支持 |

## 相关链接

- 源码: https://github.com/NousResearch/hermes-agent
- 文档: https://hermes-agent.nousresearch.com/docs/
- 进化: hermes-agent-self-evolution (DSPy + GEPA)
- 标准: agentskills.io 兼容

---

*最后更新: 2026-07*
*项目信息见: [repos/NousResearch-hermes-agent.md](../../repos/NousResearch-hermes-agent.md)*
