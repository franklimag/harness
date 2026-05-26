# Kiro Specs vs Superpowers 对照分析

> 两种"结构化 Agent 开发"方案的对比 — 辅助理解 Spec-Driven Development 的通用模式。

## 为什么做这个对比

Kiro Specs 和 obra/superpowers 解决同一个核心问题：**让 Agent 不要跳过设计直接写代码**。
但它们的实现层面完全不同：

- **Kiro Specs** = IDE 原生机制（平台级别）
- **Superpowers** = Agent Skills 层（配置级别）

对照学习有助于提炼"结构化 Agent 开发"的**通用模式**，不被具体工具绑定。

---

## 一、核心理念对比

| 维度 | Kiro Specs | Superpowers |
|------|-----------|-------------|
| 本质 | IDE 内置的结构化开发工作流 | Agent Skill 层的开发方法论 |
| 哲学 | "先规格化再编码" (Spec-First) | "先对话再动手" (Brainstorm-First) |
| 绑定 | Kiro IDE 专属 | 跨 8+ Harness 可移植 |
| 格式 | EARS notation (结构化需求语言) | 自然语言 Markdown |
| 驱动方式 | IDE UI 面板 + 文件 | Skills 自动触发 + 子 Agent |
| 产出 | .kiro/specs/ 目录下的文件集 | 代码 + 测试 + commit |
| 人的角色 | 每阶段 checkpoint 确认 | 签署 spec 后可长时间自主 |

---

## 二、流程阶段映射

```
Kiro Specs                              Superpowers
═══════════════════════════════════════════════════════════════

┌─────────────────────┐            ┌─────────────────────┐
│ 1. Requirements     │            │ 1. Brainstorming    │
│    (EARS 记法)       │     ←→     │    (开放对话)        │
│    结构化需求描述     │            │    "你真正想做什么?" │
└──────────┬──────────┘            └──────────┬──────────┘
           │                                  │
           ▼                                  ▼
┌─────────────────────┐            ┌─────────────────────┐
│ 2. Design           │            │ 2. Specification    │
│    (设计文档)        │     ←→     │    (分段确认设计)    │
│    技术方案 + 接口    │            │    分块展示让你消化  │
└──────────┬──────────┘            └──────────┬──────────┘
           │                                  │
           ▼                                  ▼
┌─────────────────────┐            ┌─────────────────────┐
│ 3. Tasks            │            │ 3. Planning         │
│    (任务清单)        │     ←→     │    (实现计划)        │
│    checkbox 逐项勾选 │            │    TDD + YAGNI + DRY│
└──────────┬──────────┘            └──────────┬──────────┘
           │                                  │
           ▼                                  ▼
┌─────────────────────┐            ┌─────────────────────┐
│ 4. (Agent 执行任务)  │            │ 4. Implementation   │
│    IDE 驱动          │     ←→     │    Subagent-Driven  │
│    逐 task 执行      │            │    子 Agent 并行执行  │
└──────────┬──────────┘            └──────────┬──────────┘
           │                                  │
           ▼                                  ▼
┌─────────────────────┐            ┌─────────────────────┐
│ 5. (完成/验证)       │            │ 5. Review + Finish  │
│    task checkbox ✅   │     ←→     │    代码审查 + 合并    │
└─────────────────────┘            └─────────────────────┘
```

### 阶段差异细节

| 阶段 | Kiro Specs 特点 | Superpowers 特点 |
|------|----------------|-----------------|
| 需求/脑暴 | EARS notation（结构化："When [condition], the system shall..."） | 自然对话（Agent 反问"你真正想做什么？"） |
| 设计 | 生成 design.md 文件，包含技术方案 | 分块展示设计，每块让你确认后再继续 |
| 任务 | 生成 tasks.md，每项是一个 checkbox | 生成"热情初级工程师能执行的"实现计划 |
| 执行 | IDE 面板中逐 task 点击执行 | 子 Agent 自主执行，可连续工作数小时 |
| 验证 | Task 完成后自动勾选 | Code review skill 检查 + TDD 保障 |

---

## 三、执行方式差异

### Kiro Specs: IDE 原生驱动

```
用户 → Kiro IDE 面板 → 选择 "Create Spec"
                          │
                          ▼
                   生成 .kiro/specs/<feature>/
                   ├── requirements.md  (EARS)
                   ├── design.md        (技术方案)
                   └── tasks.md         (任务清单)
                          │
                          ▼ (用户确认每阶段)
                   Agent 按 tasks.md 逐项执行
                          │
                          ▼
                   Hooks 自动触发验证
```

**特点：** 文件即状态，IDE 面板即控制面，用户全程可视化追踪。

### Superpowers: Skill 自动触发 + 子 Agent

```
用户 → "我要做 XXX"
          │
          ▼ (Skill 自动检测到任务)
   brainstorming skill 激活
          │
          ▼ (Agent 发问、用户回答)
   spec 生成 → 分段确认
          │
          ▼ (用户说 "go")
   subagent-driven-development 启动
          │
          ├── 子 Agent 1: 执行 task A
          ├── 子 Agent 2: 执行 task B
          └── 主 Agent: 审查、继续
          │
          ▼ (可自主工作 1-2 小时)
   code-reviewer skill 审查最终结果
```

**特点：** 无 UI 面板，全靠 Skill 在对话中自动编排，执行自主性更高。

---

## 四、适用场景

| 场景 | 更适合 Kiro Specs | 更适合 Superpowers |
|------|-------------------|-------------------|
| 需要团队可见的需求文档 | ✅ EARS 格式可审计 | ⚠️ 产出在对话中，不持久 |
| 需要长时间自主执行 | ⚠️ 逐 task 需确认 | ✅ 子 Agent 连续工作数小时 |
| 多人协作 / 交接 | ✅ specs 文件可共享 | ⚠️ 依赖对话上下文 |
| 快速原型 / vibe coding | ⚠️ 流程较重 | ✅ 对话式更灵活 |
| 跨工具使用 | ❌ Kiro IDE 专属 | ✅ Claude Code/Codex/Cursor 等 |
| 严格 TDD | ⚠️ Tasks 不强制 TDD | ✅ TDD 是核心强制流程 |
| 需要可追溯的决策记录 | ✅ 文件版本控制 | ⚠️ 需额外记录 |

### 能否组合使用？

**可以。** 理论上：
- 用 Kiro Specs 做前期需求和设计（EARS → design.md）
- 在执行阶段用 Superpowers 的 TDD + subagent 模式
- 用 Kiro Hooks 做自动化验证

但实际操作上，Kiro IDE 内暂时无法直接加载 Superpowers 的 Skills（它们是 Claude Code plugin 格式）。组合方式更多是**思维模式的借鉴**而非工具层面的集成。

---

## 五、学习启示 — 结构化 Agent 开发的通用模式

从 Kiro Specs 和 Superpowers 的对比中，可以提炼出"结构化 Agent 开发"的**通用模式**：

### 5.1 核心三阶段（所有方案共有）

```
1. 对齐 (Alignment)     — 确保 Agent 理解你要什么
2. 规划 (Planning)       — 将目标分解为可执行的步骤
3. 受控执行 (Execution)  — 按计划执行 + 持续验证
```

### 5.2 各方案的实现方式

| 通用模式 | Kiro Specs | Superpowers | ECC | mattpocock/skills |
|---------|-----------|-------------|-----|-------------------|
| 对齐 | EARS requirements | Brainstorming skill | — | grill-me / grill-with-docs |
| 规划 | Design + Tasks | Spec + Plan | /plan agent | to-prd / to-issues |
| 受控执行 | Task-by-task | Subagent-driven | Hooks + Rules | tdd + diagnose |
| 验证 | Hooks | Code review skill | AgentShield + quality-gate | tdd (red-green) |
| 记忆/持续 | .kiro/specs/ 文件 | — (对话内) | Instincts (CL v2) | CONTEXT.md |

### 5.3 关键设计取舍

| 取舍 | 选择 A | 选择 B |
|------|--------|--------|
| 结构化程度 | 高（EARS, 固定模板）→ 可预测 | 低（自然对话）→ 灵活 |
| 执行自主性 | 低（逐步确认）→ 安全 | 高（子 Agent 自主）→ 高效 |
| 可移植性 | 低（绑定平台）→ 深度集成 | 高（跨工具）→ 通用 |
| 学习曲线 | 高（需要学 EARS 等）→ 专业 | 低（自然语言）→ 易上手 |

---

## 六、待深入（学完 Superpowers 后补充）

- [ ] Kiro EARS notation 的具体语法和示例
- [ ] Superpowers 子 Agent 执行的具体中间产物
- [ ] 两者在大型功能（>1天工作量）上的表现对比
- [ ] Kiro Hooks 与 Superpowers Skills 的事件触发机制对比
- [ ] 实际体验：同一个需求分别用两种方式完成的效率对比

---

*辅助学习材料，配合 [notes/obra-superpowers/](../notes/obra-superpowers/) 使用*

*最后更新: 2026-05-26*
