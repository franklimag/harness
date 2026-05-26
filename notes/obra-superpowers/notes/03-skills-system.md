# Skills 系统学习笔记

> superpowers 的 Skills 自动触发机制、SKILL.md 格式与 agentskills.io 标准。

## 概述

superpowers 的 Skills 系统与一般的 Skill 集合最大的区别是：**自动触发 (Auto-trigger)**。Skills 不需要用户显式调用，而是基于当前上下文和工作流阶段自动激活。

### 核心特征

| 特征 | superpowers Skills | 其他 Skills |
|------|-------------------|------------|
| 触发方式 | 自动（上下文驱动） | 手动（用户调用） |
| 阶段绑定 | 绑定到六阶段工作流 | 独立使用 |
| 流程约束 | 强制按顺序 | 自由组合 |
| 标准 | agentskills.io | 各自格式 |

## 自动触发机制

### 触发逻辑

```
Agent 观察当前上下文
    │
    ├── 用户提出新想法/需求?
    │       → 触发: brainstorming
    │
    ├── brainstorm 完成 (用户确认方向)?
    │       → 触发: specification
    │
    ├── specification 完成 (设计块确认)?
    │       → 触发: planning
    │
    ├── plan 完成 (步骤列表确认)?
    │       → 触发: subagent-driven-development
    │
    ├── implementation 完成 (所有 task done)?
    │       → 触发: code-reviewer
    │
    └── review 通过?
            → 触发: finish 流程
```

### 触发信号

| 信号类型 | 示例 | 触发的 Skill |
|---------|------|-------------|
| 用户意图 | "我想做一个..." | brainstorming |
| 阶段完成 | 上一阶段产出确认 | 下一阶段对应 Skill |
| 问题发生 | 测试失败/错误 | systematic-debugging |
| 显式请求 | "帮我写个 Skill" | writing-skills |

## SKILL.md 格式 (agentskills.io 标准)

### 标准结构

```markdown
# [Skill Name]

## Purpose
[为什么存在这个 Skill]

## When to Activate
[触发条件列表]

## Workflow
[步骤化的执行流程]

## Constraints
[必须遵守的约束]

## Examples (可选)
[示例场景]
```

### 与 mattpocock/skills 格式对比

| 维度 | superpowers | mattpocock/skills |
|------|------------|-------------------|
| 标题 | Skill Name | Skill Name |
| 目的说明 | Purpose 章节 | > 一句话描述 |
| 触发条件 | When to Activate | 何时使用 |
| 流程 | Workflow (编号步骤) | 流程 (编号步骤) |
| 约束 | Constraints | 关键原则 |
| 反模式 | — | 避免的做法 |

### 关键 Skills 定义

#### brainstorming

```markdown
# Brainstorming

## Purpose
Ensure we understand the real problem before jumping to solutions.
Prevent premature implementation by exploring the problem space first.

## When to Activate
- User proposes a new feature
- User describes a problem to solve
- Start of a new development task
- User says "how should we..." or "what approach..."

## Workflow
1. Ask "what are you really trying to accomplish?"
2. Explore the problem space (not solution space)
3. Generate multiple possible approaches
4. Discuss trade-offs of each approach
5. Get explicit user confirmation on chosen direction

## Constraints
- DO NOT write any code
- DO NOT make technical decisions
- DO NOT skip to implementation
- MUST get explicit user agreement before proceeding
```

#### subagent-driven-development

```markdown
# Subagent-Driven-Development

## Purpose
Execute implementation tasks through focused sub-agents,
each with isolated context and clear deliverables.

## When to Activate
- Implementation phase begins
- Plan has been approved
- Tasks are ready for execution

## Workflow
1. Read the approved plan
2. Identify tasks and dependencies
3. For each task:
   a. Create subagent with task description
   b. Include necessary context (files, specs)
   c. Specify TDD requirement
   d. Define acceptance criteria
4. Review each subagent's output
5. Integrate approved outputs

## Constraints
- Each subagent MUST follow TDD
- Subagent scope MUST not exceed assigned task
- Failed subagent output MUST be rejected and redone
- Dependencies MUST be respected (no parallel when dependent)
```

## agentskills.io 标准

### 标准定义

agentskills.io 是一个社区标准，定义了:

| 方面 | 规范内容 |
|------|---------|
| 文件格式 | SKILL.md 的结构和必要章节 |
| 注册 | 如何发布和发现 Skills |
| 安装 | 统一的安装协议 |
| 触发 | Agent 如何识别和激活 Skills |
| 兼容 | 跨 Harness 兼容性要求 |

### 标准核心原则

```
1. 可移植性 (Portability)
   — Skill 在任何符合标准的 Harness 中都能工作

2. 自描述性 (Self-describing)
   — Skill 文件本身包含所有必要信息

3. 无运行时 (No Runtime)
   — Skill 是纯文本指令，不需要执行环境

4. 渐进式 (Progressive)
   — 简单 Skill 很容易写，复杂 Skill 有空间表达
```

## Skills 的协同模式

### 链式触发

```
brainstorming
    │ (完成，用户确认)
    ▼
specification
    │ (完成，用户确认)
    ▼
planning
    │ (完成，用户确认)
    ▼
subagent-driven-development
    │ (完成，所有 task done)
    ▼
code-reviewer
    │ (通过)
    ▼
finish
```

### 插入式触发

```
任何阶段中遇到问题:
    │
    └── systematic-debugging 介入
            │
            └── 问题解决后回到原阶段
```

## 问题与思考

- [x] 自动触发如何工作? → 上下文匹配 + 阶段完成信号
- [x] 与 agentskills.io 的关系? → superpowers 遵循并推动该标准
- [ ] 自动触发的误触发如何处理?
- [ ] 能否自定义触发逻辑?
- [ ] 第三方 Skills 如何集成到 superpowers 流程?

---

*参考: [architecture.md](../architecture.md) | [01-workflow-methodology.md](./01-workflow-methodology.md)*
