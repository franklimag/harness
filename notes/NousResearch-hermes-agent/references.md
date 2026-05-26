# 参考资料

> 整理 hermes-agent 相关的官方资源、研究论文和技术参考。

## 官方资源

| 资源 | 链接 |
|------|------|
| 源码仓库 | https://github.com/NousResearch/hermes-agent |
| 官方文档 | https://hermes-agent.nousresearch.com/docs/ |
| 自进化分支 | hermes-agent-self-evolution |
| 许可证 (MIT) | 仓库 LICENSE |
| Nous Research | https://nousresearch.com |

## 核心技术组件

| 组件 | 技术 | 用途 |
|------|------|------|
| Agent Core | Python | 主要推理和协调 |
| 性能关键路径 | Rust | 高性能处理 |
| 全文搜索 | SQLite FTS5 | 跨会话搜索 |
| 用户建模 | Honcho | 辩证法建模 |
| 自进化 | DSPy + GEPA | 优化 pipeline |
| Skills 标准 | agentskills.io | 技能兼容 |

## 模型提供商参考

| 提供商 | 链接/说明 |
|--------|---------|
| Nous Portal | Nous Research 自有推理服务 |
| OpenRouter | https://openrouter.ai/ (200+ 模型) |
| NovitaAI | https://novita.ai/ |
| NVIDIA NIM | NVIDIA 推理微服务 |
| Xiaomi MiMo | 小米 AI 模型 |
| z.ai / GLM | 智谱 AI |
| Kimi / Moonshot | https://www.moonshot.cn/ |
| MiniMax | https://www.minimax.chat/ |
| HuggingFace | https://huggingface.co/ |
| OpenAI | https://openai.com/ |

## Terminal Backend 参考

| 后端 | 参考 |
|------|------|
| Docker | https://docs.docker.com/ |
| SSH | OpenSSH 标准 |
| Singularity/Apptainer | https://apptainer.org/ |
| Modal | https://modal.com/ |
| Daytona | https://www.daytona.io/ |
| Vercel Sandbox | https://vercel.com/ |

## 通信通道参考

| 通道 | API/协议 |
|------|---------|
| Telegram | Bot API |
| Discord | Discord.py / Bot API |
| Slack | Slack Bolt / Web API |
| WhatsApp | WhatsApp Business API |
| Signal | Signal Protocol |
| CLI | 自建 TUI (Python) |

## 研究参考

| 领域 | 参考 |
|------|------|
| DSPy | Stanford DSPy 框架 (优化 LM 程序) |
| GEPA | Genetic Evolution of Prompt Agents |
| 轨迹生成 | Agent 轨迹数据收集方法论 |
| 轨迹压缩 | 信息压缩理论 + LLM 蒸馏 |
| 辩证法建模 | Honcho dialectic user modeling |
| FTS5 | SQLite Full-Text Search 5 |

## 核心概念术语表

| 术语 | 定义 |
|------|------|
| Closed Learning Loop | 闭环学习，从经验中学习并自我改进 |
| Gateway | 通信网关，统一多通道接入 |
| Terminal Backend | 终端后端，代码执行环境 |
| Honcho | 辩证法用户建模系统 |
| FTS5 | SQLite 全文搜索引擎 (第 5 版) |
| Subagent | 子 Agent，隔离执行独立任务 |
| Trajectory | 轨迹，Agent 执行任务的完整记录 |
| DSPy | Declarative Self-Improving Python |
| GEPA | Genetic Evolution of Prompt Agents |

## 对比参考项目

| 项目 | 对比维度 |
|------|---------|
| ECC Instincts | 学习系统设计对比 |
| claude-mem | 记忆系统对比 |
| superpowers Subagent | 子 Agent 模式对比 |
| OpenClaw Gateway | 通信网关设计对比 |

---

*最后更新: 2026-07*
