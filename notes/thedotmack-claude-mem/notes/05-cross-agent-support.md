# 跨 Agent 支持学习笔记

> claude-mem 的平台层 — 7+ Agent 支持、Hook 适配方式与架构演进 (PM2→Bun)。

## 概述

claude-mem 支持 7+ 种 Agent 工具，通过统一的 Hook 接口为不同 Agent 提供记忆能力。

## 支持的 Agent

| Agent | Hook 集成方式 | 支持深度 |
|-------|-------------|---------|
| Claude Code | 原生 Hook API | 完整 (主要目标) |
| OpenClaw | Hook 适配器 | 完整 |
| Codex | Hook 适配器 | 完整 |
| Gemini | Hook 适配器 | 完整 |
| Hermes | Hook 适配器 | 完整 |
| Copilot | Hook 适配器 | 完整 |
| OpenCode | Hook 适配器 | 完整 |

## Hook 适配架构

```
┌──────────────────────────────────────────────────┐
│  Agent 特定 Hook 事件                             │
├──────────────────────────────────────────────────┤
│  Claude Code: PreToolUse/PostToolUse/SessionStart │
│  OpenClaw: tool_call/session_event               │
│  Codex: pre_action/post_action                   │
│  ...                                             │
└──────────────────┬───────────────────────────────┘
                   │ 适配器转换
                   ▼
┌──────────────────────────────────────────────────┐
│  claude-mem 统一事件格式                          │
│  PreSession / ToolCall / Observation /           │
│  Compression / PostSession                       │
└──────────────────────────────────────────────────┘
```

## 适配器模式

### 典型适配器

```typescript
// Claude Code 适配器 (最简单，原生对应)
class ClaudeCodeAdapter {
  mapEvent(event: ClaudeCodeEvent): MemEvent {
    switch(event.type) {
      case 'SessionStart': return { type: 'PreSession', ... };
      case 'PreToolUse': return { type: 'ToolCall', ... };
      case 'PostToolUse': return { type: 'Observation', ... };
      case 'SessionEnd': return { type: 'PostSession', ... };
    }
  }
}

// 其他 Agent 适配器 (需要映射)
class GenericAdapter {
  mapEvent(event: AgentEvent): MemEvent {
    // 将各 Agent 特有事件映射到 claude-mem 5 阶段
  }
}
```

## 架构演进: PM2 → Bun

### 时间线

```
v1: PM2 + Node.js
├── Worker 由 PM2 管理
├── TypeScript → 编译 → JavaScript
├── 依赖: pm2, typescript, ts-node
└── 问题: 启动慢，依赖多，配置复杂

v2: Bun (当前)
├── Worker 由 Bun 原生管理
├── TypeScript 直接运行 (无编译)
├── 依赖: 只需要 bun
└── 优势: 启动快，依赖少，开发体验好
```

### 迁移动机

| 痛点 | PM2 方案 | Bun 解决 |
|------|---------|---------|
| TypeScript 编译 | tsc → 慢 | 原生运行 |
| 冷启动 | ~2s | ~50ms |
| 进程管理 | pm2 start/stop | bun --watch |
| 依赖安装 | npm install (慢) | bun install (快) |
| 测试 | jest 配置 | bun test 内置 |

## 多 Agent 共享记忆

### 场景

```
开发者使用多个 Agent:
  上午: Claude Code (写代码)
  下午: Cursor (review)
  晚上: Hermes (研究)

共享记忆:
  Claude Code 发现的知识 → 存入 claude-mem
  Cursor 开始时 → 注入 Claude Code 的发现
  Hermes 开始时 → 注入所有之前的知识

= 跨 Agent 的连续记忆体验
```

### 隔离策略

| 策略 | 说明 | 适用场景 |
|------|------|---------|
| 共享全部 | 所有 Agent 共享所有记忆 | 个人开发者 |
| 项目隔离 | 按项目目录隔离 | 多项目 |
| Agent 隔离 | 每个 Agent 独立记忆 | 安全要求高 |
| 混合 | 核心知识共享 + Agent 特有分开 | 推荐 |

## 问题与思考

- [x] 为什么支持 7+ Agent? → 用户不只用一个工具
- [x] 适配器模式如何工作? → 统一事件格式映射
- [ ] 不同 Agent 的 Hook 能力差异?
- [ ] 多 Agent 并发写入的数据一致性?
- [ ] 新 Agent 的适配开发成本?
- [ ] 记忆共享的安全边界?

---

*参考: [architecture.md](../architecture.md) | [01-hook-lifecycle.md](./01-hook-lifecycle.md)*
