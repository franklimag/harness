# Agent Harness 学习资源库

> 收集热门的 Agent Harness 相关仓库信息，并记录学习计划和学习笔记。

## 什么是 Agent Harness？

Agent Harness 是包裹在 AI 模型外部的**软件基础设施层**，用于管理 Agent 的生命周期、上下文和与外部世界的交互。它不是做"思考"的大脑，而是为大脑提供工具、记忆和安全边界的环境。

> "If you're not the model, you're the harness." — LangChain

### 核心组成

| 组件 | 职责 |
|------|------|
| 编排循环 (Orchestration Loop) | 调用模型 → 决定工具 → 传递结果 → 管理上下文窗口 → 处理失败 |
| 工具执行 (Tool Execution) | 文件系统、Shell、浏览器、API 等工具的调用和结果收集 |
| 状态与持久化 (State & Persistence) | 会话管理、检查点、长期记忆 |
| 安全与治理 (Security & Governance) | 沙箱隔离、权限控制、人机协作审批 |
| 可观测性 (Observability) | 日志、追踪、Token 用量监控 |
| 评估 (Evaluation) | 基准测试、能力评估、回归检测 |

### 与相关概念的区别

| 概念 | 说明 |
|------|------|
| Agent Framework | 构建 Agent 的 SDK/库（如 LangChain, CrewAI） |
| Agent Harness | 运行 Agent 的基础设施（编排、沙箱、工具、安全） |
| Agent Benchmark | 评估 Agent 能力的测试集（如 SWE-bench） |
| Agent Sandbox | Harness 的子集，专注于隔离执行环境 |

## 收录项目

| 项目 | 核心定位 | 详情 |
|------|---------|------|
| CodeGraph | 预索引代码知识图谱，减少 Agent 92% 工具调用 | [详情](repos/codegraph.md) |

> 由项目维护者手动指定，详见 [repos/README.md](repos/README.md)

## 项目结构

```
.
├── README.md                  # 本文件 — 项目总览
├── repos/                     # 各仓库的详细信息
│   ├── README.md              # 仓库汇总与分类
│   ├── _template.md           # 仓库信息模板
│   └── <project>.md           # 每个项目一个文件
├── docs/                      # 文档与参考资料
│   ├── references.md          # 参考链接汇总
│   └── comparison.md          # 对比分析
├── plans/                     # 学习计划
│   └── README.md              # 学习路线
├── notes/                     # 学习笔记
│   └── README.md              # 笔记索引
└── .kiro/steering/            # Kiro 工作约定
```

## 相关资源

- [awesome-agent-harness (mahonzhan)](https://github.com/mahonzhan/awesome-agent-harness)
- [awesome-agent-harness (Picrew)](https://github.com/Picrew/awesome-agent-harness)
- [awesome-harness-engineering](https://github.com/ai-boost/awesome-harness-engineering)
- [Agent Harness Survey (preprints.org)](https://www.preprints.org/manuscript/202604.0428/v1)
- [The Anatomy of an Agent Harness (LangChain)](https://blog.langchain.com/the-anatomy-of-an-agent-harness/)
- [Agent Harness: Why the LLM Is the Smallest Part (MongoDB)](https://mongodb.com/ja-jp/company/blog/technical/agent-harness-why-llm-is-smallest-part-of-your-agent-system)
- [AWS Bedrock AgentCore Harness](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/harness.html)

---

*最后更新: 2026-05-26*
