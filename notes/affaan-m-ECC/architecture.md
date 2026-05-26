# ECC 架构总览

> 基于 GitHub 仓库 (affaan-m/ECC) 和公开资料整理。最后更新: 2026-07 (v2.0.0-rc.1)

## 零、项目基本信息

| 属性 | 值 |
|------|-----|
| 仓库 | https://github.com/affaan-m/ECC |
| 原名 | everything-claude-code |
| 作者 | Affaan Mustafa (@affaan-m) |
| 语言 | Markdown/YAML + Node.js/TypeScript/Python/Go/Java/Perl |
| 许可证 | MIT |
| npm 包 | ecc-universal, ecc-agentshield |
| 当前版本 | **v2.0.0-rc.1** |
| GitHub Stars | 182K+ |
| Forks | 28K+ |
| Contributors | 170+ |
| 荣誉 | Anthropic Hackathon 获奖项目 |

## 一、整体架构

ECC (Everything Claude Code) 是一个 **plugin-first** 的 Agent Harness 配置系统。它不是一个独立运行的 Agent，而是增强和优化现有 AI 编码工具（Claude Code、Cursor、Codex 等）行为的配置框架。

### 核心定位

```
ECC = Agent Harness 的"操作系统层"
    = Skills + Agents + Commands + Hooks + Rules + Instincts
    = 让 AI 编码工具按照最佳实践自动工作
```

### 架构层次图

```
┌─────────────────────────────────────────────────────────────┐
│              Harness Layer (工具接入层)                       │
│  Claude Code / Cursor / Codex / OpenCode / Gemini CLI       │
│  Zed / GitHub Copilot / Antigravity / JoyCode / Qwen CLI   │
│  (10+ 工具，DRY Adapter Pattern)                            │
└───────────────────────────┬─────────────────────────────────┘
                            │ AGENTS.md / 配置注入
                            ▼
┌─────────────────────────────────────────────────────────────┐
│              Control Plane (控制平面)                         │
│  ECC 2.0 Rust Daemon (dashboard/start/sessions/status/stop) │
│  Install Pipeline (manifest-driven selective install)        │
│  SQLite State Store + Query CLI                             │
└───────────────────────────┬─────────────────────────────────┘
                            │ 调度 / 触发
                            ▼
┌─────────────────────────────────────────────────────────────┐
│             Component Layer (组件层)                          │
│  Agents (61) / Skills (246) / Commands (76)                 │
│  Hooks (8~15 events) / Rules (34) / Instincts (CL v2)      │
│  MCP Configs / Contexts (dev/review/research)               │
└───────────────────────────┬─────────────────────────────────┘
                            │ 执行 / 反馈
                            ▼
┌─────────────────────────────────────────────────────────────┐
│            Security Layer (安全层)                            │
│  AgentShield: Red-Team / Blue-Team / Auditor Pipeline       │
│  1282 Tests / 102 Static Rules / Opus 4.6 驱动             │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│           Learning Layer (学习层)                             │
│  Continuous Learning v2 (Instincts)                         │
│  Session Observation → Confidence Scoring → Evolution       │
│  Project-Scoped (防止跨项目污染)                             │
└─────────────────────────────────────────────────────────────┘
```

## 二、六大核心构建块

### 2.1 Agents（智能体 — 61 个）

Agents 是 ECC 中的专业化子代理，每个 Agent 对应一个特定职责：

#### 目录结构

```
agents/
├── planner/
│   └── AGENT.md          # YAML frontmatter 定义 name, tools, model
├── architect/
├── tdd-guide/
├── code-reviewer/
├── security-reviewer/
├── build-error-resolver/
├── e2e-runner/
└── ... (共 61 个)
```

#### AGENT.md 格式

```markdown
---
name: planner
description: Strategic planning and task decomposition
model: sonnet
tools:
  - read_file
  - write_file
  - search
triggers:
  - /plan
  - complex task detection
---

# Planner Agent

## Responsibilities
...

## Workflow
...
```

#### 关键 Agents 列表

| Agent | 职责 |
|-------|------|
| planner | 策略规划与任务分解 |
| architect | 系统架构设计 |
| tdd-guide | 测试驱动开发引导 |
| code-reviewer | 代码审查 |
| security-reviewer | 安全审查 |
| build-error-resolver | 构建错误解决 |
| e2e-runner | 端到端测试执行 |

### 2.2 Skills（技能 — 246 个）

Skills 是 ECC 的**主要工作流表面**，比 Commands 更丰富：

#### 目录结构

```
skills/
├── coding-standards/
│   └── SKILL.md
├── backend-patterns/
├── frontend-patterns/
├── continuous-learning-v2/
├── tdd-workflow/
├── security-review/
├── eval-harness/
└── ... (共 246 个)
```

#### SKILL.md 格式

```markdown
---
name: tdd-workflow
version: 2.0.0
category: development
auto_trigger: true
triggers:
  - new feature request
  - bug fix
---

# TDD Workflow

## When to Activate
...

## Steps
1. Write failing test
2. Implement minimal code
3. Refactor
...
```

#### 技能分类

| 类别 | 数量 | 说明 |
|------|------|------|
| coding-standards | ~30 | 编码规范 |
| backend-patterns | ~25 | 后端模式 |
| frontend-patterns | ~25 | 前端模式 |
| continuous-learning-v2 | ~15 | 持续学习 |
| tdd-workflow | ~20 | 测试驱动 |
| security-review | ~15 | 安全审查 |
| eval-harness | ~10 | 评估测试 |
| 其他 | ~106 | 各类专项 |

### 2.3 Commands（命令 — 76 个）

Commands 是斜杠命令接口，在迁移到 Skills 过程中仍保留：

| 命令 | 说明 |
|------|------|
| `/plan` | 启动规划流程 |
| `/code-review` | 执行代码审查 |
| `/build-fix` | 修复构建错误 |
| `/tdd` | 启动 TDD 流程 |
| `/security-scan` | 安全扫描 |
| `/refactor` | 代码重构 |
| ... | 共 76 个命令 |

### 2.4 Hooks（钩子）

Hooks 是基于触发器的自动化系统，在特定事件发生时执行 Node.js 脚本：

#### 事件类型

| 平台 | 事件类型数 | 典型事件 |
|------|-----------|---------|
| Claude Code | 8 | PreToolUse, PostToolUse, Stop, SessionStart, SessionEnd 等 |
| Cursor | 15 | 扩展 Claude Code 事件 + IDE 特有事件 |
| OpenCode | 11 | 适配 OpenCode 生命周期事件 |

#### 脚本位置

```
scripts/hooks/
├── pre-tool-use/
│   ├── security-check.js
│   └── token-guard.js
├── post-tool-use/
│   └── log-action.js
├── session-start/
│   └── load-context.js
├── session-end/
│   └── save-instinct.js
└── stop/
    └── cleanup.js
```

#### 运行时控制

| 环境变量 | 作用 |
|---------|------|
| `ECC_HOOK_PROFILE` | 选择钩子配置文件（dev/prod/minimal） |
| `ECC_DISABLED_HOOKS` | 禁用特定钩子（逗号分隔） |

### 2.5 Rules（规则 — 34 条）

Rules 是必须始终遵循的指令，按语言分类组织：

```
rules/
├── common/              # 通用规则
│   ├── code-quality.md
│   ├── documentation.md
│   └── git-workflow.md
├── typescript/          # TypeScript 专用
├── python/              # Python 专用
├── golang/              # Go 专用
├── swift/               # Swift 专用
├── php/                 # PHP 专用
└── arkts/               # ArkTS 专用
```

### 2.6 Instincts（直觉 — 持续学习 v2）

Instincts 是 ECC 独创的自动学习系统：

```
Session 观察 → 原子直觉创建 → 置信度评分 → 进化
                                              │
                                    ┌─────────┼─────────┐
                                    ▼         ▼         ▼
                                  Skill   Command    Agent
```

#### 生命周期

1. **观察**: Session 结束时分析对话模式
2. **提取**: 生成原子级别的行为直觉
3. **评分**: 基于出现频率和成功率打分
4. **隔离**: 项目级作用域，防止跨项目污染
5. **进化**: 高置信度直觉升级为 Skill/Command/Agent

## 三、跨 Harness 架构

### DRY Adapter Pattern

ECC 的核心设计原则是**一套配置，多工具复用**：

```
┌─────────────────────────────────────────┐
│          ECC Core (统一配置源)            │
│  Skills / Agents / Commands / Rules      │
└────────────────────┬────────────────────┘
                     │
      ┌──────────────┼──────────────┐
      ▼              ▼              ▼
┌──────────┐  ┌──────────┐  ┌──────────┐
│Claude Code│  │  Cursor  │  │  Codex   │
│ (primary) │  │ (adapter)│  │(macOS+CLI)│
└──────────┘  └──────────┘  └──────────┘
      │              │              │
      ▼              ▼              ▼
  原生 Hooks     复用 CC 脚本   独立适配
```

#### Cursor DRY Adapter

Cursor 适配器**直接复用** Claude Code 的 Hook 脚本，而非重写：

```javascript
// cursor-adapter.js — 典型 DRY 复用模式
const ccHook = require('../claude-code/hooks/pre-tool-use/security-check.js');
module.exports = (event) => ccHook(transformEvent(event));
```

#### 支持的 Harness

| Harness | 支持级别 | 适配方式 |
|---------|---------|---------|
| Claude Code | Primary | 原生支持 |
| Cursor IDE | Full | DRY Adapter |
| Codex (macOS + CLI) | Full | 独立适配 |
| OpenCode | Full | Plugin 支持 |
| Gemini CLI | Supported | 配置映射 |
| Zed | Supported | 配置映射 |
| GitHub Copilot | Supported | 配置映射 |
| Antigravity | Supported | 配置映射 |
| JoyCode/CodeBuddy | Supported | 配置映射 |
| Qwen CLI | Supported | 配置映射 |

#### AGENTS.md 通用文件

`AGENTS.md` 作为跨工具的统一入口文件，被所有支持的 Harness 识别。

## 四、安装管线

### Manifest-Driven Selective Install

ECC 使用清单驱动的选择性安装，不强制全量部署：

```
┌───────────────┐     ┌────────────────┐     ┌──────────────┐
│ install-plan.js│────▶│ install-plan   │────▶│install-apply.js│
│  (分析环境)    │     │ (生成清单)      │     │  (执行安装)    │
└───────────────┘     └────────────────┘     └──────────────┘
```

#### 安装方式

| 方式 | 命令 | 说明 |
|------|------|------|
| Plugin (推荐) | `ecc@ecc` (Claude Code Marketplace) | 一键安装 |
| npm 全局 | `npm install -g ecc-universal` | 全工具通用 |
| Selective | `node install-plan.js && node install-apply.js` | 按需选择 |

#### 安装流程

1. `install-plan.js` 检测当前环境（语言、框架、工具链）
2. 生成安装清单（选择相关的 Skills、Rules、Hooks）
3. 用户确认或修改清单
4. `install-apply.js` 执行安装（复制文件、配置钩子、注册命令）

## 五、Hook 系统详解

### 事件生命周期

```
SessionStart → [PreToolUse → ToolExecution → PostToolUse]* → Stop → SessionEnd
```

### Claude Code Hook 事件 (8 类)

| 事件 | 触发时机 | 典型用途 |
|------|---------|---------|
| SessionStart | 会话开始 | 加载上下文、初始化状态 |
| SessionEnd | 会话结束 | 保存学习、清理资源 |
| PreToolUse | 工具调用前 | 安全检查、权限验证 |
| PostToolUse | 工具调用后 | 日志记录、结果验证 |
| Stop | Agent 停止 | 清理、状态持久化 |
| PreEdit | 文件编辑前 | 备份、格式检查 |
| PostEdit | 文件编辑后 | Lint、测试触发 |
| Error | 错误发生 | 错误恢复、通知 |

### 运行时配置

```bash
# 使用开发配置文件
export ECC_HOOK_PROFILE=dev

# 禁用特定钩子
export ECC_DISABLED_HOOKS=token-guard,log-action

# 自定义钩子目录
export ECC_HOOKS_DIR=./my-hooks
```

## 六、持续学习 v2 (Instincts)

### 系统架构

```
┌─────────────────────────────────────────┐
│           Session Observer               │
│  监听: 对话模式、工具使用、错误恢复       │
└────────────────────┬────────────────────┘
                     │ 提取
                     ▼
┌─────────────────────────────────────────┐
│         Instinct Generator               │
│  生成: 原子级行为直觉                     │
│  格式: { pattern, action, confidence }   │
└────────────────────┬────────────────────┘
                     │ 评分
                     ▼
┌─────────────────────────────────────────┐
│        Confidence Scorer                 │
│  指标: 频率、成功率、一致性              │
│  阈值: 低(<0.3) / 中(0.3-0.7) / 高(>0.7)│
└────────────────────┬────────────────────┘
                     │ 存储/进化
                     ▼
┌─────────────────────────────────────────┐
│         Project Store (SQLite)           │
│  隔离: 项目级作用域                      │
│  进化: instinct → skill / command / agent│
└─────────────────────────────────────────┘
```

### 关键设计决策

- **项目作用域**: 每个项目独立的直觉存储，防止跨项目污染
- **原子性**: 每个直觉只包含一个行为模式
- **渐进式**: 从观察到正式组件的渐进升级路径
- **可回溯**: 所有直觉都有来源追踪

## 七、AgentShield 安全管线

### 三阶段管线

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  Red Team    │────▶│  Blue Team   │────▶│   Auditor    │
│  (攻击模拟)   │     │  (防御验证)   │     │  (审计报告)   │
└──────────────┘     └──────────────┘     └──────────────┘
```

| 阶段 | 职责 | 驱动模型 |
|------|------|---------|
| Red Team | 模拟攻击场景，尝试绕过防护 | Opus 4.6 |
| Blue Team | 验证防御有效性，修补漏洞 | Opus 4.6 |
| Auditor | 生成审计报告，给出安全评分 | Opus 4.6 |

### 测试规模

| 指标 | 数值 |
|------|------|
| 自动化测试 | 1282 个 |
| 静态规则 | 102 条 |
| 扫描模式 | 多种（快速/深度/全量） |

## 八、ECC 2.0 Rust Control-Plane

### 架构概览

ECC 2.0 引入了 Rust 编写的控制平面，提供 daemon 式管理：

```
┌──────────────────────────────────┐
│     ECC 2.0 Rust Control-Plane   │
│                                  │
│  ┌───────────┐  ┌────────────┐  │
│  │ Dashboard │  │  Sessions  │  │
│  │  (GUI)    │  │  (管理)    │  │
│  └───────────┘  └────────────┘  │
│                                  │
│  ┌───────────┐  ┌────────────┐  │
│  │   Start   │  │   Status   │  │
│  │  (启动)    │  │  (状态)    │  │
│  └───────────┘  └────────────┘  │
│                                  │
│  ┌───────────┐  ┌────────────┐  │
│  │   Stop    │  │   Resume   │  │
│  │  (停止)    │  │  (恢复)    │  │
│  └───────────┘  └────────────┘  │
│                                  │
│  ┌───────────────────────────┐  │
│  │        Daemon             │  │
│  │  (后台守护进程)            │  │
│  └───────────────────────────┘  │
└──────────────────────────────────┘
```

### CLI 命令

```bash
ecc dashboard    # 启动 GUI 仪表板
ecc start        # 启动 ECC 会话
ecc sessions     # 列出活跃会话
ecc status       # 查看系统状态
ecc stop         # 停止会话
ecc resume       # 恢复会话
ecc daemon       # 守护进程管理
```

### 设计目标

- **性能**: Rust 编写，低内存占用，快速启动
- **可靠性**: 进程管理、崩溃恢复
- **可视化**: Dashboard GUI 提供系统全貌
- **会话管理**: 多会话并发，状态持久化

## 九、MCP 配置

ECC 集成多个 MCP (Model Context Protocol) Server：

| MCP Server | 用途 |
|------------|------|
| GitHub | 代码仓库操作 |
| Supabase | 数据库操作 |
| Vercel | 部署管理 |
| Railway | 基础设施管理 |
| Context7 | 上下文管理 |
| Exa | 搜索引擎 |
| Playwright | 浏览器自动化 |
| Sequential Thinking | 推理辅助 |
| Memory | 记忆管理 |

⚠️ **Token 优化建议**: 保持活跃 MCP 数量 ≤ 10，活跃工具 ≤ 80。

## 十、Contexts（动态上下文注入）

Contexts 提供基于场景的系统提示词注入：

| Context | 场景 | 注入内容 |
|---------|------|---------|
| `dev.md` | 开发模式 | 开发最佳实践、TDD 流程、代码标准 |
| `review.md` | 审查模式 | 代码审查标准、安全检查清单 |
| `research.md` | 研究模式 | 探索性编码、原型制作指南 |

## 十一、关键设计原则

1. **Plugin-First**: 通过 Marketplace 一键安装，降低入门门槛
2. **DRY Adapter**: 一套配置多工具复用，减少维护成本
3. **Manifest-Driven**: 按需安装，不强制全量
4. **Project-Scoped Learning**: 学习隔离，防止跨项目污染
5. **Token-Aware**: 内置 token 优化策略，控制成本
6. **Security-First**: AgentShield 管线确保安全
7. **Progressive Enhancement**: 从 Instinct 到 Skill 到 Agent 的渐进式进化
8. **Cross-Harness**: 不绑定单一工具，支持 10+ Harness

## 十二、待深入研究

- [ ] ECC 2.0 Rust control-plane 的具体实现细节
- [ ] AgentShield 的 Red Team 攻击向量分类
- [ ] Instincts 置信度评分的具体算法
- [ ] SQLite State Store 的 schema 设计
- [ ] Operator workflows 的编排机制
- [ ] NanoClaw v2 与 Harness Performance System 的关系
- [ ] 12 语言生态系统的 Rules 覆盖范围

---

*参考来源见 [references.md](./references.md)*
*最后更新: 2026-07*
