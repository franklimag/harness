# obra/superpowers 学习计划指南

> 系统化的学习路径，从理解方法论到掌握 Subagent 模式，按依赖关系编排。

## 使用说明

本指南提供四个学习阶段，逐步深入 superpowers 的开发方法论和执行机制。

---

## 笔记依赖关系图

```
architecture.md (总览，入口)
        │
        ├── notes/01-workflow-methodology.md (六阶段工作流，核心)
        │       │
        │       └── notes/02-subagent-driven-development.md (依赖工作流理解)
        │
        ├── notes/03-skills-system.md (Skills 系统，独立)
        │
        └── notes/04-multi-harness-install.md (安装，独立)

changelog.md — 了解演进历史
references.md — 查阅原始资料
```

## 推荐阅读顺序

| 顺序 | 文件 | 原因 |
|------|------|------|
| 1 | `architecture.md` | 全局视角 |
| 2 | `notes/01-workflow-methodology.md` | 核心方法论 |
| 3 | `notes/02-subagent-driven-development.md` | 实现机制 |
| 4 | `notes/03-skills-system.md` | 技术实现 |
| 5 | `notes/04-multi-harness-install.md` | 实际使用 |

---

## 阶段一: 理解方法论（Day 1）

**目标**: 理解 superpowers 为什么是"方法论"而非"技能集合"。

| 任务 | 阅读材料 | 完成标准 |
|------|----------|----------|
| 理解定位 | `architecture.md` | 能解释方法论 vs 工具箱 |
| 六阶段概览 | `notes/01-workflow-methodology.md` | 能列出六个阶段 |
| 设计哲学 | `architecture.md` 第六节 | 理解"强制流程"的原因 |

**验证**: 能解释 "为什么 Agent 在没有强制流程时倾向跳过 brainstorm 和 review"。

---

## 阶段二: 掌握工作流细节（Day 2-3）

**目标**: 深入每个阶段的执行机制和产出要求。

| 任务 | 阅读材料 | 完成标准 |
|------|----------|----------|
| Brainstorm 细节 | `notes/01-workflow-methodology.md` | 理解"不写代码"原则 |
| Plan 原则 | `notes/01-workflow-methodology.md` | 理解 Junior-Friendly |
| Review 标准 | `notes/01-workflow-methodology.md` | 知道审查什么 |
| TDD/YAGNI/DRY | `architecture.md` | 三原则如何内置 |

**验证**: 能按六阶段工作流完成一个小功能开发。

---

## 阶段三: Subagent 驱动开发（Day 4）

**目标**: 理解 Subagent-Driven-Development 的执行机制。

| 任务 | 阅读材料 | 完成标准 |
|------|----------|----------|
| Subagent 概念 | `notes/02-subagent-driven-development.md` | 理解主/子 Agent 关系 |
| 任务分解 | `notes/02-subagent-driven-development.md` | 理解如何拆分任务 |
| 审查整合 | `notes/02-subagent-driven-development.md` | 知道如何验证 Subagent 产出 |

**验证**: 能解释 "Subagent 模式相比单一 Agent 串行执行的优势"。

---

## 阶段四: Skills 系统与安装（Day 5）

**目标**: 掌握 Skills 自动触发机制和跨 Harness 安装方式。

| 任务 | 阅读材料 | 完成标准 |
|------|----------|----------|
| 自动触发 | `notes/03-skills-system.md` | 理解触发逻辑 |
| SKILL.md 格式 | `notes/03-skills-system.md` | 能读懂 Skill 定义 |
| 多 Harness 安装 | `notes/04-multi-harness-install.md` | 知道 7+ 种安装方式 |
| agentskills.io | `references.md` | 了解标准规范 |

**验证**: 能在自己的 Harness 中安装并使用 superpowers。

---

## 难度与时间预估

| 阶段 | 难度 | 预计时间 |
|------|------|----------|
| 1 理解方法论 | ⭐ | 2 小时 |
| 2 工作流细节 | ⭐⭐ | 4 小时 |
| 3 Subagent | ⭐⭐⭐ | 3 小时 |
| 4 Skills 与安装 | ⭐⭐ | 2 小时 |

**总计**: 约 11 小时

---

## 与其他项目的学习交叉

| 概念 | superpowers | 对应的其他项目 |
|------|------------|---------------|
| 方法论流程 | 六阶段 | ECC: Operator Workflows |
| TDD | 内置原则 | mattpocock/skills: tdd Skill |
| Subagent | 子 Agent 执行 | hermes-agent: Subagent delegation |
| Auto-trigger | 自动激活 | ECC: auto_trigger in SKILL.md |
| 代码审查 | code-reviewer | ECC: code-reviewer Agent |

---

*最后更新: 2026-07*
