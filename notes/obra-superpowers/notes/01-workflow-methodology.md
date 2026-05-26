# 六阶段开发工作流

> superpowers 的核心 — 强制执行的软件开发方法论，从头脑风暴到完成的完整流程。

## 概述

superpowers 的六阶段工作流是一个**强制性**的开发流程。Agent 不能跳步骤，必须按顺序执行。每个阶段有明确的输入、输出和完成条件。

### 为什么需要强制流程

| 无强制流程的 Agent 行为 | 有强制流程的 Agent 行为 |
|------------------------|----------------------|
| 用户说需求 → 直接写代码 | 用户说需求 → 先头脑风暴 |
| 跳过设计 → 边写边改 | 先设计 → 分块理解 → 再写 |
| 写完不审查 → 直接交付 | 写完 → 强制审查 → 再交付 |
| 质量靠运气 | 质量有保障 |

## 阶段 1: Brainstorming (头脑风暴)

### 核心原则: 不写代码

```
┌─────────────────────────────────────────────────┐
│  Brainstorming 阶段的黄金法则:                    │
│                                                 │
│        ❌ 绝对不写任何代码                        │
│        ❌ 不做技术方案选型                        │
│        ✅ 只探索问题和可能的方向                   │
│        ✅ 确保理解"真正要解决什么"                 │
└─────────────────────────────────────────────────┘
```

### Brainstorm 的提问框架

| 层次 | 问题类型 | 示例 |
|------|---------|------|
| Why | 为什么要做这件事 | "解决什么用户痛点？" |
| What | 具体做什么 | "核心功能是什么？边界在哪？" |
| How (方向) | 可能的方案方向 | "有哪些实现路径？" |
| Trade-off | 各方案的优劣 | "方案 A 快但不灵活，方案 B 慢但可扩展" |
| Decision | 最终选择 | "基于当前情况，推荐方案 B" |

### 退出条件

Brainstorming 阶段的完成标准:
1. 用户明确同意了问题定义
2. 探索了至少两种方案
3. 用户明确选择了一个方向
4. Agent 复述了理解，用户确认

## 阶段 2: Specification (规格化)

### 核心原则: Digestible Chunks (可消化的块)

```
❌ 错误方式: 一个 50 页的设计文档
  → Agent 上下文溢出
  → 用户看不完
  → 无法逐步验证

✅ 正确方式: 分成多个小块
  块 1: 数据模型设计 (独立可验证)
  块 2: API 接口设计 (独立可验证)
  块 3: 前端组件设计 (独立可验证)
  块 4: 错误处理策略 (独立可验证)
```

### Specification 输出格式

```markdown
## Spec Block 1: 数据模型

### 实体
- User: id, name, email, role
- Notification: id, user_id, type, content, read_at

### 关系
- User 1:N Notification

### 约束
- Notification.type ∈ {email, push, in_app}
- read_at nullable (null = 未读)

### 验证问题
"这个数据模型能支撑你描述的所有场景吗？"
```

## 阶段 3: Planning (规划)

### 核心原则: Junior-Engineer Friendly

```
┌─────────────────────────────────────────────────┐
│  Plan 的标准: 一个初级工程师看了能直接执行        │
│                                                 │
│  包含:                                          │
│    - 清晰的步骤编号                              │
│    - 每步的预期产出                              │
│    - 每步的验证方式                              │
│    - 依赖关系标注                                │
│    - 无模糊表述                                  │
└─────────────────────────────────────────────────┘
```

### Plan 输出格式

```markdown
## Implementation Plan

### Task 1: 创建 Notification 数据模型
- 文件: src/models/notification.ts
- 产出: TypeORM entity 定义
- 验证: 运行 migration，确认表创建成功
- 依赖: 无

### Task 2: 实现通知发送服务
- 文件: src/services/notification.service.ts
- 产出: sendNotification(), markAsRead() 方法
- 验证: 单元测试通过
- 依赖: Task 1
- TDD: 先写测试再实现

### Task 3: 实现通知 API
- 文件: src/controllers/notification.controller.ts
- 产出: GET /notifications, PATCH /notifications/:id/read
- 验证: 集成测试通过
- 依赖: Task 2
- TDD: 先写测试再实现
```

### TDD + YAGNI + DRY 如何体现

| 原则 | 在 Plan 中的体现 |
|------|----------------|
| TDD | 每个 Task 标注"先写测试再实现" |
| YAGNI | 不规划"将来可能需要的"功能 |
| DRY | 识别共用逻辑，规划为共享模块 |

## 阶段 4: Implementation (实现)

### Subagent-Driven-Development

详见 [02-subagent-driven-development.md](./02-subagent-driven-development.md)

核心流程:
```
主 Agent 读取 Plan
    │
    ├── 为 Task 1 创建 Subagent → 独立执行
    ├── 为 Task 2 创建 Subagent → 独立执行 (依赖 Task 1 完成)
    └── 为 Task 3 创建 Subagent → 独立执行 (依赖 Task 2 完成)
```

## 阶段 5: Review (审查)

### 审查清单

| 检查项 | 标准 |
|--------|------|
| 功能完整性 | 是否实现了 Plan 中所有 Task |
| 测试覆盖 | 每个 Task 是否有测试 |
| 代码质量 | 命名、结构、可读性 |
| 安全性 | 无明显安全漏洞 |
| 性能 | 无明显性能问题 |
| 一致性 | 风格与项目现有代码一致 |

### Review 决策

```
Review 结果:
├── ✅ 通过 → 进入 Finish 阶段
├── ⚠️ 小问题 → 在 Review 中直接修复
└── ❌ 重大问题 → 返回 Implement 阶段
```

## 阶段 6: Finish (完成)

### 完成动作清单

| 动作 | 说明 |
|------|------|
| 清理临时文件 | 删除调试代码、临时文件 |
| Commit | 生成规范的 commit message |
| Branch | 确保在正确分支上 |
| PR 准备 | 生成 PR 描述（如需要） |
| 文档 | 更新相关文档（如需要） |

## 问题与思考

- [x] 为什么是六阶段? → 覆盖完整开发生命周期
- [x] 为什么强制? → Agent 自主时跳过关键步骤
- [ ] 能否自定义阶段? 如跳过 Brainstorm?
- [ ] 小任务也走完整六阶段吗?
- [ ] 阶段间的状态如何持久化?

---

*参考: [architecture.md](../architecture.md) | [02-subagent-driven-development.md](./02-subagent-driven-development.md)*
