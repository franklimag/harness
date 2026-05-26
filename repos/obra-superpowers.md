# Superpowers

> Agentic Skills 框架 & 软件开发方法论 — 让 Agent 遵循工程流程

## 基本信息

| 属性 | 值 |
|------|-----|
| 仓库 | https://github.com/obra/superpowers |
| 作者 | Jesse Vincent (@obra) |
| 语言 | Markdown (SKILL.md 文件) |
| 定位 | 完整的 Agent 软件开发方法论 + 可组合 Skills 框架 |
| Stars | 57k+ |
| 官方文档 | https://obra-superpowers.mintlify.app/ |
| PyPI | [agent-superpowers](https://pypi.org/project/agent-superpowers/) |
| 标准 | 兼容 [agentskills.io](https://agentskills.io) 开放标准 |

## 核心特点

- **完整开发方法论**: 不只是工具，是一套强制执行的开发流程
- **流程强制**: brainstorm → plan → TDD → review → finish
- **子 Agent 驱动**: 使用 subagent-driven-development 实现自主长时间工作
- **多 Harness 支持**: Claude Code, Codex CLI/App, Gemini CLI, OpenCode, Cursor, Copilot
- **官方 Plugin 市场**: 通过各 Harness 的 plugin marketplace 安装
- **自动触发**: Skills 根据上下文自动激活，无需手动指定

## 开发流程 (The Superpowers Workflow)

```
用户发起任务
    ↓
1. 🧠 Brainstorming    — 不直接写代码，先问"你真正想做什么"
    ↓
2. 📋 Specification     — 生成分块的设计 spec，分段让用户确认
    ↓
3. 📝 Planning          — 制定清晰的实现计划（TDD + YAGNI + DRY）
    ↓
4. 🏗️ Implementation   — Subagent-driven-development，子 Agent 按计划执行
    ↓ (可自主工作数小时)
5. 🔍 Code Review       — 检查和审查子 Agent 的工作
    ↓
6. ✅ Finish            — 完成分支、清理、合并
```

## 核心 Skills

| Skill | 说明 |
|-------|------|
| brainstorming | 需求澄清和方案探索 |
| subagent-driven-development | 子 Agent 驱动的并行开发 |
| TDD | 红绿重构测试驱动开发 |
| code-reviewer | 结构化代码审查 |
| systematic-debugging | 系统化调试方法 |
| writing-skills | 用 TDD 方法写 Skill 文档 |

## 适用场景

- 让 AI 编程 Agent 遵循工程最佳实践
- 团队标准化 Agent 辅助开发流程
- 需要 Agent 长时间自主工作的复杂任务
- 教育场景：演示 Agent 应该如何工作

## 学习价值

- ⭐⭐ 理解"开发方法论"如何编码为 Agent Skills
- 学习 subagent-driven-development 模式
- 理解 "Skills 自动触发" 的设计机制
- 对比 "Skills 框架" vs "Harness 框架" 的边界
- Jesse Vincent 是经验丰富的开源维护者 (Perl/Ruby 社区知名)

## 参考资料

- [Superpowers Skills Framework (57k stars)](https://rywalker.com/research/superpowers-skills-framework)
- [A skills framework that pulls coding agents back into engineering process](https://www.knightli.com/en/2026/05/15/obra-superpowers-agentic-skills-framework/)
- [Skills System Overview (官方文档)](https://obra-superpowers.mintlify.app/concepts/overview)
- [The Superpowers Workflow (官方文档)](https://obra-superpowers.mintlify.app/concepts/workflow)
- [Claude Plugin Marketplace](https://claude.com/plugins/superpowers)

---

*最后更新: 2026-05-26*
