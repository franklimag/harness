# Hermes Agent

> The agent that grows with you — Nous Research 出品的自进化 AI Agent

## 基本信息

| 属性 | 值 |
|------|-----|
| 仓库 | https://github.com/NousResearch/hermes-agent |
| 组织 | [Nous Research](https://nousresearch.com) |
| 语言 | Python + Rust |
| 定位 | 自进化 AI Agent —— 唯一内置学习循环的 Agent |
| 许可证 | MIT |
| 官方文档 | https://hermes-agent.nousresearch.com/docs/ |
| 相关项目 | [hermes-agent-self-evolution](https://github.com/NousResearch/hermes-agent-self-evolution) (DSPy + GEPA 自我进化) |

## 核心特点

- **内置学习循环**: 唯一自带学习系统的 Agent —— 从经验创建 Skills，使用中改进，自动持久化知识
- **多平台通信**: Telegram, Discord, Slack, WhatsApp, Signal + CLI，通过 Gateway 统一管理
- **模型无关**: Nous Portal, OpenRouter (200+ models), NVIDIA NIM, Xiaomi, Kimi, HuggingFace, OpenAI 等
- **多种运行环境**: 7 种 Terminal Backend —— 本地、Docker、SSH、Singularity、Modal、Daytona、Vercel Sandbox
- **Serverless 持久化**: Modal/Daytona 支持 —— 空闲时休眠，需要时唤醒
- **完整 TUI**: 多行编辑、slash-command 自动补全、会话历史、中断重定向、流式工具输出
- **子 Agent 委派**: 生成隔离子 Agent 进行并行工作流
- **研究就绪**: 批量轨迹生成，轨迹压缩（用于训练下一代工具调用模型）

## 学习系统 (Closed Learning Loop)

```
Session 活动
    ↓
Agent 策划的记忆 + 周期性 nudges (自动提醒持久化知识)
    ↓
自主 Skill 创建 (复杂任务后自动生成)
    ↓
Skills 在使用中自我改进
    ↓
FTS5 Session 搜索 + LLM 摘要 (跨 session 回忆)
    ↓
Honcho 辩证用户建模 (构建对用户的深层理解)
    ↓
兼容 agentskills.io 开放标准
```

## 架构概述

```
多通道接入层
├── Telegram / Discord / Slack / WhatsApp / Signal
├── CLI (TUI)
└── Voice memo transcription
         ↓
    Gateway (统一通信)
         ↓
    Agent Core (Python)
    ├── LLM Provider (任意模型 + fallback)
    ├── Tool Execution
    │   ├── Web search / Browser automation / Vision
    │   ├── Image generation / TTS
    │   └── Multi-model reasoning
    ├── Learning Loop (Skills 自创建 + 自改进)
    ├── Memory (Agent-curated + FTS5 + Honcho)
    ├── Cron Scheduler (定时任务)
    └── Subagent Delegation (并行工作流)
         ↓
    Terminal Backends (7 种)
    ├── Local / Docker / SSH / Singularity
    └── Modal / Daytona / Vercel Sandbox (serverless)
```

## 适用场景

- 需要 Agent 跨 session 持续学习和成长
- 多平台统一 AI 助手
- 云端部署的持久化 Agent ($5 VPS 即可)
- Agent 行为研究和训练数据生成

## 学习价值

- ⭐⭐⭐ 学习 Agent "自进化"系统的完整实现
- 理解 Gateway 模式（多通道 → 统一 Agent）
- 学习 Agent 记忆系统的多层设计（FTS5 + Honcho + Skills）
- Serverless Agent 部署架构
- 研究级别的轨迹生成和压缩
- 对比 OpenClaw 的 Gateway 模式

## 参考资料

- [官方文档](https://hermes-agent.nousresearch.com/docs/)
- [CLI Commands Reference](https://hermes-agent.nousresearch.com/docs/reference/cli-commands)
- [Self-Evolution (DSPy + GEPA)](https://github.com/NousResearch/hermes-agent-self-evolution)
- [Nous Research](https://nousresearch.com)
- [agentskills.io 标准](https://agentskills.io)
- [Honcho 用户建模](https://github.com/plastic-labs/honcho)

---

*最后更新: 2026-05-26*
