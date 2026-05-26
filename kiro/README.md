# Kiro 学习笔记

> 记录使用 Kiro 过程中的心得、技巧和最佳实践。

## 说明

本目录用于记录**工具使用经验**，不是研究对象。
Kiro 是我们使用的 AI 编码工具，这里记录的是：
- 使用技巧和最佳实践
- 功能理解和对比
- Steering / Specs / Skills / Hooks 的实践经验
- 与本项目学习的 Agent Harness 概念的关联

## 目录

| 文件 | 内容 |
|------|------|
| [ide-vs-cli.md](ide-vs-cli.md) | Kiro IDE 与 CLI 的区别和选择建议 |
| [specs-workflow.md](specs-workflow.md) | Specs 工作流学习笔记（待填充） |
| [steering-tips.md](steering-tips.md) | Steering 文件编写技巧（待填充） |
| [hooks-practice.md](hooks-practice.md) | Hooks 实践经验（待填充） |

## 与 Agent Harness 学习的关联

Kiro 本身就是一个 Agent Harness 产品，它的设计可以与收录项目对照：

| Kiro 功能 | 对应的 Agent Harness 概念 | 相关项目 |
|-----------|--------------------------|---------|
| Specs | 结构化开发流程 | obra/superpowers (方法论) |
| Steering | Agent 行为配置 | affaan-m/ECC (Rules) |
| Skills | 可组合能力扩展 | mattpocock/skills |
| Hooks | 事件驱动自动化 | affaan-m/ECC (Hooks), thedotmack/claude-mem |
| Powers | MCP Server 集成 | colbymchenry/codegraph (MCP) |

---

*最后更新: 2026-05-26*
