# Token 优化学习笔记

> ECC 的成本控制策略 — 模型选择、思考 Token、上下文压缩、MCP/工具管理。

## 系统概述

Token 成本是 AI 编码工具使用中的核心问题。ECC 内置了一套**综合 Token 优化策略**，通过模型选择、思考控制、上下文压缩、工具管理四个维度大幅降低使用成本。

### 核心数据

| 优化维度 | 配置 | 效果 |
|---------|------|------|
| 模型选择 | `model: sonnet` | **60% 成本降低** |
| 思考 Token | `MAX_THINKING_TOKENS: 10000` | **70% 隐藏思考成本降低** |
| 上下文压缩 | `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE: 50` | 更早压缩，减少累积 |
| MCP 管理 | ≤ 10 MCPs, ≤ 80 tools | 减少每请求 token |

## 一、模型选择策略

### 为什么选择 Sonnet?

| 模型 | 成本/1M input | 成本/1M output | 能力 | ECC 推荐 |
|------|-------------|---------------|------|---------|
| Opus | $15 | $75 | 最强 | 仅用于 AgentShield |
| Sonnet | $3 | $15 | 强 | **日常开发 (默认)** |
| Haiku | $0.25 | $1.25 | 基础 | 简单任务/批处理 |

### 成本对比

```
假设日均使用: 500K input + 200K output tokens

Opus:   500K × $15/M + 200K × $75/M  = $7.5 + $15.0 = $22.5/天
Sonnet: 500K × $3/M  + 200K × $15/M  = $1.5 + $3.0  = $4.5/天
                                                        ↓
                                               节省 $18/天 (80%)
                                               ≈ 节省 $540/月
```

### 模型配置

```yaml
# ECC 默认配置
model: sonnet

# Agent 级别覆盖 (仅在需要时使用 Opus)
agents:
  planner: sonnet
  architect: sonnet
  security-reviewer: opus    # 安全审查用更强模型
  code-reviewer: sonnet
  tdd-guide: sonnet
```

### 何时使用 Opus?

| 场景 | 推荐模型 | 原因 |
|------|---------|------|
| 日常编码 | Sonnet | 性价比最优 |
| 代码审查 | Sonnet | 足够胜任 |
| 安全扫描 (AgentShield) | Opus | 需要最强推理能力 |
| 复杂架构设计 | Opus | 需要深度思考 |
| 简单格式化/重命名 | Haiku | 不需要强推理 |

## 二、思考 Token 控制

### 什么是隐藏思考成本?

Claude 的"扩展思考" (Extended Thinking) 功能让模型在回复前进行深度推理。但思考 Token **也计入收费**，且往往是成本大头。

```
用户看到的: 500 output tokens
实际消耗的: 500 output + 15000 thinking tokens
            ≈ 实际成本是表面的 30 倍
```

### MAX_THINKING_TOKENS 配置

```bash
# ECC 推荐: 限制思考 Token 为 10000
export MAX_THINKING_TOKENS=10000
```

| 配置值 | 效果 | 适用场景 |
|--------|------|---------|
| 0 | 禁用扩展思考 | 简单任务，不需要推理 |
| 5000 | 轻度思考 | 常规编码 |
| **10000** | **ECC 推荐** | **平衡质量与成本** |
| 50000 | 深度思考 | 复杂架构问题 |
| 无限制 | 最大思考 | 不计成本的关键问题 |

### 成本影响

```
无限制思考:    平均 ~35000 thinking tokens/请求
ECC 限制 (10K): 最多 10000 thinking tokens/请求
                    ↓
              节省 ~70% 的隐藏思考成本
```

### 质量与成本权衡

| 思考 Token | 代码质量 | 成本 | 推荐 |
|-----------|---------|------|------|
| 0 | 70% | 极低 | ❌ 日常不推荐 |
| 5000 | 85% | 低 | ⚠️ 简单任务可用 |
| 10000 | 92% | 中 | ✅ ECC 默认 |
| 30000 | 96% | 高 | ⚠️ 仅复杂问题 |
| 50000+ | 98% | 极高 | ❌ 很少需要 |

## 三、上下文压缩策略

### CLAUDE_AUTOCOMPACT_PCT_OVERRIDE

```bash
# ECC 推荐: 在上下文达到 50% 时触发压缩
export CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=50
```

默认值通常是 80%（即上下文达到 80% 才压缩）。ECC 将其设为 50%，这意味着**更早压缩**。

### 为什么更早压缩?

```
默认 (80%):
┌────────────────────────────────────────┐
│████████████████████████████████░░░░░░░░│ ← 80% 才压缩
│        大量历史上下文累积                │    可能已经很慢
└────────────────────────────────────────┘

ECC (50%):
┌────────────────────────────────────────┐
│████████████████████░░░░░░░░░░░░░░░░░░░│ ← 50% 就压缩
│        保持上下文精简                   │    始终快速响应
└────────────────────────────────────────┘
```

| 方案 | 优点 | 缺点 |
|------|------|------|
| 晚压缩 (80%) | 保留更多历史 | 请求慢、成本高 |
| **早压缩 (50%)** | 快速响应、成本低 | 可能丢失部分历史细节 |

### 战略性逻辑断点压缩

除了自动压缩，ECC 还在**逻辑断点**手动触发压缩：

```
适合压缩的时机:
✅ 完成一个功能的实现
✅ 通过所有测试
✅ 完成一轮代码审查
✅ 切换到不同模块/文件

不适合压缩的时机:
❌ 调试进行中 (需要保留错误上下文)
❌ 多步操作的中间步骤
❌ 等待外部结果时
```

## 四、MCP 与工具管理

### 工具数量对 Token 的影响

每个注册的工具都会在 system prompt 中占用 Token：

```
1 个工具定义 ≈ 200-500 tokens (名称、描述、参数 schema)

10 MCPs × 8 工具/MCP = 80 工具
80 工具 × 350 tokens ≈ 28,000 tokens 仅用于工具定义!

每次请求的隐性成本: 28K tokens × $3/M = $0.084/请求
日均 100 请求: $8.4/天 仅工具定义!
```

### ECC 优化建议

| 指标 | 推荐上限 | 原因 |
|------|---------|------|
| 活跃 MCP 数量 | ≤ 10 | 减少工具定义 token |
| 活跃工具数量 | ≤ 80 | 保持 system prompt 精简 |
| 工具描述长度 | 简洁 | 减少每个工具的 token 占用 |

### MCP 按需加载

```yaml
# 不是所有 MCP 都需要始终激活
# 推荐: 基础 MCP 常驻，其他按需

always_active:
  - GitHub          # 代码操作 (必需)
  - Memory          # 记忆管理 (必需)
  - Sequential Thinking  # 推理辅助

on_demand:
  - Supabase        # 仅数据库项目
  - Vercel          # 仅部署时
  - Railway         # 仅基础设施操作
  - Playwright      # 仅测试/自动化时
  - Exa             # 仅搜索时
  - Context7        # 仅需要外部上下文时
```

## 五、综合优化策略

### Token 预算公式

```
总成本 = (system_prompt + tools + history + thinking + output) × price_per_token

ECC 优化:
- system_prompt: Skills/Rules 选择性加载 (不全量)
- tools: ≤ 80 工具 (减少 28K→ tokens)
- history: 50% 压缩阈值 (减少累积)
- thinking: 10K 上限 (70% 降低)
- output: 无限制 (不影响质量)
- price: sonnet (60% 降低)
```

### 典型场景成本对比

| 场景 | 无优化 | ECC 优化 | 节省 |
|------|--------|---------|------|
| 日常编码 (1h) | ~$5.0 | ~$1.2 | 76% |
| 代码审查 | ~$3.0 | ~$0.8 | 73% |
| 功能开发 (4h) | ~$20.0 | ~$5.0 | 75% |
| 安全扫描 (full) | ~$15.0 | ~$12.0 | 20% (需要 Opus) |
| 月均使用 | ~$600 | ~$150 | 75% |

### ECC 推荐配置清单

```bash
# ~/.bashrc 或 .env

# 模型选择 (60% 降本)
export CLAUDE_MODEL=sonnet

# 思考 Token 限制 (70% 降本)
export MAX_THINKING_TOKENS=10000

# 上下文压缩 (更早压缩)
export CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=50

# Hook Profile (减少不必要开销)
export ECC_HOOK_PROFILE=prod

# 工具管理建议
# - 保持 ≤ 10 MCPs 活跃
# - 保持 ≤ 80 tools 活跃
# - 按需加载非核心 MCP
```

## 六、高级优化技巧

### 1. Skill 选择性加载

不是所有 246 个 Skills 都需要注入到每次对话中：

```
项目类型: TypeScript + React + Next.js

需要加载的 Skills:
✅ typescript-conventions
✅ react-patterns
✅ nextjs-patterns
✅ tdd-workflow
✅ git-workflow

不需要加载:
❌ python-patterns
❌ django-patterns
❌ golang-patterns
❌ spring-boot-patterns
```

### 2. 分层上下文注入

```
Level 1 (始终): Rules (精简, ~2K tokens)
Level 2 (按需): Active Skills (~5K tokens)
Level 3 (触发): Specific Skill details (~2K tokens)
Level 4 (引用): Full documentation (仅被调用时)
```

### 3. Agent 专业化分工

让不同 Agent 只携带自己需要的上下文：

```
planner Agent:
- 加载: planning skills, architecture rules
- 不加载: coding-standards, testing tools
- Token 节省: ~40%

code-reviewer Agent:
- 加载: review skills, quality rules
- 不加载: deployment tools, database MCP
- Token 节省: ~35%
```

### 4. 压缩时机优化

```javascript
// 在 Hook 中检测逻辑断点
module.exports = async function(event) {
  if (event.type === 'PostToolUse') {
    if (isLogicalBreakpoint(event)) {
      // 任务完成后主动压缩
      await triggerCompaction(event.session);
    }
  }
};

function isLogicalBreakpoint(event) {
  return (
    event.tool.name === 'execute_command' &&
    event.tool.params.command.includes('test') &&
    event.result.exitCode === 0  // 测试通过 = 逻辑断点
  );
}
```

## 七、监控与诊断

### Token 使用追踪

```bash
# SQLite State Store 查询 (概念)
ecc query "SELECT SUM(input_tokens), SUM(output_tokens), 
           SUM(thinking_tokens) FROM sessions WHERE date > '2026-07-01'"
```

### 成本警报

```javascript
// scripts/hooks/pre-tool-use/budget-alert.js
const DAILY_BUDGET = 500000; // tokens

module.exports = async function(event) {
  const todayUsage = await getTodayUsage();
  
  if (todayUsage > DAILY_BUDGET * 0.8) {
    console.warn(`⚠️ Token budget 80% used: ${todayUsage}/${DAILY_BUDGET}`);
  }
  
  if (todayUsage > DAILY_BUDGET) {
    return {
      allow: false,
      reason: `Daily token budget exceeded (${todayUsage}/${DAILY_BUDGET})`
    };
  }
  
  return { allow: true };
};
```

## 问题与思考

- [x] 主要优化维度? → 模型 + 思考Token + 压缩 + 工具管理
- [x] Sonnet vs Opus 成本差? → 约 60-80% 节省
- [x] 为什么早压缩? → 减少累积，保持快速
- [ ] 不同任务类型的最优 MAX_THINKING_TOKENS 值?
- [ ] 压缩后信息丢失的具体影响?
- [ ] MCP 懒加载的具体实现方式?
- [ ] 月度成本报告的可视化方案?
- [ ] 团队共享预算的分配策略?

## 学习记录

| 日期 | 内容 | 来源 |
|------|------|------|
| 2026-07 | 初始整理: 四维优化策略、配置建议、成本对比、高级技巧 | GitHub README + Token 定价 |

---

*参考: [architecture.md](../architecture.md) | [references.md](../references.md)*
