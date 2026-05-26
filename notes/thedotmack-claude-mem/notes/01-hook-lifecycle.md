# 5 阶段 Hook 生命周期学习笔记

> claude-mem 的数据入口 — PreSession、ToolCall、Observation、Compression、PostSession。

## 概述

claude-mem 通过 5 阶段 Hook 系统拦截 Agent Session 的完整生命周期，捕获所有有价值的信息用于后续压缩和持久化。

### 5 阶段全景

```
┌──────────────────────────────────────────────────────────┐
│  Session 完整生命周期                                      │
│                                                          │
│  ┌──────────┐                                            │
│  │1.PreSess │  搜索历史 → 注入相关记忆                    │
│  └────┬─────┘                                            │
│       │                                                  │
│       ▼                                                  │
│  ┌──────────┐                                            │
│  │2.ToolCall│  捕获每次工具使用 (read/write/exec)         │
│  └────┬─────┘                                            │
│       │ ←─── (多次循环)                                   │
│       ▼                                                  │
│  ┌──────────┐                                            │
│  │3.Observe │  标记重要发现为候选记忆                      │
│  └────┬─────┘                                            │
│       │                                                  │
│       ▼                                                  │
│  ┌──────────┐                                            │
│  │4.Compress│  AI 压缩累积观察 → 精华                     │
│  └────┬─────┘                                            │
│       │                                                  │
│       ▼                                                  │
│  ┌──────────┐                                            │
│  │5.PostSess│  持久化到 DB + 更新索引                     │
│  └──────────┘                                            │
└──────────────────────────────────────────────────────────┘
```

## 阶段 1: PreSession (会话前)

### 功能

```
新 Session 开始时:
  1. 获取当前 Session 上下文 (工作目录/最近文件/任务描述)
  2. 搜索历史记忆中的相关内容
  3. 按 Token 预算选择最相关的记忆
  4. 注入到 Agent 的初始上下文中
```

### 搜索策略

| 策略 | 方法 | 权重 |
|------|------|------|
| 关键词匹配 | FTS5 全文搜索 | 0.4 |
| 语义相似 | ChromaDB 向量 (可选) | 0.3 |
| 时间相近 | 最近的记忆优先 | 0.2 |
| 重要性 | importance 评分 | 0.1 |

## 阶段 2: ToolCall (工具调用)

### 捕获的事件

| 工具类型 | 捕获内容 | 价值 |
|---------|---------|------|
| read_file | 读取了哪些文件 | 理解 Agent 关注什么 |
| write_file | 写入了什么内容 | 记录代码变更 |
| execute | 执行了什么命令 | 记录操作历史 |
| search | 搜索了什么 | 理解信息需求 |
| web_fetch | 访问了什么 URL | 外部资源参考 |

### 捕获格式

```typescript
interface ToolCallEvent {
  tool: string;          // 工具名
  params: object;        // 调用参数
  result: string;        // 返回结果 (可截断)
  timestamp: number;     // 时间戳
  session_id: string;    // Session ID
  importance: number;    // 重要性评分 (0-1)
}
```

## 阶段 3: Observation (观察)

### 什么被标记为"重要发现"

| 类型 | 示例 | 触发条件 |
|------|------|---------|
| 新发现 | "发现这个 API 需要特殊认证" | Agent 表达惊讶/发现 |
| 决策 | "决定用 Redis 而非 Memcached" | 做出技术选择 |
| 问题解决 | "原来 bug 是因为时区问题" | 根因确认 |
| 用户偏好 | "用户说不要用 class 组件" | 用户反馈 |
| 约束发现 | "这个环境只支持 Node 18" | 环境限制 |

### Observation 与 ToolCall 的区别

```
ToolCall: 记录"Agent 做了什么" (行为层)
  → read_file("user.ts")
  → execute("npm test")
  → write_file("fix.ts", "...")

Observation: 记录"Agent 学到了什么" (知识层)
  → "UserService 有循环依赖"
  → "测试需要先启动 Docker"
  → "项目用 pnpm 不是 npm"
```

## 阶段 4: Compression (压缩)

### AI 压缩原理

```
输入: 累积的 Observations (可能很长)
  - "发现 API 需要 Bearer token"
  - "token 从 /auth/login 获取"
  - "token 有效期 1 小时"
  - "过期后需要 refresh"
  - "refresh token 在 cookie 中"

压缩后 (Claude Agent SDK):
  "项目 API 认证: Bearer token (从 /auth/login 获取，1h 过期，
   refresh token 在 cookie 中自动刷新)"

效果: 5 条观察 → 1 条精华记忆
```

### 压缩触发条件

| 条件 | 阈值 |
|------|------|
| 观察数量 | > 10 条未压缩观察 |
| Token 数量 | > 2000 tokens 未压缩 |
| 时间间隔 | 每 5 分钟检查一次 |
| Session 结束 | 强制压缩所有剩余 |

## 阶段 5: PostSession (会话后)

### 动作清单

| 动作 | 说明 |
|------|------|
| 持久化 | 压缩后的记忆写入 SQLite |
| FTS5 索引 | 更新全文搜索索引 |
| ChromaDB | 生成 embedding 并存储 (可选) |
| 清理 | 删除临时观察数据 |
| 统计 | 更新 Session 统计信息 |

## 与 hermes-agent 学习循环的对比

| 维度 | claude-mem Hook | hermes-agent Loop |
|------|----------------|-------------------|
| 触发 | 被动 (Hook 拦截) | 主动 (Agent 观察) |
| 处理 | 外部 Worker | Agent 内部 |
| 决策 | 自动+阈值 | Agent 判断 |
| Skill 进化 | ❌ 只记忆 | ✅ 创建/改进 Skill |
| 用户建模 | ❌ | ✅ Honcho |

## 问题与思考

- [x] 5 阶段的数据流? → Pre(注入) → Tool(捕获) → Observe(标记) → Compress(压缩) → Post(存储)
- [x] Hook 如何不影响性能? → 异步Worker，不阻塞 Agent
- [ ] Observation 的重要性如何自动评分?
- [ ] 压缩质量如何评估?
- [ ] 长 Session (>1h) 的多次压缩策略?
- [ ] Hook 失败时的容错机制?

---

*参考: [architecture.md](../architecture.md) | [02-worker-service.md](./02-worker-service.md)*
