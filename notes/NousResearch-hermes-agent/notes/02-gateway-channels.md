# 多平台通信网关学习笔记

> hermes-agent 的通信层 — 单一 Gateway 进程统一 6 种通信通道。

## 概述

hermes-agent 通过单一 Gateway 进程统一接入 6 种通信通道，使得同一个 Agent 实例可以通过任何渠道与用户交互。

### Gateway 架构

```
┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐
│ Telegram │ │ Discord  │ │  Slack   │ │ WhatsApp │ │  Signal  │
└────┬─────┘ └────┬─────┘ └────┬─────┘ └────┬─────┘ └────┬─────┘
     │             │             │             │             │
     └─────────────┴─────────────┴─────────────┴─────────────┘
                                 │
                                 ▼
                   ┌─────────────────────────┐
                   │     Gateway Process      │
                   │                         │
                   │ ┌─────────────────────┐ │
                   │ │ 消息标准化           │ │
                   │ │ Channel → Unified   │ │
                   │ └─────────────────────┘ │
                   │ ┌─────────────────────┐ │
                   │ │ 鉴权 & 路由         │ │
                   │ │ User → Agent        │ │
                   │ └─────────────────────┘ │
                   │ ┌─────────────────────┐ │
                   │ │ 响应格式化           │ │
                   │ │ Unified → Channel   │ │
                   │ └─────────────────────┘ │
                   └────────────┬────────────┘
                                │
                                ▼
                          Agent Core
```

## 通道详情

### CLI (TUI)

| 属性 | 值 |
|------|-----|
| 类型 | 本地终端 |
| 特点 | 多行编辑、斜杠命令、流式输出 |
| 延迟 | 零（本地进程） |
| 适用 | 开发调试、重度使用 |

### Telegram

| 属性 | 值 |
|------|-----|
| API | Telegram Bot API |
| 特点 | 移动优先、Markdown 支持 |
| 延迟 | 低 (WebSocket/长轮询) |
| 适用 | 随时随地、快速交互 |

### Discord

| 属性 | 值 |
|------|-----|
| API | Discord Bot API |
| 特点 | 频道管理、线程、嵌入 |
| 延迟 | 低 |
| 适用 | 团队协作、社区 |

### Slack

| 属性 | 值 |
|------|-----|
| API | Slack Bolt / Web API |
| 特点 | 企业集成、App Home |
| 延迟 | 低 |
| 适用 | 工作场景、企业 |

### WhatsApp

| 属性 | 值 |
|------|-----|
| API | WhatsApp Business API |
| 特点 | 端到端加密、全球用户 |
| 延迟 | 中 |
| 适用 | 安全通信、移动 |

### Signal

| 属性 | 值 |
|------|-----|
| 协议 | Signal Protocol |
| 特点 | 隐私优先、零知识 |
| 延迟 | 中 |
| 适用 | 高安全要求场景 |

## 消息标准化

### 统一消息格式

```python
class UnifiedMessage:
    channel: str       # "telegram" | "discord" | "slack" | ...
    user_id: str       # 统一用户标识
    content: str       # 消息文本
    attachments: list  # 附件（图片、文件）
    thread_id: str     # 会话线程 ID
    timestamp: float   # Unix 时间戳
    metadata: dict     # 通道特定元数据
```

### 响应适配

```
Agent 返回统一格式:
  { text: "代码如下:\n```python\nprint('hello')\n```" }

Gateway 适配到各通道:
  Telegram: Markdown 格式 (原生支持)
  Discord: Markdown + 代码块
  Slack: mrkdwn 格式转换
  CLI: ANSI 彩色输出
```

## 与其他系统的对比

| 维度 | hermes-agent Gateway | OpenClaw Channels |
|------|---------------------|-------------------|
| 通道数 | 6 | 50+ |
| 架构 | 单一进程 | 多 Channel Adapter |
| 鉴权 | 内置 | 分离的 Auth 层 |
| 格式化 | Gateway 内部 | Channel 层负责 |

## 问题与思考

- [x] 为什么单一 Gateway? → 降低运维复杂度
- [x] 通道差异如何处理? → 消息标准化 + 响应适配
- [ ] 多通道同时在线的会话管理?
- [ ] 大消息（长代码块）在各通道的限制?
- [ ] 附件（图片/文件）的跨通道处理?

---

*参考: [architecture.md](../architecture.md) | [03-terminal-backends.md](./03-terminal-backends.md)*
