# Instincts 持续学习 v2 学习笔记

> ECC 独创的自动学习系统 — 从 Session 观察中提取行为模式，渐进进化为正式组件。

## 系统概述

**Continuous Learning v2 (Instincts)** 是 ECC 最具差异化的设计。它让 Agent 能从工作过程中**自动学习**并**渐进进化**，无需人工干预地将反复出现的行为模式固化为正式的 Skills、Commands 或 Agents。

### 核心理念

```
"观察 Agent 的工作方式，提取成功模式，自动优化未来行为"
```

### 核心数据

| 属性 | 值 |
|------|-----|
| 引入版本 | v1.2.0 (Feb 2026) |
| 存储方式 | SQLite (项目级) |
| 作用域 | 项目级隔离 |
| 进化目标 | Skill / Command / Agent |
| 置信度范围 | 0.0 ~ 1.0 |

## 生命周期详解

### 完整生命周期

```
┌──────────────────────────────────────────────────────────────┐
│                    Instinct 生命周期                          │
│                                                              │
│  1. 观察                    2. 提取                          │
│  ┌─────────────┐           ┌─────────────┐                  │
│  │Session      │──────────▶│ Instinct    │                  │
│  │Observer     │           │ Generator   │                  │
│  │(分析对话模式)│           │(生成原子直觉) │                  │
│  └─────────────┘           └──────┬──────┘                  │
│                                   │                          │
│  3. 评分                    4. 存储                          │
│  ┌─────────────┐           ┌─────────────┐                  │
│  │Confidence   │◀──────────│  Project    │                  │
│  │Scorer       │──────────▶│  Store      │                  │
│  │(置信度计算)  │           │ (SQLite)    │                  │
│  └─────────────┘           └──────┬──────┘                  │
│                                   │                          │
│  5. 进化                                                     │
│  ┌─────────────────────────────────────────────┐            │
│  │  高置信度 Instinct → Skill / Command / Agent │            │
│  └─────────────────────────────────────────────┘            │
└──────────────────────────────────────────────────────────────┘
```

### 阶段 1: Session 观察

Session Observer 在会话过程中**被动监听**以下信号：

| 观察维度 | 信号类型 | 示例 |
|---------|---------|------|
| 对话模式 | 重复出现的请求模式 | "每次改 API 都要改 DTO" |
| 工具使用 | 工具调用序列 | "总是先 lint 再 commit" |
| 错误恢复 | 修复错误的方式 | "类型错误总是先检查 import" |
| 决策模式 | 选择的实现方式 | "React 组件总是用 function 声明" |
| 时间模式 | 操作的顺序关系 | "测试总是在实现之前编写" |

### 阶段 2: 直觉提取

Instinct Generator 从观察中提取**原子级别**的行为直觉：

```javascript
// Instinct 数据结构
{
  id: "inst_a1b2c3d4",
  pattern: "当修改 API endpoint 时，需要同步更新对应的 DTO 类型",
  action: "检查并更新 types/ 目录下对应的接口定义",
  context: {
    language: "typescript",
    framework: "express",
    area: "api-development"
  },
  confidence: 0.0,  // 初始为 0
  occurrences: 1,
  successes: 0,
  project: "my-project",
  source_sessions: ["session_xyz"],
  created_at: "2026-07-01T10:00:00Z",
  updated_at: "2026-07-01T10:00:00Z"
}
```

#### 原子性原则

每个 Instinct 只包含**一个**行为模式：

```
✅ 好的 Instinct:
"当创建新的 React 组件时，使用 function 声明而非箭头函数"

❌ 坏的 Instinct (过于复杂):
"创建 React 组件时，使用 function 声明，添加 PropTypes，
 编写单元测试，更新 index.ts 导出"
```

### 阶段 3: 置信度评分

Confidence Scorer 基于多个维度计算分数：

#### 评分维度

| 维度 | 权重 | 计算方式 |
|------|------|---------|
| 出现频率 | 40% | occurrences / total_sessions |
| 成功率 | 35% | successes / occurrences |
| 一致性 | 15% | 跨 session 的稳定性 |
| 时效性 | 10% | 近期出现次数加权 |

#### 置信度阈值

| 阈值 | 范围 | 状态 | 行为 |
|------|------|------|------|
| 低 | < 0.3 | 观察中 | 仅记录，不影响行为 |
| 中 | 0.3 - 0.7 | 建议中 | 作为软性建议提供 |
| 高 | > 0.7 | 准备进化 | 候选升级为正式组件 |
| 极高 | > 0.9 | 已进化 | 自动升级 |

#### 分数更新逻辑

```javascript
function updateConfidence(instinct, sessionResult) {
  instinct.occurrences++;
  
  if (sessionResult.success) {
    instinct.successes++;
  }
  
  const frequency = instinct.occurrences / totalProjectSessions;
  const successRate = instinct.successes / instinct.occurrences;
  const consistency = calculateConsistency(instinct);
  const recency = calculateRecency(instinct);
  
  instinct.confidence = 
    frequency * 0.40 +
    successRate * 0.35 +
    consistency * 0.15 +
    recency * 0.10;
  
  instinct.updated_at = new Date().toISOString();
}
```

### 阶段 4: 项目级存储

#### SQLite Schema (概念)

```sql
CREATE TABLE instincts (
  id TEXT PRIMARY KEY,
  pattern TEXT NOT NULL,
  action TEXT NOT NULL,
  context_json TEXT,
  confidence REAL DEFAULT 0.0,
  occurrences INTEGER DEFAULT 0,
  successes INTEGER DEFAULT 0,
  project_id TEXT NOT NULL,      -- 项目级隔离
  source_sessions TEXT,          -- JSON array of session IDs
  evolved_to TEXT,               -- 进化后的目标组件 ID
  created_at TEXT,
  updated_at TEXT
);

CREATE INDEX idx_project ON instincts(project_id);
CREATE INDEX idx_confidence ON instincts(confidence DESC);
```

#### 项目隔离原则

```
项目 A (TypeScript/React):
├── "组件使用 function 声明"  confidence: 0.85
├── "CSS 使用 Tailwind class"  confidence: 0.72
└── "状态用 zustand 管理"  confidence: 0.61

项目 B (Python/Django):
├── "视图使用 class-based"  confidence: 0.78
├── "序列化用 DRF Serializer"  confidence: 0.69
└── "测试用 pytest fixture"  confidence: 0.55

❌ 不允许: 项目 A 的 React 习惯影响项目 B 的 Django 开发
```

### 阶段 5: 进化路径

高置信度 Instinct 可以进化为三种正式组件：

```
Instinct (confidence > 0.9)
        │
        ├──▶ Skill (工作流类)
        │    当模式是"如何做某事"时
        │    例: "API 开发时总是先写 OpenAPI spec"
        │         → 进化为 api-first-design skill
        │
        ├──▶ Command (命令类)
        │    当模式是"触发某个动作"时
        │    例: "每次 PR 前都要运行完整测试"
        │         → 进化为 /pre-pr-check 命令
        │
        └──▶ Agent (专家类)
        │    当模式涉及"复杂多步决策"时
        │    例: "性能问题需要分析、基准、优化、验证"
        │         → 进化为 performance-optimizer agent
```

#### 进化条件

| 进化目标 | 置信度 | 额外条件 |
|---------|--------|---------|
| Skill | > 0.85 | 包含明确的步骤序列 |
| Command | > 0.80 | 可用单一触发词描述 |
| Agent | > 0.90 | 涉及多步决策和专业知识 |

## 关键设计决策

### 为什么选择项目级隔离?

| 问题 | 全局共享的风险 |
|------|--------------|
| 语言差异 | Python 的 snake_case 会污染 TypeScript 的 camelCase |
| 框架差异 | Django 的 class views 会影响 Express 的 function handlers |
| 团队差异 | A 团队偏好可能不适合 B 团队 |
| 领域差异 | 前端习惯不适用于后端 |

### 为什么选择原子性?

| 问题 | 复合 Instinct 的风险 |
|------|---------------------|
| 评分困难 | 部分正确时如何打分? |
| 进化歧义 | 应该变成 Skill 还是 Agent? |
| 冲突处理 | 复合模式内部矛盾时怎么办? |
| 可解释性 | 用户难以理解复杂模式 |

### 为什么选择渐进式进化?

```
观察 ──▶ 记录 ──▶ 建议 ──▶ 正式化
 │         │        │         │
 ▼         ▼        ▼         ▼
零成本    低成本   可验证    高价值
无风险    可回滚   可修正    持久化
```

## 与 Hook 系统的集成

Instincts 系统通过 Hooks 实现观察和学习：

```
SessionStart Hook:
    └── load-instincts.js
        └── 从 SQLite 加载当前项目的高置信度 Instincts
        └── 注入到 Agent 上下文中作为行为参考

SessionEnd Hook:
    └── save-instinct.js
        └── 分析本次 Session 的行为模式
        └── 提取新 Instinct / 更新现有 Instinct 置信度
        └── 检查是否有 Instinct 达到进化阈值
```

## 实践场景

### 场景 1: 学习 TDD 偏好

```
Session 1: 用户先写测试再实现 → 记录模式
Session 2: 用户又先写测试 → confidence 上升
Session 3: 用户再次先写测试 → confidence: 0.7
...
Session 10: confidence: 0.92 → 进化为 "always-test-first" skill
```

### 场景 2: 学习代码风格

```
Session 1: 用户将 if-else 改为 early return → 记录
Session 3: 再次 early return → confidence 上升
Session 7: confidence: 0.85 → 作为建议提供
Session 15: confidence: 0.91 → 进化为 coding-standards rule
```

### 场景 3: 学习项目特定模式

```
项目 X (monorepo):
Session 1: 修改 shared/ 后总是运行 affected:test → 记录
Session 5: confidence: 0.88 → 进化为 /test-affected 命令
```

## 问题与思考

- [x] 为什么项目级隔离? → 防止跨项目习惯污染
- [x] Instinct 格式? → { pattern, action, confidence, project }
- [x] 进化目标? → Skill / Command / Agent
- [ ] 置信度衰减机制? (长时间不出现的模式是否降分?)
- [ ] 冲突检测? (两个高置信度 Instinct 矛盾时?)
- [ ] 用户是否能手动否决/删除 Instinct?
- [ ] 进化后的组件如何标注来源?
- [ ] 多人协作时的 Instinct 合并策略?

## 与其他系统对比

| 维度 | ECC Instincts | OpenClaw Memory | IDE 配置 |
|------|--------------|-----------------|----------|
| 学习方式 | 自动观察 | 手动 + 自动 | 手动配置 |
| 粒度 | 原子行为模式 | 文档级 (SOUL.md) | 配置项 |
| 进化能力 | ✅ (Instinct → Skill) | ❌ | ❌ |
| 项目隔离 | ✅ | ✅ (per-agent) | ❌ (全局) |
| 置信度 | ✅ (数值评分) | ❌ | ❌ |
| 可解释性 | 高 (模式可读) | 高 (Markdown) | 低 |

## 学习记录

| 日期 | 内容 | 来源 |
|------|------|------|
| 2026-07 | 初始整理: 生命周期、评分机制、隔离策略、进化路径 | GitHub README + CL v2 文档 |

---

*参考: [architecture.md](../architecture.md) | [01-skills-system.md](./01-skills-system.md)*
