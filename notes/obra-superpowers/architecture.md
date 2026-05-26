# obra/superpowers 架构总览

> 基于 GitHub 仓库 (obra/superpowers) 和公开文档整理。最后更新: 2026-07

## 零、项目基本信息

| 属性 | 值 |
|------|-----|
| 仓库 | https://github.com/obra/superpowers |
| 作者 | Jesse Vincent (@obra) |
| Stars | 57K+ |
| 文档 | https://obra-superpowers.mintlify.app/ |
| PyPI | agent-superpowers |
| 标准 | agentskills.io |
| 支持 Harness | 7+ (Claude Code, Codex, Gemini, OpenCode, Cursor, Copilot CLI, Factory Droid) |
| 许可证 | MIT |

## 一、整体架构

### 核心定位

```
superpowers = 完整的软件开发方法论 (Methodology)
           ≠ 技能集合 (Skill Collection)
           ≠ 配置框架 (Configuration Framework)
           = "一套经过验证的、Agent 可执行的开发流程"
```

### 与其他系统的本质区别

| 项目 | 定位 | 比喻 |
|------|------|------|
| mattpocock/skills | 工具箱 | 各种螺丝刀（你挑着用） |
| ECC | 操作系统 | 完整的工作台（功能齐全） |
| **superpowers** | **方法论** | **施工手册（按步骤走）** |

### 架构层次图

```
┌─────────────────────────────────────────────────────────────┐
│                  方法论层 (Methodology)                       │
│                                                             │
│  Brainstorm → Specify → Plan → Implement → Review → Finish │
│                                                             │
│  核心: 不跳步骤，强制流程                                     │
└───────────────────────────────┬─────────────────────────────┘
                                │ 由 Skills 实现
                                ▼
┌─────────────────────────────────────────────────────────────┐
│                  Skills 层 (Execution)                        │
│                                                             │
│  brainstorming / subagent-driven-development / TDD          │
│  code-reviewer / systematic-debugging / writing-skills      │
│                                                             │
│  核心: 每个阶段由对应 Skill 自动触发                          │
└───────────────────────────────┬─────────────────────────────┘
                                │ 通过 agentskills.io 标准
                                ▼
┌─────────────────────────────────────────────────────────────┐
│                  安装层 (Distribution)                        │
│                                                             │
│  Plugin Marketplace / PyPI / 直接添加 SKILL.md               │
│  支持: Claude Code, Codex, Gemini, OpenCode, Cursor, etc.   │
│                                                             │
│  核心: 一次安装，任何 Harness 可用                            │
└─────────────────────────────────────────────────────────────┘
```

## 二、六阶段工作流架构

### 阶段 1: Brainstorming (头脑风暴)

```
用户: "我想做一个通知系统"
    │
    ▼
┌───────────────────────────────────────────────┐
│  superpowers Brainstorm                        │
│                                               │
│  ❌ 不做: 立刻开始写代码                       │
│  ✅ 做:                                       │
│    1. "你真正想解决什么问题？"                   │
│    2. "有哪些可能的方案？"                      │
│    3. "各方案的 trade-off 是什么？"            │
│    4. "最适合当前情况的方案是？"                 │
│                                               │
│  核心价值: 确保做对的事，而非把事做对           │
└───────────────────────────────────────────────┘
```

### 阶段 2: Specification (规格化)

```
┌───────────────────────────────────────────────┐
│  superpowers Specify                           │
│                                               │
│  输入: Brainstorm 的结论                       │
│  输出: 可消化的设计块                           │
│                                               │
│  关键: "Digestible Chunks"                     │
│    - 不是一个巨大的设计文档                     │
│    - 而是分成可以逐个理解的小块                  │
│    - 每块独立可验证                             │
└───────────────────────────────────────────────┘
```

### 阶段 3: Planning (规划)

```
┌───────────────────────────────────────────────┐
│  superpowers Plan                              │
│                                               │
│  输入: 设计规格                                │
│  输出: 实现计划                                │
│                                               │
│  原则:                                        │
│    - Junior-Engineer Friendly (新手可读)       │
│    - TDD (测试驱动)                            │
│    - YAGNI (不做多余的)                        │
│    - DRY (不重复)                              │
│                                               │
│  格式: 清晰的步骤列表，每步可独立执行           │
└───────────────────────────────────────────────┘
```

### 阶段 4: Implementation (实现)

```
┌───────────────────────────────────────────────┐
│  superpowers Implement                         │
│                                               │
│  方式: Subagent-Driven-Development             │
│                                               │
│  主 Agent (协调者)                             │
│    │                                          │
│    ├── Subagent 1: 实现任务 A                  │
│    ├── Subagent 2: 实现任务 B                  │
│    └── Subagent 3: 实现任务 C                  │
│                                               │
│  每个 Subagent 独立执行，遵循 TDD              │
└───────────────────────────────────────────────┘
```

### 阶段 5: Review (审查)

```
┌───────────────────────────────────────────────┐
│  superpowers Review                            │
│                                               │
│  检查内容:                                    │
│    - Subagent 的产出是否符合 Plan              │
│    - 代码质量是否达标                          │
│    - 测试是否覆盖                              │
│    - 是否有安全问题                            │
│                                               │
│  不通过 → 返回 Implement 修正                  │
│  通过 → 进入 Finish                            │
└───────────────────────────────────────────────┘
```

### 阶段 6: Finish (完成)

```
┌───────────────────────────────────────────────┐
│  superpowers Finish                            │
│                                               │
│  动作:                                        │
│    - 完成当前分支                              │
│    - 清理临时文件                              │
│    - 生成 commit message                      │
│    - 准备 PR/MR                               │
│    - 可选: 生成变更日志                        │
└───────────────────────────────────────────────┘
```

## 三、Subagent-Driven-Development

### 核心概念

```
传统 Agent 工作方式:
  一个 Agent → 按顺序做所有事 → 长会话 → 上下文溢出

superpowers Subagent 方式:
  主 Agent (协调者)
    ├── 分解任务
    ├── 为每个任务创建 Subagent
    ├── Subagent 独立执行
    ├── 主 Agent 审查结果
    └── 整合产出
```

### 优势

| 维度 | 传统单 Agent | Subagent 驱动 |
|------|-------------|--------------|
| 上下文 | 溢出风险高 | 每个 Subagent 上下文独立 |
| 专注度 | 注意力分散 | 每个 Agent 专注一件事 |
| 失败隔离 | 一个错误影响全部 | 一个 Subagent 失败不影响其他 |
| 并行性 | 串行执行 | 理论上可并行 |
| 可审查 | 大块产出难审查 | 小块产出易审查 |

### Subagent 生命周期

```
主 Agent 决定需要 Subagent
    │
    ├── 1. 创建: 给出明确任务描述和约束
    │
    ├── 2. 执行: Subagent 独立工作
    │       ├── 遵循 TDD
    │       ├── 只做分配的任务
    │       └── 完成后报告结果
    │
    ├── 3. 审查: 主 Agent 检查产出
    │       ├── 代码质量
    │       ├── 测试通过
    │       └── 符合计划
    │
    └── 4. 整合: 将 Subagent 产出合并
```

## 四、Skills 系统

### 自动触发机制

superpowers 的 Skills 基于上下文**自动触发**，不需要用户显式调用：

```
触发逻辑:

if (用户提出新想法) → 自动激活 brainstorming
if (brainstorm 完成) → 自动进入 specification
if (specification 完成) → 自动进入 planning
if (plan 完成) → 自动进入 implementation (subagent)
if (implementation 完成) → 自动进入 review
if (review 通过) → 自动进入 finish
```

### Skills 列表

| Skill | 触发阶段 | 功能 |
|-------|---------|------|
| brainstorming | 阶段 1 | 引导探索问题和解决方案 |
| subagent-driven-development | 阶段 4 | 子 Agent 执行实现 |
| TDD | 阶段 4 (内嵌) | 测试驱动开发 |
| code-reviewer | 阶段 5 | 代码审查 |
| systematic-debugging | 任意 | 系统化问题诊断 |
| writing-skills | 元技能 | 编写新 Skill |

### SKILL.md 格式 (agentskills.io 标准)

```markdown
# Brainstorming

## Purpose
Guide the agent through structured exploration before jumping to code.

## When to Activate
- User proposes a new feature or system
- User asks "how should we..." or "what's the best way to..."
- Beginning of a development session

## Workflow
1. Ask clarifying questions about the real goal
2. Explore multiple approaches
3. Discuss trade-offs
4. Arrive at a recommended approach
5. Get user confirmation before proceeding

## Constraints
- Do NOT write any code during this phase
- Do NOT skip to implementation
- Ensure user explicitly agrees with the approach
```

## 五、安装架构

### 多 Harness 安装方式

| Harness | 安装方式 |
|---------|---------|
| Claude Code | `/plugin install superpowers@claude-plugins-official` |
| Codex CLI | 配置文件添加 SKILL.md 路径 |
| Gemini CLI | 配置文件映射 |
| OpenCode | Plugin 系统 |
| Cursor | 扩展/配置 |
| GitHub Copilot CLI | 配置添加 |
| Factory Droid | 原生支持 |

### agentskills.io 标准

superpowers 遵循 agentskills.io 社区标准，确保跨 Harness 兼容性：

```
agentskills.io 标准:
├── 定义: SKILL.md 文件格式
├── 发现: Skills 注册和搜索
├── 安装: 统一安装协议
└── 执行: Agent 如何读取和执行 Skills
```

## 六、设计哲学

### 为什么是"方法论"

```
工具论 (Tool Philosophy):
  "给你工具，怎么用你自己决定"
  优点: 灵活
  缺点: 可能用错、可能漏步骤

方法论 (Methodology Philosophy):
  "给你流程，按步骤走"
  优点: 一致性高、质量有保障
  缺点: 灵活性较低
  
superpowers 的选择: 方法论
理由: Agent 在无约束时倾向于跳过重要步骤 (如 brainstorm, review)
```

### 三大内置原则

| 原则 | 全称 | 在 superpowers 中的体现 |
|------|------|----------------------|
| TDD | Test-Driven Development | Implementation 阶段必须先写测试 |
| YAGNI | You Aren't Gonna Need It | Plan 阶段不设计"将来可能需要的" |
| DRY | Don't Repeat Yourself | Review 阶段检查重复代码 |

## 七、待深入研究

- [ ] Subagent 的具体创建和通信机制
- [ ] 自动触发的匹配算法细节
- [ ] Review 阶段的评判标准
- [ ] 与 claude-mem 记忆系统的集成可能
- [ ] Factory Droid 的原生支持实现
- [ ] agentskills.io 标准的完整规范

---

*参考来源见 [references.md](./references.md)*
*最后更新: 2026-07*
