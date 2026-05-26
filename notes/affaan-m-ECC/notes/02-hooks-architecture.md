# Hook 架构学习笔记

> ECC 的事件驱动自动化系统 — 在 Agent 生命周期的关键节点注入自定义逻辑。

## 系统概述

Hooks 是 ECC 中的**命令式自动化层**，在特定事件发生时执行 Node.js 脚本。不同于 Skills 的声明式工作流，Hooks 提供了底层的行为拦截和扩展能力。

### 核心数据

| 属性 | 值 |
|------|-----|
| 脚本语言 | Node.js |
| 脚本位置 | `scripts/hooks/` |
| Claude Code 事件数 | 8 类 |
| Cursor 事件数 | 15 类 |
| OpenCode 事件数 | 11 类 |
| 运行时控制 | ECC_HOOK_PROFILE, ECC_DISABLED_HOOKS |

## 事件类型详解

### Claude Code Hook 事件 (8 类)

| 事件 | 触发时机 | 典型用途 | 重要性 |
|------|---------|---------|--------|
| `SessionStart` | 会话开始时 | 加载上下文、初始化状态、设置环境 | ⭐⭐⭐ |
| `SessionEnd` | 会话结束时 | 保存学习、归档日志、清理资源 | ⭐⭐⭐ |
| `PreToolUse` | 工具调用前 | 安全检查、权限验证、token 预算检查 | ⭐⭐⭐ |
| `PostToolUse` | 工具调用后 | 日志记录、结果验证、质量检查 | ⭐⭐ |
| `Stop` | Agent 停止时 | 清理、状态持久化、报告生成 | ⭐⭐ |
| `PreEdit` | 文件编辑前 | 备份、格式校验、lint 预检 | ⭐⭐ |
| `PostEdit` | 文件编辑后 | 自动 lint、测试触发、格式化 | ⭐⭐ |
| `Error` | 错误发生时 | 错误恢复、降级处理、通知 | ⭐ |

### 事件生命周期

```
SessionStart
    │
    ▼ (会话进行中)
    │
    ├──▶ PreToolUse ──▶ [Tool Execution] ──▶ PostToolUse
    │         │                                     │
    │         │ (可拒绝)                             │ (可审计)
    │         ▼                                     ▼
    │    拒绝执行 / 修改参数                    日志 / 质量检查
    │
    ├──▶ PreEdit ──▶ [File Edit] ──▶ PostEdit
    │         │                          │
    │         ▼                          ▼
    │    备份 / 检查                   Lint / Test
    │
    ├──▶ Error ──▶ Recovery / Fallback
    │
    ▼
Stop ──▶ SessionEnd
```

### Cursor 扩展事件 (15 类)

Cursor IDE 在 Claude Code 的 8 个事件基础上增加了 IDE 特有事件：

| 额外事件 | 说明 |
|---------|------|
| `FileOpen` | 文件打开时 |
| `FileClose` | 文件关闭时 |
| `FileSave` | 文件保存时 |
| `SelectionChange` | 选区变化时 |
| `DiagnosticUpdate` | 诊断信息更新时 |
| `TerminalOutput` | 终端输出时 |
| `CompletionAccept` | 接受补全时 |

### OpenCode 事件 (11 类)

OpenCode 有独立的事件体系，介于 Claude Code 和 Cursor 之间。

## 脚本架构

### 目录结构

```
scripts/hooks/
├── pre-tool-use/
│   ├── security-check.js       # 安全检查 — 阻止危险工具调用
│   ├── token-guard.js          # Token 预算 — 防止超支
│   └── permission-check.js     # 权限验证 — 操作授权
├── post-tool-use/
│   ├── log-action.js           # 操作日志 — 审计追踪
│   ├── quality-check.js        # 质量检查 — 输出验证
│   └── metrics-collect.js      # 指标收集 — 性能数据
├── session-start/
│   ├── load-context.js         # 加载上下文 — 初始化环境
│   ├── check-environment.js    # 检查环境 — 验证依赖
│   └── load-instincts.js       # 加载直觉 — CL v2 集成
├── session-end/
│   ├── save-instinct.js        # 保存直觉 — 学习结果持久化
│   ├── archive-session.js      # 归档会话 — 日志保存
│   └── cleanup.js              # 清理 — 临时文件/状态
├── stop/
│   └── graceful-shutdown.js    # 优雅退出
├── pre-edit/
│   └── backup-file.js          # 文件备份
├── post-edit/
│   ├── auto-lint.js            # 自动 Lint
│   └── trigger-test.js         # 触发相关测试
└── error/
    └── error-recovery.js       # 错误恢复
```

### 脚本接口

```javascript
// Hook 脚本标准接口
module.exports = async function(event) {
  // event 对象包含:
  // - type: 事件类型 (e.g., 'PreToolUse')
  // - tool: 工具信息 (仅 tool 相关事件)
  // - session: 会话信息
  // - context: 上下文数据
  // - timestamp: 事件时间戳
  
  // 返回值:
  // - { allow: true }  — 允许继续
  // - { allow: false, reason: '...' } — 阻止执行
  // - { modified: {...} } — 修改参数后继续
  // - undefined — 不干预
  
  if (event.type === 'PreToolUse') {
    if (isDangerous(event.tool)) {
      return { allow: false, reason: 'Blocked by security policy' };
    }
  }
  
  return { allow: true };
};
```

### 典型 Hook 实现示例

#### security-check.js (PreToolUse)

```javascript
// scripts/hooks/pre-tool-use/security-check.js
const DANGEROUS_PATTERNS = [
  /rm\s+-rf\s+\//,
  /sudo\s+/,
  /curl.*\|\s*sh/,
  /wget.*\|\s*bash/,
];

module.exports = async function(event) {
  if (event.tool.name === 'execute_command') {
    const cmd = event.tool.params.command;
    for (const pattern of DANGEROUS_PATTERNS) {
      if (pattern.test(cmd)) {
        return {
          allow: false,
          reason: `Blocked dangerous command pattern: ${pattern}`
        };
      }
    }
  }
  return { allow: true };
};
```

#### token-guard.js (PreToolUse)

```javascript
// scripts/hooks/pre-tool-use/token-guard.js
const MAX_TOKENS_PER_SESSION = 100000;

module.exports = async function(event) {
  const usage = await getSessionTokenUsage(event.session.id);
  
  if (usage > MAX_TOKENS_PER_SESSION * 0.9) {
    // 接近预算上限，触发压缩
    await triggerCompaction(event.session);
  }
  
  if (usage > MAX_TOKENS_PER_SESSION) {
    return {
      allow: false,
      reason: 'Token budget exceeded. Please start a new session.'
    };
  }
  
  return { allow: true };
};
```

#### save-instinct.js (SessionEnd)

```javascript
// scripts/hooks/session-end/save-instinct.js
module.exports = async function(event) {
  const patterns = await analyzeSessionPatterns(event.session);
  
  for (const pattern of patterns) {
    await saveInstinct({
      pattern: pattern.description,
      action: pattern.action,
      confidence: pattern.frequency / event.session.totalActions,
      project: event.session.projectId,  // 项目级隔离
      source: event.session.id,
    });
  }
};
```

## 运行时控制

### 环境变量

| 变量 | 作用 | 示例值 |
|------|------|--------|
| `ECC_HOOK_PROFILE` | 选择钩子配置文件 | `dev` / `prod` / `minimal` |
| `ECC_DISABLED_HOOKS` | 禁用特定钩子 | `token-guard,log-action` |
| `ECC_HOOKS_DIR` | 自定义钩子目录 | `./my-hooks` |
| `ECC_HOOK_TIMEOUT` | 钩子执行超时 | `5000` (ms) |
| `ECC_HOOK_VERBOSE` | 详细日志 | `true` / `false` |

### Profile 配置

```bash
# 开发模式 — 最多日志，宽松限制
export ECC_HOOK_PROFILE=dev

# 生产模式 — 严格安全，性能优先
export ECC_HOOK_PROFILE=prod

# 最小模式 — 仅核心钩子
export ECC_HOOK_PROFILE=minimal
```

| Profile | 激活的 Hooks | 特点 |
|---------|-------------|------|
| `dev` | 全部 | 详细日志、宽松安全、调试信息 |
| `prod` | 安全 + 质量 + 学习 | 严格安全、性能优先 |
| `minimal` | 仅安全相关 | 最少开销、基本防护 |

### 禁用特定 Hook

```bash
# 禁用 token 守卫和日志钩子
export ECC_DISABLED_HOOKS=token-guard,log-action

# 禁用所有学习相关钩子
export ECC_DISABLED_HOOKS=save-instinct,load-instincts,pattern-detect
```

## 跨平台适配器

### DRY Adapter Pattern (Cursor)

Cursor 不重写 Hook 脚本，而是通过适配器**复用** Claude Code 的实现：

```javascript
// cursor/hooks/adapter.js
const ccHooks = require('../../scripts/hooks/');

// 事件转换层
function transformCursorEvent(cursorEvent) {
  return {
    type: mapEventType(cursorEvent.type),  // Cursor → CC 事件映射
    tool: cursorEvent.tool,
    session: cursorEvent.session,
    context: cursorEvent.context,
    timestamp: Date.now(),
  };
}

// Cursor 事件 → CC Hook 转发
module.exports = async function(cursorEvent) {
  const ccEvent = transformCursorEvent(cursorEvent);
  const hook = ccHooks[ccEvent.type];
  if (hook) {
    return await hook(ccEvent);
  }
};
```

### 事件映射表

| Cursor 事件 | 映射到 CC 事件 | 说明 |
|------------|---------------|------|
| `onToolCall` | `PreToolUse` | 直接映射 |
| `onToolResult` | `PostToolUse` | 直接映射 |
| `onSessionStart` | `SessionStart` | 直接映射 |
| `onSessionEnd` | `SessionEnd` | 直接映射 |
| `onFileOpen` | — | Cursor 独有，无需映射 |
| `onFileSave` | `PostEdit` | 近似映射 |
| `onDiagnostic` | — | Cursor 独有 |

## Hook 与其他组件的交互

```
┌───────────────────────────────────────────────────┐
│                   Hooks 系统                       │
│                                                   │
│  SessionStart ──▶ 加载 Skills, Rules, Instincts   │
│  PreToolUse  ──▶ 执行 Rules 检查, 安全验证        │
│  PostToolUse ──▶ 触发 Skills (如 code-review)     │
│  SessionEnd  ──▶ 保存 Instincts, 归档数据        │
│                                                   │
└───────────────────────────────────────────────────┘
         │              │              │
         ▼              ▼              ▼
   ┌──────────┐  ┌──────────┐  ┌──────────┐
   │  Skills  │  │  Rules   │  │Instincts │
   │  (触发)   │  │  (检查)   │  │  (学习)   │
   └──────────┘  └──────────┘  └──────────┘
```

## 性能考量

### 执行开销

| 因素 | 影响 | 优化建议 |
|------|------|---------|
| Hook 数量 | 每个事件触发 N 个 hooks | 使用 Profile 限制 |
| 脚本复杂度 | 同步阻塞增加延迟 | 异步执行，设置超时 |
| I/O 操作 | 文件/网络操作阻塞 | 缓存，延迟写入 |
| 跨平台转换 | 事件映射开销 | 预编译映射表 |

### 最佳实践

1. **快速返回**: Hook 脚本应在 100ms 内完成
2. **异步非阻塞**: I/O 操作使用 async/await
3. **按需加载**: 使用 Profile 只激活必要的 Hooks
4. **错误容忍**: Hook 失败不应阻塞主流程
5. **缓存利用**: 频繁读取的数据应缓存

## 问题与思考

- [x] Hook 脚本的接口? → module.exports = async function(event)
- [x] 如何阻止危险操作? → PreToolUse 返回 { allow: false }
- [x] Cursor 如何复用? → DRY Adapter + 事件映射
- [ ] Hook 执行顺序是否可配置?
- [ ] 并发 Hook 的冲突解决机制?
- [ ] Hook 脚本的热重载支持?
- [ ] OpenCode 11 个事件的完整列表?

## 学习记录

| 日期 | 内容 | 来源 |
|------|------|------|
| 2026-07 | 初始整理: 事件类型、脚本架构、运行时控制、跨平台适配 | GitHub 仓库结构分析 |

---

*参考: [architecture.md](../architecture.md) | [04-cross-harness.md](./04-cross-harness.md)*
