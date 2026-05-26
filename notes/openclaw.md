# OpenClaw 学习笔记

> 引用自独立学习仓库：https://github.com/franklimag/openclaw

## 学习仓库说明

OpenClaw 的学习内容已在独立仓库中系统整理，包含架构分析、模块笔记和版本追踪。

## 仓库结构

```
franklimag/openclaw/
├── README.md                  # 项目简介 + 学习路线
├── docs/
│   ├── architecture.md        # 架构总览 (四层模型 + 组件详解)
│   ├── changelog.md           # 版本更新追踪 (2025 Q4 ~ 2026.5.22)
│   ├── references.md          # 参考资料汇总 (70+ 链接)
│   └── learning-guide.md      # 学习指南
├── notes/
│   ├── 01-gateway.md          # Gateway 控制平面 (WebSocket/Session/Queue)
│   ├── 02-agent-loop.md       # Agentic Loop (Observe-Plan-Act/Codex)
│   ├── 03-memory.md           # 记忆系统 (SOUL/AGENTS/Activation-Decay)
│   ├── 04-tools-skills.md     # Tools/Skills/Plugins (ClawHub/MCP/Browser)
│   ├── 05-channels.md         # 消息通道 (50+ 平台/BlueBubbles/Voice)
│   ├── 06-security.md         # 安全机制 (攻击面/事件/加固)
│   └── 07-team-setup.md       # 团队配置
└── experiments/               # 实验代码/配置
```

## 学习进度

| 阶段 | 主题 | 状态 |
|------|------|------|
| 阶段一 | 理解整体架构（四层模型） | ✅ 完成 |
| 阶段二 | 深入 Gateway WebSocket 和 Pi Agent Core | 进行中 |
| 阶段三 | Memory/SOUL 系统和 Activation/Decay | 未开始 |
| 阶段四 | Skills/Plugins 开发和 ClawHub 生态 | 未开始 |
| 阶段五 | 跟踪安全事件和版本更新 | 持续 |

## 核心笔记摘要

### 01 - Gateway

OpenClaw 的控制平面 — WebSocket 服务器 (port 18789)，统管所有通道和 Agent：
- Channel Adapters: 50+ 平台适配
- Session 管理: append-only transcript + mutable index
- Lane Queue: 同 session 串行执行
- 路由: 消息分配到正确 Agent 实例

### 02 - Agent Loop

核心区别于普通聊天机器人的 Observe-Plan-Act 循环：
- 自主链式调用（无需每步确认）
- Compaction（长上下文自动压缩）
- Stalled-stream Recovery（流中断恢复）
- Runtime Fallbacks（模型失败自动切换）

### 03 - Memory

Markdown-first 设计，透明可审计：
- SOUL.md / AGENTS.md / TOOLS.md / IDENTITY.md
- SQLite Index 加速语义检索
- Activation/Decay 记忆活跃度系统

### 04 - Tools/Skills

AgentSkills 格式的能力扩展：
- ClawHub 市场 1200+ skills
- 安全分级 A-F
- MCP 兼容
- defineToolPlugin API

### 05 - Channels

50+ 平台支持：
- 内置通道 + 通道插件
- Android 实时语音 (2026.5.18+)
- 跨平台对话连续性

### 06 - Security

真实安全事件和防御策略：
- ClawJacked / Claw Chain / ClawHub 投毒
- Exec Policy Engine 为最主要攻击入口
- Container Boundary 沙箱逃逸风险

## 与本仓库其他项目的关联

| 对比维度 | OpenClaw | 关联项目 |
|---------|----------|---------|
| Gateway 模式 | WebSocket + Channel Adapters | Hermes Agent (类似 Gateway 架构) |
| 记忆系统 | Markdown-first + Activation/Decay | Claude-Mem (Hook+压缩) |
| Skills 系统 | AgentSkills 标准 + ClawHub | Superpowers / ECC / Skills |
| 知识图谱 | 无内置 | CodeGraph / Graphify |

---

*完整笔记请访问: https://github.com/franklimag/openclaw*

*最后更新: 2026-05-26*
