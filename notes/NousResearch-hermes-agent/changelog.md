# hermes-agent 版本更新追踪

> 跟踪 hermes-agent 的发展历程、核心功能迭代和研究方向演进。

## 项目概况

hermes-agent 由 Nous Research 团队开发维护，作为研究级自进化 Agent，其更新既包含工程特性也包含研究突破。

## 核心发展时间线

### 早期 — 基础 Agent 能力

| 功能 | 说明 |
|------|------|
| 基础推理循环 | 工具调用 + 流式输出 |
| CLI (TUI) | 多行编辑 + 斜杠命令 |
| Local 后端 | 本地代码执行 |
| 基础记忆 | 会话内上下文管理 |

### 中期 — 平台扩展

| 功能 | 说明 |
|------|------|
| Gateway 架构 | 统一消息网关 |
| Telegram/Discord | 首批通信通道 |
| Docker/SSH 后端 | 远程执行能力 |
| 多模型支持 | OpenRouter 集成 |
| FTS5 搜索 | 跨会话搜索 |

### 当前 — 自进化与研究

| 功能 | 说明 |
|------|------|
| 闭环学习 | Skills 自创建和自改进 |
| Honcho 用户建模 | 辩证法用户模型 |
| 7 种后端 | Modal/Daytona/Vercel/Singularity |
| 10+ 提供商 | 国内外模型全覆盖 |
| 轨迹生成 | batch 模式研究数据 |
| 轨迹压缩 | 训练数据高效化 |
| Subagent | 隔离并行工作流 |
| 定时调度 | cron + 自然语言 |

## 通信通道演进

```
Phase 1: CLI Only (本地)
    │
    ▼
Phase 2: + Telegram + Discord (社区)
    │
    ▼
Phase 3: + Slack (企业)
    │
    ▼
Phase 4: + WhatsApp + Signal (全通道)
```

## Terminal Backend 演进

```
Phase 1: Local (最基础)
    │
    ▼
Phase 2: + Docker + SSH (远程隔离)
    │
    ▼
Phase 3: + Singularity (HPC)
    │
    ▼
Phase 4: + Modal + Daytona + Vercel (无服务器)
```

## 模型提供商演进

```
早期: Nous Portal + OpenAI
    │
    ▼
扩展: + OpenRouter (200+ 模型)
    │
    ▼
国际化: + NVIDIA + HuggingFace + NovitaAI
    │
    ▼
中国区: + Xiaomi MiMo + z.ai/GLM + Kimi + MiniMax
```

## 研究方向里程碑

| 研究 | 说明 |
|------|------|
| hermes-agent-self-evolution | DSPy + GEPA 自进化分支 |
| 轨迹生成 | batch 任务执行记录 |
| 轨迹压缩 | 训练数据精简 |
| Honcho | 辩证法用户建模 |

## 关键设计决策时间线

| 决策 | 原因 |
|------|------|
| Python + Rust | Python (AI 生态) + Rust (性能关键路径) |
| 单一 Gateway | 统一接入降低复杂度 |
| 7 种后端 | 覆盖全场景（开发/测试/生产/研究） |
| agentskills.io 兼容 | 生态互操作性 |
| FTS5 | 轻量级全文搜索（无需外部服务） |

## 关注的未来方向

- [ ] DSPy + GEPA 自进化的成熟化
- [ ] 更多通信通道（Matrix、IRC 等）
- [ ] 轨迹压缩算法优化
- [ ] 多 Agent 协作（非单一 Subagent）
- [ ] 联邦学习（跨实例知识共享）
- [ ] 安全框架（类似 ECC AgentShield）

## 如何追踪更新

1. **GitHub**: https://github.com/NousResearch/hermes-agent
2. **文档**: https://hermes-agent.nousresearch.com/docs/
3. **Nous Research**: 官方博客和论文
4. **Discord**: Nous Research 社区

---

*最后更新: 2026-07*
