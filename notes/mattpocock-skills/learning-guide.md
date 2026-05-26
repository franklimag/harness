# mattpocock/skills 学习计划指南

> 系统化的学习路径，从理解设计哲学到掌握实践应用，按依赖关系编排。

## 使用说明

本指南提供四个学习阶段，逐步深入 mattpocock/skills 的设计哲学和实践方法。
建议按顺序完成，每个阶段都有明确的验证标准。

---

## 笔记依赖关系图

```
architecture.md (总览，入口)
        │
        ├── notes/01-skill-format.md (SKILL.md 格式，基础)
        │       │
        │       ├── notes/02-alignment-skills.md (对齐技能，依赖格式理解)
        │       │
        │       ├── notes/03-quality-skills.md (质量技能，依赖格式理解)
        │       │
        │       └── notes/04-workflow-skills.md (工作流技能，依赖格式理解)
        │
        └── 可以独立阅读

changelog.md — 了解演进历史
references.md — 深入时查阅原始资料
```

## 推荐阅读顺序

| 顺序 | 文件 | 原因 |
|------|------|------|
| 1 | `architecture.md` | 全局视角，理解"为什么这样设计" |
| 2 | `notes/01-skill-format.md` | 基础格式，后续所有内容的前提 |
| 3 | `notes/02-alignment-skills.md` | 最常用的对齐技能 |
| 4 | `notes/03-quality-skills.md` | 代码质量保障核心 |
| 5 | `notes/04-workflow-skills.md` | 完整工作流管理 |
| 参考 | `changelog.md` | 版本变化 |
| 参考 | `references.md` | 原始资料 |

---

## 阶段一: 理解设计哲学（Day 1）

**目标**: 理解 mattpocock/skills 为什么存在、解决什么问题、与其他系统有何不同。

| 任务 | 阅读材料 | 完成标准 |
|------|----------|----------|
| 理解定位 | `architecture.md` 第一节 | 能解释"纯 Markdown Skill"的含义 |
| 理解四大问题 | `architecture.md` 第六节 | 能列出 4 个核心问题及其解法 |
| 理解与 ECC 的区别 | `architecture.md` 第七节 | 能比较"小而精" vs "大而全" |

**验证**: 能用一段话解释 "为什么 Matt Pocock 选择纯 Markdown 而不是像 ECC 那样做复杂系统"。

**关键理解**:
- "Small, easy to adapt, composable" 不是偷懒，是刻意的架构决策
- 无运行时 = 零故障面、零维护成本、零部署复杂度
- 模型无关 = 不绑定任何供应商

---

## 阶段二: 掌握 SKILL.md 格式（Day 2）

**目标**: 深入理解 SKILL.md 的格式设计和渐进式披露模式。

| 任务 | 阅读材料 | 完成标准 |
|------|----------|----------|
| SKILL.md 结构 | `notes/01-skill-format.md` | 理解四层渐进式披露 |
| 与 ECC 格式对比 | `notes/01-skill-format.md` | 知道无 YAML 的设计原因 |
| skills.sh CLI | `architecture.md` 第三节 | 能使用安装/列出/移除命令 |

**验证**: 能自己编写一个简单的 SKILL.md 文件。

**关键理解**:
- Level 1 (标题) → Level 2 (步骤) → Level 3 (细节) → Level 4 (示例)
- Agent 按需读取，不需要一次性加载全部内容
- 格式的"不做什么"比"做什么"更重要

---

## 阶段三: 学习核心技能（Day 3-4）

**目标**: 深入理解对齐技能和质量技能的使用方法。

| 任务 | 阅读材料 | 完成标准 |
|------|----------|----------|
| grill-me 模式 | `notes/02-alignment-skills.md` | 理解编码前确认流程 |
| grill-with-docs | `notes/02-alignment-skills.md` | 理解文档验证方式 |
| CONTEXT.md | `notes/02-alignment-skills.md` | 能创建共享语言文件 |
| tdd 流程 | `notes/03-quality-skills.md` | 理解 Red-Green-Refactor |
| diagnose 方法 | `notes/03-quality-skills.md` | 掌握系统化诊断 |
| 架构改善 | `notes/03-quality-skills.md` | 理解增量改善策略 |

**验证**: 在实际项目中使用 grill-me + tdd 组合完成一个小功能。

**关键理解**:
- grill-me 是"慢就是快"的体现 — 花 2 分钟确认，省 20 分钟返工
- tdd 不是"写测试"，是"用测试驱动设计"
- diagnose 是系统化的，不是"试试看"

---

## 阶段四: 工作流与扩展（Day 5）

**目标**: 掌握工作流技能和自定义 Skill 的方法。

| 任务 | 阅读材料 | 完成标准 |
|------|----------|----------|
| to-prd 流程 | `notes/04-workflow-skills.md` | 理解需求文档化 |
| to-issues 拆分 | `notes/04-workflow-skills.md` | 掌握 Issue 拆分策略 |
| handoff 交接 | `notes/04-workflow-skills.md` | 理解跨会话传递 |
| prototype 原型 | `notes/04-workflow-skills.md` | 掌握快速验证方式 |
| write-a-skill | `notes/01-skill-format.md` | 能编写自定义 Skill |

**验证**: 使用 to-prd → to-issues → tdd 完成一个完整的小项目工作流。

**关键理解**:
- 从需求到代码的完整链路: grill-me → to-prd → to-issues → tdd → prototype
- handoff 解决了 Agent 的"金鱼记忆"问题
- write-a-skill 让系统可以持续进化

---

## 难度与时间预估

| 阶段 | 难度 | 预计时间 | 前置要求 |
|------|------|----------|----------|
| 1 设计哲学 | ⭐ | 1-2 小时 | 无 |
| 2 SKILL.md 格式 | ⭐ | 2-3 小时 | 阶段 1 |
| 3 核心技能 | ⭐⭐ | 3-4 小时 | 阶段 2 |
| 4 工作流与扩展 | ⭐⭐ | 3-4 小时 | 阶段 3 |

**总计**: 约 9-13 小时

---

## 实践练习

### 练习 1: 使用 grill-me 开发功能

```
1. 选择一个小功能需求
2. 安装 grill-me: npx skills@latest add mattpocock/skills/grill-me
3. 在 Agent 中启动: "请使用 grill-me 来确认我的需求"
4. 回答 Agent 的确认问题
5. 观察: Agent 是否更准确地理解了需求?
```

### 练习 2: TDD 循环

```
1. 安装 tdd: npx skills@latest add mattpocock/skills/tdd
2. 给 Agent 一个简单函数需求
3. 观察 Red-Green-Refactor 循环
4. 对比: 有 tdd Skill vs 无 tdd Skill 的代码质量
```

### 练习 3: 编写自定义 Skill

```
1. 安装 write-a-skill: npx skills@latest add mattpocock/skills/write-a-skill
2. 确定你想教 Agent 的一个行为模式
3. 使用 write-a-skill 指导编写
4. 测试: 新 Skill 是否有效指导 Agent?
```

---

## 与 ECC 学习的交叉参考

| 概念 | mattpocock/skills 中的体现 | ECC 中的对应 |
|------|---------------------------|-------------|
| 技能格式 | 纯 Markdown SKILL.md | YAML frontmatter SKILL.md |
| 触发机制 | Agent 手动读取 | auto_trigger + 事件 |
| 共享语言 | CONTEXT.md | Contexts (dev/review/research) |
| 安全 | git-guardrails | AgentShield (Red/Blue/Auditor) |
| Token 优化 | caveman (单 Skill) | 多策略组合 |
| 学习系统 | 无 | Instincts (CL v2) |
| 分发 | skills.sh CLI | Plugin Marketplace + npm |

---

## 进度追踪模板

```markdown
## 我的 mattpocock/skills 学习进度

### 阶段 1: 设计哲学 (开始日期: ___)
- [ ] 读完 architecture.md
- [ ] 理解四大核心问题
- [ ] 能解释与 ECC 的设计取舍

### 阶段 2: SKILL.md 格式 (开始日期: ___)
- [ ] 读完 01-skill-format.md
- [ ] 理解渐进式披露模式
- [ ] 能手写简单 SKILL.md

### 阶段 3: 核心技能 (开始日期: ___)
- [ ] 读完 02-alignment-skills.md
- [ ] 读完 03-quality-skills.md
- [ ] 实践过 grill-me + tdd

### 阶段 4: 工作流与扩展 (开始日期: ___)
- [ ] 读完 04-workflow-skills.md
- [ ] 实践过完整工作流
- [ ] 写过自定义 Skill
```

---

*最后更新: 2026-07*
