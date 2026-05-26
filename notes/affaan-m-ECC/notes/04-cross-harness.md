# 跨 Harness 设计学习笔记

> ECC 的"一套配置、十个工具"哲学 — DRY Adapter Pattern 与 AGENTS.md 通用文件。

## 系统概述

ECC 的核心设计目标之一是**不绑定单一工具**。它通过 DRY Adapter Pattern 和通用入口文件 (AGENTS.md)，让同一套 Skills、Hooks、Rules 能在 10+ 个 AI 编码工具中工作。

### 核心数据

| 属性 | 值 |
|------|-----|
| 支持工具数 | 10+ |
| 主要目标 | Claude Code |
| 适配模式 | DRY Adapter Pattern |
| 通用入口 | AGENTS.md |
| Hook 事件差异 | 8 (CC) / 15 (Cursor) / 11 (OpenCode) |

## 支持的 Harness 列表

### 三级支持体系

| 级别 | 说明 | 包含工具 |
|------|------|---------|
| **Primary** | 原生完整支持，所有功能可用 | Claude Code |
| **Full** | 完整适配，核心功能对等 | Cursor, Codex, OpenCode |
| **Supported** | 配置映射，基础功能可用 | Gemini CLI, Zed, Copilot, Antigravity, JoyCode, Qwen CLI |

### 各 Harness 详情

| Harness | 级别 | 适配方式 | Hook 事件数 | 说明 |
|---------|------|---------|-----------|------|
| Claude Code | Primary | 原生 | 8 | 一切功能完整 |
| Cursor IDE | Full | DRY Adapter | 15 | 复用 CC Hook 脚本 |
| Codex (macOS) | Full | 独立适配 | — | macOS 桌面应用 |
| Codex (CLI) | Full | 独立适配 | — | 命令行模式 |
| OpenCode | Full | Plugin | 11 | 插件方式集成 |
| Gemini CLI | Supported | 配置映射 | — | Google AI 工具 |
| Zed | Supported | 配置映射 | — | Zed 编辑器 |
| GitHub Copilot | Supported | 配置映射 | — | Copilot 增强 |
| Antigravity | Supported | 配置映射 | — | 社区工具 |
| JoyCode/CodeBuddy | Supported | 配置映射 | — | 社区工具 |
| Qwen CLI | Supported | 配置映射 | — | 阿里云 AI 工具 |

## DRY Adapter Pattern 详解

### 设计哲学

```
DRY = Don't Repeat Yourself

核心思想: 不为每个工具重写 Hook/Skill，而是通过适配器转换事件格式
```

### 架构图

```
┌─────────────────────────────────────────────────┐
│              ECC Core (唯一的实现)                │
│                                                  │
│  scripts/hooks/pre-tool-use/security-check.js   │
│  scripts/hooks/session-end/save-instinct.js     │
│  skills/tdd-workflow/SKILL.md                   │
│  rules/common/code-quality.md                   │
│  ... (所有配置和脚本)                            │
└────────────────────────┬────────────────────────┘
                         │
           ┌─────────────┼─────────────────┐
           ▼             ▼                 ▼
   ┌──────────────┐ ┌──────────────┐ ┌──────────────┐
   │ Claude Code  │ │   Cursor     │ │   OpenCode   │
   │  (直接使用)   │ │  (Adapter)   │ │  (Plugin)    │
   └──────────────┘ └──────┬───────┘ └──────┬───────┘
                           │                 │
                           ▼                 ▼
                    ┌──────────────┐  ┌──────────────┐
                    │  Event       │  │   Event      │
                    │  Transform   │  │   Transform  │
                    │  (事件转换)   │  │   (事件转换)  │
                    └──────────────┘  └──────────────┘
```

### Cursor DRY Adapter 实现

```javascript
// cursor/hooks/adapter.js — DRY 适配器核心

const path = require('path');

// CC Hook 脚本的引用（不复制！）
const CC_HOOKS_DIR = path.resolve(__dirname, '../../scripts/hooks');

// Cursor 事件 → Claude Code 事件映射
const EVENT_MAP = {
  // 直接映射
  'cursor:onToolCall':     'PreToolUse',
  'cursor:onToolResult':   'PostToolUse',
  'cursor:onSessionStart': 'SessionStart',
  'cursor:onSessionEnd':   'SessionEnd',
  'cursor:onStop':         'Stop',
  
  // 近似映射
  'cursor:onFileSave':     'PostEdit',
  'cursor:onFileCreate':   'PostEdit',
  
  // Cursor 独有（无映射，使用 Cursor 专属处理）
  'cursor:onFileOpen':      null,
  'cursor:onFileClose':     null,
  'cursor:onSelectionChange': null,
  'cursor:onDiagnostic':    null,
  'cursor:onTerminalOutput': null,
  'cursor:onCompletionAccept': null,
  'cursor:onInlineEdit':    null,
  'cursor:onChatMessage':   null,
};

// 适配器主函数
async function adaptCursorEvent(cursorEvent) {
  const ccEventType = EVENT_MAP[cursorEvent.type];
  
  if (ccEventType === null) {
    // Cursor 独有事件，使用专属处理器
    return handleCursorOnly(cursorEvent);
  }
  
  if (ccEventType) {
    // 转换事件格式
    const ccEvent = {
      type: ccEventType,
      tool: cursorEvent.tool,
      session: cursorEvent.session,
      context: cursorEvent.context,
      file: cursorEvent.file,
      timestamp: Date.now(),
    };
    
    // 调用 CC Hook 脚本
    const hookDir = path.join(CC_HOOKS_DIR, kebabCase(ccEventType));
    const hooks = loadHooksFromDir(hookDir);
    
    for (const hook of hooks) {
      const result = await hook(ccEvent);
      if (result && result.allow === false) {
        return result;  // 阻止执行
      }
    }
  }
  
  return { allow: true };
}

module.exports = { adaptCursorEvent };
```

### DRY 的优势

| 维度 | DRY Adapter | 独立重写 |
|------|-------------|---------|
| 维护成本 | 一处修改，全平台生效 | N 个平台 × N 处修改 |
| Bug 修复 | 修一次即可 | 每个平台单独修 |
| 功能一致性 | 保证一致 | 容易出现偏差 |
| 代码量 | 少 (仅适配器层) | 多 (完整重复) |
| 更新同步 | 自动 | 需手动同步 |

## AGENTS.md 通用文件

### 角色

`AGENTS.md` 是 ECC 的**跨工具通用入口文件**，所有支持的 Harness 都能识别并加载它。

### 文件位置

```
项目根目录/
├── AGENTS.md          ← 通用入口 (所有工具识别)
├── .claude/           ← Claude Code 专属
├── .cursor/           ← Cursor 专属
├── .codex/            ← Codex 专属
└── .opencode/         ← OpenCode 专属
```

### 内容结构

```markdown
# AGENTS.md

## Project Context
- 项目类型、语言、框架
- 团队约定、代码风格

## Active Skills
- 列出当前项目激活的 Skills

## Rules
- 列出当前项目适用的 Rules

## Hooks
- 声明 Hook 配置

## Instincts
- 列出已进化的高置信度 Instincts
```

### 为什么 AGENTS.md 能跨工具?

| 原因 | 说明 |
|------|------|
| 格式简单 | 纯 Markdown，任何工具都能解析 |
| 位置统一 | 始终在项目根目录 |
| 语义明确 | 文件名自描述 |
| 社区共识 | 多个工具已约定识别此文件 |

## 功能对等表

### 核心功能支持矩阵

| 功能 | Claude Code | Cursor | Codex | OpenCode | 其他 |
|------|:-----------:|:------:|:-----:|:--------:|:----:|
| Skills 加载 | ✅ | ✅ | ✅ | ✅ | ⚠️ |
| Hooks 执行 | ✅ (8) | ✅ (15) | ❌ | ✅ (11) | ❌ |
| Rules 应用 | ✅ | ✅ | ✅ | ✅ | ✅ |
| Commands | ✅ | ⚠️ | ⚠️ | ⚠️ | ❌ |
| Instincts | ✅ | ✅ | ⚠️ | ✅ | ❌ |
| Agents 调度 | ✅ | ⚠️ | ⚠️ | ⚠️ | ❌ |
| MCP 集成 | ✅ | ✅ | ❌ | ✅ | ⚠️ |
| AGENTS.md | ✅ | ✅ | ✅ | ✅ | ✅ |
| Token 优化 | ✅ | ⚠️ | ❌ | ⚠️ | ❌ |
| AgentShield | ✅ | ✅ | ✅ | ✅ | ✅ |

图例: ✅ 完整支持 | ⚠️ 部分支持 | ❌ 不支持

### 功能差异原因

| 限制因素 | 影响 |
|---------|------|
| Hook API 差异 | 各工具的事件系统不同 |
| 插件能力 | 部分工具不支持插件 |
| 上下文注入 | 不同工具的 system prompt 注入方式不同 |
| 工具调用 | 各 Harness 的 tool calling 格式不同 |

## 新增 Harness 适配指南

### 适配步骤

```
1. 确定支持级别 (Full / Supported)
2. 分析事件系统 (有哪些 Hook 点?)
3. 创建事件映射 (新工具事件 → CC 事件)
4. 实现适配器 (参考 Cursor DRY Adapter)
5. 配置 AGENTS.md 加载
6. 测试功能对等
```

### 适配器模板

```javascript
// <harness>/adapter.js — 通用适配器模板

const CC_HOOKS = require('../scripts/hooks/');

const EVENT_MAP = {
  // 填入新工具的事件 → CC 事件映射
  '<new_tool>:event1': 'PreToolUse',
  '<new_tool>:event2': 'PostToolUse',
  // ...
};

async function adapt(event) {
  const ccEventType = EVENT_MAP[event.type];
  if (!ccEventType) return { allow: true };
  
  const ccEvent = transformEvent(event, ccEventType);
  return await CC_HOOKS.execute(ccEvent);
}

module.exports = { adapt };
```

## 目录组织

### 仓库中的跨 Harness 结构

```
ECC/
├── scripts/hooks/          # CC 原始 Hook 脚本 (唯一实现)
├── skills/                 # 通用 Skills (所有工具共享)
├── rules/                  # 通用 Rules (所有工具共享)
├── agents/                 # 通用 Agents (所有工具共享)
├── commands/               # 通用 Commands
│
├── cursor/                 # Cursor 适配层
│   ├── adapter.js          #   DRY Adapter
│   └── cursor-only/        #   Cursor 独有功能
│
├── codex/                  # Codex 适配层
│   └── ...
│
├── opencode/               # OpenCode 适配层
│   └── ...
│
├── gemini/                 # Gemini CLI 配置映射
├── zed/                    # Zed 配置映射
├── copilot/                # GitHub Copilot 配置映射
├── antigravity/            # Antigravity 配置映射
├── joycode/                # JoyCode/CodeBuddy 配置映射
├── qwen/                   # Qwen CLI 配置映射
│
└── AGENTS.md               # 通用入口文件
```

## 问题与思考

- [x] 为什么不每个工具单独实现? → DRY 降低维护成本
- [x] AGENTS.md 的角色? → 跨工具通用入口
- [x] Cursor 如何复用 CC 脚本? → DRY Adapter + 事件映射
- [ ] 新工具出现时适配的优先级排序?
- [ ] 事件映射精度丢失的处理方式?
- [ ] Cursor 独有事件 (如 SelectionChange) 的利用策略?
- [ ] 多工具并用时 (CC + Cursor) 的冲突处理?
- [ ] AGENTS.md 的版本兼容策略?

## 学习记录

| 日期 | 内容 | 来源 |
|------|------|------|
| 2026-07 | 初始整理: DRY Adapter、AGENTS.md、功能对等表、适配指南 | GitHub 仓库结构 + README |

---

*参考: [architecture.md](../architecture.md) | [02-hooks-architecture.md](./02-hooks-architecture.md)*
