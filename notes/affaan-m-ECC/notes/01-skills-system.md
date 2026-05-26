# Skills 系统学习笔记

> ECC 的主要工作流表面 — 246 个声明式技能定义 Agent 行为模式。

## 系统概述

Skills 是 ECC 中最核心的组件，定义了 Agent **"做什么"和"怎么做"**。每个 Skill 是一个 `SKILL.md` 文件，通过 YAML frontmatter 声明元数据，Markdown 正文描述工作流程。

### 核心数据

| 属性 | 值 |
|------|-----|
| 总数量 | 246 个 |
| 格式 | SKILL.md (YAML frontmatter + Markdown) |
| 触发方式 | 自动触发 / 手动调用 / 命令关联 |
| 与 Commands 关系 | Skills 是 Commands 的超集和替代方向 |
| 与 Agents 关系 | Agent 可以引用和执行 Skills |

## SKILL.md 格式详解

### 基础结构

```markdown
---
name: tdd-workflow
version: 2.0.0
category: development
description: Test-Driven Development workflow guidance
auto_trigger: true
triggers:
  - new feature request
  - bug fix
  - refactoring task
model: sonnet
tools:
  - read_file
  - write_file
  - execute_command
tags:
  - testing
  - development
  - best-practice
---

# TDD Workflow

## 概述
指导 Agent 按照 TDD (Red-Green-Refactor) 流程工作。

## 触发条件
- 收到新功能需求
- 收到 bug 修复请求
- 重构任务

## 工作流程

### Step 1: Red (编写失败测试)
1. 分析需求
2. 编写最小测试用例
3. 运行测试确认失败

### Step 2: Green (实现最少代码)
1. 编写最少代码通过测试
2. 不做过度设计
3. 运行测试确认通过

### Step 3: Refactor (重构优化)
1. 清理代码
2. 消除重复
3. 改善命名
4. 确认测试仍通过

## 注意事项
- 每次只测试一个行为
- 测试名称要描述预期行为
- 不要在测试中使用 mock 过多
```

### Frontmatter 字段

| 字段 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `name` | string | ✅ | 技能唯一标识 |
| `version` | string | ✅ | 语义化版本号 |
| `category` | string | ✅ | 所属分类 |
| `description` | string | ✅ | 简短描述 |
| `auto_trigger` | boolean | ❌ | 是否自动触发 |
| `triggers` | array | ❌ | 触发条件列表 |
| `model` | string | ❌ | 推荐使用的模型 |
| `tools` | array | ❌ | 需要的工具列表 |
| `tags` | array | ❌ | 标签分类 |

## 技能分类体系

### 按功能分类

| 类别 | 示例技能 | 约数量 | 说明 |
|------|---------|--------|------|
| coding-standards | code-style, naming-conventions, documentation | ~30 | 编码规范 |
| backend-patterns | api-design, database-patterns, caching | ~25 | 后端模式 |
| frontend-patterns | component-design, state-management, styling | ~25 | 前端模式 |
| continuous-learning-v2 | instinct-creation, pattern-detection, evolution | ~15 | 持续学习 |
| tdd-workflow | red-green-refactor, test-design, mocking | ~20 | 测试驱动 |
| security-review | vulnerability-scan, dependency-check, auth-review | ~15 | 安全审查 |
| eval-harness | benchmark, regression-test, performance-check | ~10 | 评估测试 |
| git-workflow | commit-message, branch-strategy, pr-review | ~15 | Git 工作流 |
| refactoring | extract-method, rename, decompose | ~15 | 重构技术 |
| debugging | error-diagnosis, log-analysis, bisect | ~10 | 调试技巧 |
| architecture | design-patterns, system-design, microservices | ~15 | 架构设计 |
| deployment | ci-cd, docker, monitoring | ~10 | 部署相关 |
| 其他 | — | ~41 | 各类专项 |

### 按语言/框架分类

| 语言/框架 | 相关 Skills |
|-----------|------------|
| TypeScript/JavaScript | ~40 |
| Python | ~25 |
| Go | ~15 |
| Java/Spring Boot | ~15 |
| Swift | ~10 |
| PHP | ~10 |
| Django | ~10 |
| React/Next.js | ~15 |
| ArkTS (HarmonyOS) | ~5 |

## 触发机制

### 自动触发 (auto_trigger: true)

当 Agent 检测到匹配的触发条件时，自动激活对应 Skill：

```
用户请求 "添加用户登录功能"
    │
    ▼
匹配 triggers: ["new feature request"]
    │
    ▼
自动激活: tdd-workflow + security-review + backend-patterns
```

### 手动调用

通过关联的 Command 或直接引用：

```
/tdd           → 触发 tdd-workflow skill
/code-review   → 触发 code-review skill
/security-scan → 触发 security-review skill
```

### 组合触发

多个 Skills 可以协同工作：

```
新功能请求
    │
    ├── tdd-workflow (测试先行)
    ├── coding-standards (代码规范)
    ├── backend-patterns (后端模式)
    └── security-review (安全检查)
```

## Skills vs Commands

| 维度 | Skills | Commands |
|------|--------|----------|
| 数量 | 246 | 76 |
| 定义 | SKILL.md (声明式) | 命令定义文件 |
| 触发 | 自动 + 手动 | 仅手动 (斜杠) |
| 内容 | 完整工作流 + 指令 | 简短命令逻辑 |
| 进化方向 | 主要表面 (未来) | 逐步迁移到 Skills |
| 灵活性 | 高 (条件触发, 组合) | 低 (单一命令) |

**趋势**: Commands 正在逐步迁移到 Skills，Skills 是 ECC 的未来主要工作流表面。

## Skills 与其他组件的关系

```
┌─────────────────────────────────────────────────┐
│                    Skills (246)                   │
│         主要工作流表面 / 声明式指令               │
└─────────────────────────┬───────────────────────┘
                          │
         ┌────────────────┼────────────────┐
         ▼                ▼                ▼
   ┌──────────┐    ┌──────────┐    ┌──────────┐
   │  Agents  │    │  Hooks   │    │ Instincts│
   │  (执行)   │    │  (触发)   │    │  (进化源) │
   │ 61 agents │    │ 8 events │    │  CL v2   │
   └──────────┘    └──────────┘    └──────────┘
         │                                ▲
         │                                │
         └────────── Rules (约束) ─────────┘
                    34 条规则
```

## 实践示例

### 示例 1: coding-standards Skill

```markdown
---
name: typescript-conventions
version: 1.5.0
category: coding-standards
auto_trigger: true
triggers:
  - typescript file creation
  - code review
tags:
  - typescript
  - style
---

# TypeScript 编码约定

## 命名规范
- 接口: PascalCase, 不加 I 前缀
- 类型: PascalCase
- 变量/函数: camelCase
- 常量: UPPER_SNAKE_CASE
- 文件: kebab-case

## 类型安全
- 禁止使用 `any`，使用 `unknown` 替代
- 优先使用 `interface` 而非 `type`
- 使用 strict mode
...
```

### 示例 2: security-review Skill

```markdown
---
name: dependency-security-check
version: 2.0.0
category: security-review
auto_trigger: true
triggers:
  - package.json change
  - requirements.txt change
  - go.mod change
model: sonnet
---

# 依赖安全检查

## 触发
当检测到依赖文件变更时自动执行。

## 检查项
1. 已知漏洞 (CVE 扫描)
2. 过期依赖
3. 许可证兼容性
4. 供应链风险
...
```

## 问题与思考

- [x] Skills 的格式? → SKILL.md with YAML frontmatter
- [x] 触发方式? → auto_trigger + 手动命令 + 组合
- [x] 与 Commands 的关系? → Skills 是超集，Commands 在迁移中
- [ ] Skills 之间的优先级冲突如何解决?
- [ ] 自定义 Skill 的调试方法?
- [ ] Skills 数量增长对 token 消耗的影响?
- [ ] auto_trigger 的匹配算法细节?

## 学习记录

| 日期 | 内容 | 来源 |
|------|------|------|
| 2026-07 | 初始整理: Skills 系统架构、格式、分类、触发机制 | GitHub README + 仓库结构分析 |

---

*参考: [architecture.md](../architecture.md) | [references.md](../references.md)*
