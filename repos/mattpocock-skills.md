# Skills (Matt Pocock)

> Skills for Real Engineers — 来自 Matt Pocock 的 .claude 目录

## 基本信息

| 属性 | 值 |
|------|-----|
| 仓库 | https://github.com/mattpocock/skills |
| 作者 | Matt Pocock (@mattpocock) |
| 语言 | Markdown (Skill 文件) |
| 定位 | 面向实际工程师的可复用 Agent Skills 集合 |
| Stars | 20k+ |
| 安装 | `npx skills@latest add mattpocock/skills` |

## 核心特点

- **来自真实工作流**: 直接从 Matt Pocock 的 `.claude` 目录公开
- **面向工程实践**: 解决实际工程问题而非炫技
- **小而可组合**: 每个 Skill 独立、易于适配和组合
- **模型无关**: 适用于任何模型
- **30 秒安装**: 通过 `npx skills` CLI 快速安装

## 核心 Skills 领域

- **更清晰的需求**: 引导 Agent 在编码前理清需求
- **共享项目语言**: 建立项目术语和约定
- **更好的 Issue 处理**: 结构化的问题解决流程
- **更干净的实现**: 代码质量和简洁性指导
- **减少 Agent 冗余输出**: 让 Agent 更简洁地沟通

## 架构概述

```
mattpocock/skills
├── /setup-matt-pocock-skills    # 必选：初始化配置
├── Skill A                      # 独立工作流指导
├── Skill B                      # 独立工作流指导
└── ...
         ↓ (通过 npx skills CLI 安装)
    ~/.claude/ 或项目 .claude/
         ↓
    Agent Harness (Claude Code / Cursor / Codex 等)
```

## 适用场景

- Claude Code 用户希望提升 Agent 工程质量
- 学习如何编写有效的 Agent Skills
- 作为参考模板定制自己的 Skills

## 学习价值

- ⭐ 理解"好的 Skill"长什么样（简洁、可操作、面向结果）
- 学习 Skills 规范和分发机制（npx skills CLI）
- Matt Pocock 是 TypeScript 社区知名教育者，Skills 设计哲学值得学习
- 对比 ECC（大而全）vs Skills（小而精）的不同策略

## 参考资料

- [How to Use Matt Pocock's Skills for Claude Code](https://medium.com/@2315610426/how-to-use-matt-pococks-skills-for-claude-code-c79f4e3ca922)
- [Claude Code Skills by Matt Pocock (opentools.ai)](https://opentools.ai/resources/claude-code-skills)
- [Newsletter: AI Hero](https://www.aihero.dev/s/skills-newsletter)

---

*最后更新: 2026-05-26*
