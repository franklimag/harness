# ECC (Everything Claude Code)

> Agent Harness 性能优化系统 — Skills, Instincts, Memory, Security

## 基本信息

| 属性 | 值 |
|------|-----|
| 仓库 | https://github.com/affaan-m/ECC |
| 作者 | Affaan Mustafa (@affaan-m) |
| 语言 | Markdown / YAML (配置驱动) |
| 定位 | Agent Harness 性能优化系统（技能、本能、记忆、安全） |
| Stars | 140k+ |
| 官网 | https://everything-claude-code-olive.vercel.app/ |
| 相关项目 | [AgentShield](https://github.com/affaan-m/agentshield) (安全扫描) |

## 核心特点

- **规模庞大**: 156+ Skills, 25+ 专用 Agent, 997 内部测试
- **Harness 无关**: 支持 Claude Code, Codex, OpenCode, Cursor 等多个 Agent Harness
- **本能系统 (Instincts)**: 观察 session → 创建原子本能（带置信度评分）→ 进化为 skills/commands/agents
- **持续学习**: 基于 Hook 的 session 观察 + 跨 session 知识演化
- **安全优先**: 内置安全扫描和配置审计
- **研究导向**: 10+ 个月日常生产使用的经验沉淀

## 解决的问题

AI Agent 的原始能力未经优化——没有结构化工作流、没有跨 session 学习、没有安全防护。ECC 提供一套完整的配置系统，将 Agent 从"聊天机器人"提升为"工程团队成员"。

## 架构概述

```
ECC 配置层
├── Skills (156+)           — 结构化工作流指导
├── Instincts               — 自动学习的行为模式
├── Agents (25+)            — 专用角色 (architect, reviewer, etc.)
├── Hooks                   — Session 观察和事件触发
├── Rules                   — 行为约束和最佳实践
├── Commands                — 快捷指令
└── Memory                  — 跨 session 知识持久化
         ↓
    Agent Harness (Claude Code / Codex / Cursor / OpenCode)
         ↓
    LLM Provider
```

核心循环：
1. **观察**: Hooks 监控 session 活动
2. **学习**: 创建原子 Instincts（带置信度）
3. **进化**: Instincts 成熟后升级为 Skills / Commands / Agents
4. **应用**: 在后续 session 中自动触发

## 适用场景

- 优化 Claude Code / Codex 等 Agent 的工作效率
- 为团队标准化 Agent 工作流
- Agent 安全配置审计
- 研究 Agent 行为自进化

## 学习价值

- ⭐⭐ 理解 Agent Harness 的"配置即基础设施"理念
- 学习 Instincts（自动学习）系统设计
- 理解 Skills 规范（agentskills.io 标准）
- Hook 机制：如何在不修改 Agent 代码的情况下扩展行为
- 跨 Harness 兼容性设计

## 参考资料

- [Everything Claude Code: The Agent Harness Your Team Is Missing](https://www.bighatgroup.com/blog/everything-claude-code-ai-agent-harness-guide/)
- [Inside the 82K-Star Agent Harness That's Dividing the Developer Community](https://medium.com/@tentenco/everything-claude-code-inside-the-82k-star-agent-harness-thats-dividing-the-developer-community-4fe54feccbc1)
- [ECC v1.10.0 — 140K stars, 156 skills, ECC 2.0 alpha](https://github.com/affaan-m/everything-claude-code/discussions/1272)
- [AgentShield — AI agent security scanner](https://github.com/affaan-m/agentshield)

---

*最后更新: 2026-05-26*
