# ECC 学习计划指南

> 系统化的学习路径，从理解概念到掌握跨 Harness 设计，按依赖关系编排。

## 使用说明

本指南提供六个学习阶段，逐步深入 ECC 的核心架构和设计哲学。
建议按顺序完成，每个阶段都有明确的验证标准。

---

## 笔记依赖关系图

```
architecture.md (总览，入口)
        │
        ├── notes/01-skills-system.md (Skills 工作流表面)
        │       │
        │       └── notes/03-instincts-learning.md (CL v2, 依赖 Skills 理解)
        │
        ├── notes/02-hooks-architecture.md (Hook 系统)
        │       │
        │       └── notes/04-cross-harness.md (跨 Harness, 依赖 Hook 理解)
        │
        ├── notes/05-security-agentshield.md (安全, 横切所有组件)
        │
        └── notes/06-token-optimization.md (优化, 综合所有知识)

changelog.md — 随时参考版本变化
references.md — 深入某话题时查阅原始资料
```

## 推荐阅读顺序

| 顺序 | 文件 | 原因 |
|------|------|------|
| 1 | `architecture.md` | 全局视角，建立心智模型 |
| 2 | `notes/01-skills-system.md` | 主要工作流表面，最常用组件 |
| 3 | `notes/02-hooks-architecture.md` | 自动化核心，理解事件驱动 |
| 4 | `notes/03-instincts-learning.md` | 独创学习系统，核心差异化 |
| 5 | `notes/04-cross-harness.md` | 跨工具设计，架构哲学 |
| 6 | `notes/05-security-agentshield.md` | 安全管线，生产级保障 |
| 7 | `notes/06-token-optimization.md` | 成本控制，实用技巧 |
| 参考 | `changelog.md` | 版本变化追踪 |
| 参考 | `references.md` | 原始资料查阅 |

---

## 阶段一: 理解核心概念（Day 1）

**目标**: 理解 ECC 是什么、解决什么问题、六大构建块的关系。

| 任务 | 阅读材料 | 完成标准 |
|------|----------|----------|
| 了解 ECC 定位 | `architecture.md` 第一节 | 能用一句话描述 ECC |
| 理解六大构建块 | `architecture.md` 第二节 | 能列出 Agents/Skills/Commands/Hooks/Rules/Instincts 的关系 |
| 理解安装方式 | `architecture.md` 第四节 | 知道 Plugin/npm/Selective 三种安装方式 |

**验证**: 能向他人解释 "ECC 与普通 Claude Code 配置有什么本质区别"。

**关键理解**:
- ECC 不是独立 Agent，而是增强现有工具的配置框架
- Plugin-first 理念：通过 Marketplace 一键安装
- 六大构建块各司其职，共同组成完整系统

---

## 阶段二: 深入组件系统（Day 2-3）

**目标**: 深入理解 Skills 和 Hooks 这两个最核心的组件。

| 任务 | 阅读材料 | 完成标准 |
|------|----------|----------|
| Skills 系统 | `notes/01-skills-system.md` | 理解 SKILL.md 格式和 246 个技能的分类 |
| Skills vs Commands | `notes/01-skills-system.md` | 理解为什么 Skills 是主要工作流表面 |
| Hook 事件类型 | `notes/02-hooks-architecture.md` | 知道 8/15/11 事件类型的区别 |
| Hook 运行时控制 | `notes/02-hooks-architecture.md` | 理解 ECC_HOOK_PROFILE 等配置 |

**验证**: 能解释 "一个 Skill 从触发到执行经历了哪些步骤"。

**关键理解**:
- Skills 是声明式的工作流（YAML frontmatter + Markdown 指令）
- Hooks 是命令式的自动化（Node.js 脚本 + 事件触发）
- 两者互补：Skills 定义"做什么"，Hooks 控制"何时做"

---

## 阶段三: 学习 Instincts 系统（Day 4）

**目标**: 理解 Continuous Learning v2 的运作机制，这是 ECC 最独创的设计。

| 任务 | 阅读材料 | 完成标准 |
|------|----------|----------|
| Instinct 生命周期 | `notes/03-instincts-learning.md` | 理解 观察→创建→评分→进化 |
| 置信度评分 | `notes/03-instincts-learning.md` | 知道评分依据和阈值 |
| 项目作用域 | `notes/03-instincts-learning.md` | 理解为什么要项目级隔离 |
| 进化路径 | `notes/03-instincts-learning.md` | 知道 Instinct 如何变成 Skill/Command/Agent |

**验证**: 能解释 "ECC 如何从你的工作习惯中自动学习并进化"。

**关键理解**:
- Instinct 是原子级别的行为模式
- 项目隔离防止"Python 项目的习惯"污染"Go 项目"
- 高置信度 Instinct 会自动升级为正式组件

---

## 阶段四: 跨 Harness 设计（Day 5）

**目标**: 理解 ECC 如何做到"一套配置，十个工具"。

| 任务 | 阅读材料 | 完成标准 |
|------|----------|----------|
| DRY Adapter Pattern | `notes/04-cross-harness.md` | 理解 Cursor 如何复用 Claude Code 脚本 |
| AGENTS.md 通用文件 | `notes/04-cross-harness.md` | 知道 AGENTS.md 的跨工具角色 |
| 适配级别 | `notes/04-cross-harness.md` | 区分 Primary/Full/Supported 三级 |
| 功能对等表 | `notes/04-cross-harness.md` | 了解各 Harness 的能力差异 |

**验证**: 能解释 "如果要新增一个 Harness 支持，需要实现什么"。

**关键理解**:
- DRY 原则：不为每个工具重写，而是通过适配器复用
- AGENTS.md 是所有工具都能识别的"通用入口"
- 不同工具的 Hook 事件数不同，需要映射

---

## 阶段五: Token 优化策略（Day 6）

**目标**: 掌握 ECC 的成本控制方法，这是实际使用中最关键的知识。

| 任务 | 阅读材料 | 完成标准 |
|------|----------|----------|
| 模型选择策略 | `notes/06-token-optimization.md` | 理解 sonnet 60% 降本 |
| 思考 Token 控制 | `notes/06-token-optimization.md` | 理解 MAX_THINKING_TOKENS 的作用 |
| 压缩策略 | `notes/06-token-optimization.md` | 理解 AUTOCOMPACT 和逻辑断点压缩 |
| MCP/工具管理 | `notes/06-token-optimization.md` | 知道 ≤10 MCPs, ≤80 tools 的原因 |

**验证**: 能计算 "使用 ECC 优化配置后，相比默认设置节省多少成本"。

**关键理解**:
- 模型降级 (sonnet) 是最大的单项优化
- 思考 Token 是隐藏成本大头
- MCP/工具数量直接影响每次请求的 token 消耗

---

## 阶段六: ECC 2.0 与 Rust 控制平面（Day 7）

**目标**: 了解 ECC 2.0 的架构方向和 Rust 控制平面的设计。

| 任务 | 阅读材料 | 完成标准 |
|------|----------|----------|
| 控制平面架构 | `architecture.md` 第八节 | 理解 Daemon 模式 |
| CLI 命令 | `architecture.md` 第八节 | 知道 dashboard/start/sessions 等命令 |
| 安全管线 | `notes/05-security-agentshield.md` | 理解 Red/Blue/Auditor 三阶段 |
| 未来方向 | `changelog.md` | 了解从 v1.2 到 v2.0 的演进趋势 |

**验证**: 能解释 "ECC 2.0 的 Rust 控制平面解决了什么问题"。

**关键理解**:
- 从"配置集合"进化为"配置 + 运行时管理"
- Rust 提供性能和可靠性保障
- Dashboard GUI 降低使用门槛

---

## 难度与时间预估

| 阶段 | 难度 | 预计时间 | 前置要求 |
|------|------|----------|----------|
| 1 核心概念 | ⭐ | 2-3 小时 | 无 |
| 2 组件系统 | ⭐⭐ | 4-5 小时 | 阶段 1 |
| 3 Instincts | ⭐⭐⭐ | 3-4 小时 | 阶段 2 |
| 4 跨 Harness | ⭐⭐ | 3-4 小时 | 阶段 2 |
| 5 Token 优化 | ⭐⭐ | 2-3 小时 | 阶段 1-2 |
| 6 ECC 2.0 | ⭐⭐⭐ | 3-4 小时 | 阶段 1-5 |

**总计**: 约 17-23 小时

---

## 学习过程中的关键决策点

| 决策点 | 时机 | 选项 | 建议 |
|--------|------|------|------|
| 安装方式 | 阶段 1 | Plugin / npm / Selective | 初学用 Plugin，定制用 Selective |
| Hook Profile | 阶段 2 | dev / prod / minimal | 学习时 dev，生产用 prod |
| 学习隔离 | 阶段 3 | 全局 / 项目级 | 始终选项目级 |
| Harness 选择 | 阶段 4 | Claude Code / Cursor / 其他 | 先掌握 Claude Code |
| 模型选择 | 阶段 5 | opus / sonnet / haiku | 日常用 sonnet，复杂任务 opus |
| 控制平面 | 阶段 6 | 使用 / 不使用 Rust CLI | 体验新功能，但可保持旧方式 |

---

## 进度追踪模板

```markdown
## 我的 ECC 学习进度

### 阶段 1: 核心概念 (开始日期: ___)
- [ ] 读完 architecture.md
- [ ] 理解六大构建块
- [ ] 能解释 ECC 定位

### 阶段 2: 组件系统 (开始日期: ___)
- [ ] 读完 01-skills-system.md
- [ ] 读完 02-hooks-architecture.md
- [ ] 理解 Skills vs Commands vs Hooks

### 阶段 3: Instincts (开始日期: ___)
- [ ] 读完 03-instincts-learning.md
- [ ] 理解置信度评分
- [ ] 理解进化路径

### 阶段 4: 跨 Harness (开始日期: ___)
- [ ] 读完 04-cross-harness.md
- [ ] 理解 DRY Adapter Pattern
- [ ] 了解各 Harness 功能对等

### 阶段 5: Token 优化 (开始日期: ___)
- [ ] 读完 06-token-optimization.md
- [ ] 掌握成本控制配置
- [ ] 理解 MCP/工具管理

### 阶段 6: ECC 2.0 (开始日期: ___)
- [ ] 读完 05-security-agentshield.md
- [ ] 理解 Rust 控制平面
- [ ] 了解版本演进趋势
```

---

## 补充资源

### 每个阶段的推荐外部阅读

| 阶段 | 推荐资源 |
|------|----------|
| 1 | ECC GitHub README |
| 2 | Claude Code 官方 Skills 文档 |
| 3 | 仓库内 continuous-learning-v2 目录 |
| 4 | 各 Harness 的官方配置文档 |
| 5 | Anthropic API 定价页面 |
| 6 | ECC 2.0 Rust 源码目录 |

### 与其他项目对比学习

| 对比维度 | ECC | OpenClaw | mattpocock/skills |
|----------|-----|----------|-------------------|
| 定位 | Harness 配置框架 | 自托管 Agent | Skills 集合 |
| 学习系统 | Instincts (CL v2) | Memory/SOUL | 无 |
| 跨工具 | 10+ Harness | 50+ Channels | Claude Code only |
| 安全 | AgentShield | Exec Policy | 无 |
| 规模 | 246 skills, 61 agents | 1200+ skills | ~50 skills |

---

*最后更新: 2026-07*
