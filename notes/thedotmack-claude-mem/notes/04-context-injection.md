# 上下文注入学习笔记

> claude-mem 的应用层 — 相关性匹配、Token 预算管理与渐进式披露搜索。

## 概述

上下文注入是 claude-mem 的"价值交付点"——在新 Session 开始时，将相关历史知识注入 Agent 上下文，让 Agent 仿佛"记得"之前的工作。

## Token 预算管理

### 预算计算

```
Agent 上下文窗口: 200K tokens (Claude)
系统 Prompt: ~5K tokens
用户消息空间: ~180K tokens
记忆注入预算: 配置比例 (默认 10%)

可用记忆预算 = 200K × 10% = 20K tokens
```

### 注入选择算法

```
1. 获取候选记忆 (搜索相关)
2. 按综合得分排序:
   score = relevance × 0.4 + importance × 0.3 + recency × 0.2 + diversity × 0.1
3. 逐条选择，累加 Token:
   while (累计 tokens < 预算):
     选择下一条最高得分的记忆
     累计 += 该记忆的 token 数
4. 返回选中记忆列表
```

### 评分维度

| 维度 | 计算方式 | 权重 |
|------|---------|------|
| relevance | FTS5/ChromaDB 得分 | 0.4 |
| importance | 记忆的重要性评分 | 0.3 |
| recency | 时间衰减函数 | 0.2 |
| diversity | 覆盖不同主题 | 0.1 |

## Subagent 注入控制 (Issue #1464)

### 问题背景

```
主 Agent 启动 Subagent:
  选项 A: Subagent 也注入记忆 → 更有上下文但消耗更多 token
  选项 B: Subagent 不注入 → 轻量但可能缺少背景

核心矛盾: 上下文丰富 vs Token 成本
```

### 解决方案

```yaml
# claude-mem 配置
injection:
  main_agent:
    budget_percent: 10    # 主 Agent 10% 预算
    strategy: "full"      # 完整注入

  subagent:
    enabled: true/false   # 是否注入
    budget_percent: 3     # Subagent 只 3% (更少)
    strategy: "minimal"   # 只注入最核心的
    filter: "task_relevant" # 只注入与当前任务相关的
```

## 渐进式披露搜索

### mem-search Skill 接口

```
Agent 需要查找记忆时，使用 mem-search Skill:

Level 1 (概览):
  Agent: "之前关于支付模块我了解了什么?"
  返回: 3 条精简摘要 (每条 <50 tokens)

Level 2 (详情):
  Agent: "详细说说 Stripe 集成的问题"
  返回: 相关记忆的完整内容

Level 3 (原始):
  Agent: "显示原始的工具调用记录"
  返回: 未压缩的原始观察列表
```

### 渐进式的价值

```
不渐进: 一次返回所有记忆 = 浪费 Token + 信息过载
渐进式: 先给摘要，需要时再展开 = 节省 Token + 相关性高
```

## 注入格式

### 注入到 Agent 上下文的格式

```markdown
## 历史记忆 (claude-mem)

### 项目知识
- API 使用 Bearer token 认证 (从 /auth/login 获取, 1h 过期)
- 数据库是 PostgreSQL 14, 通过 Prisma ORM 访问
- 项目使用 pnpm, 不是 npm

### 最近工作
- 上次 Session 完成了用户注册功能
- 发现 email 验证有 race condition (未修复)
- 测试覆盖率从 65% 提升到 78%

### 注意事项
- 不要修改 config/prod.ts (需要 PR review)
- CI 使用 GitHub Actions, 约 3 分钟
```

## 问题与思考

- [x] 预算如何计算? → 上下文窗口 × 配置比例
- [x] 如何选择注入什么? → 综合得分排序，逐条选择
- [ ] 注入格式对 Agent 行为的影响?
- [ ] 最优预算比例 (5%? 10%? 15%)?
- [ ] 不相关记忆注入的负面影响?
- [ ] 记忆冲突 (矛盾信息) 如何处理?

---

*参考: [architecture.md](../architecture.md) | [01-hook-lifecycle.md](./01-hook-lifecycle.md)*
