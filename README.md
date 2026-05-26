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

## 收录项目

| 项目 | 核心定位 | 信息 | 笔记 |
|------|---------|------|------|
| openclaw/openclaw | 自托管 AI Agent 框架（350k⭐） | [信息](repos/openclaw-openclaw.md) | [笔记](notes/openclaw-openclaw/) |
| affaan-m/ECC | Agent Harness 性能优化系统（140k⭐） | [信息](repos/affaan-m-ECC.md) | [笔记](notes/affaan-m-ECC/) |
| obra/superpowers | Agent 开发方法论框架（57k⭐） | [信息](repos/obra-superpowers.md) | [笔记](notes/obra-superpowers/) |
| thedotmack/claude-mem | 跨 Session 持久化记忆（72k⭐） | [信息](repos/thedotmack-claude-mem.md) | [笔记](notes/thedotmack-claude-mem/) |
| safishamsi/graphify | 多模态知识图谱 Skill（30k⭐） | [信息](repos/safishamsi-graphify.md) | [笔记](notes/safishamsi-graphify/) |
| mattpocock/skills | 可组合 Agent Skills 集合（20k⭐） | [信息](repos/mattpocock-skills.md) | [笔记](notes/mattpocock-skills/) |
| NousResearch/hermes-agent | 自进化 Agent（学习循环 + 多平台） | [信息](repos/NousResearch-hermes-agent.md) | [笔记](notes/NousResearch-hermes-agent/) |
| colbymchenry/codegraph | 预索引代码知识图谱（92% 工具调用减少） | [信息](repos/colbymchenry-codegraph.md) | [笔记](notes/colbymchenry-codegraph/) |

## 项目结构

```
.
├── README.md                          # 本文件 — 项目总览
├── repos/                             # 各仓库的信息卡片
│   ├── README.md                      # 仓库汇总
│   ├── _template.md                   # 信息卡片模板
│   └── <owner>-<repo_name>.md         # 每个项目一个文件
├── notes/                             # 学习笔记（每个 repo 独立目录）
│   ├── README.md                      # 笔记索引
│   └── <owner>-<repo_name>/           # 每个 repo 的学习目录
│       ├── README.md                  # 学习总览 + 进度
│       ├── architecture.md            # 架构解读
│       ├── changelog.md               # 版本更新追踪
│       ├── references.md              # 参考资料
│       └── notes/                     # 模块学习笔记
├── docs/                              # 通用文档与参考资料
│   ├── references.md                  # 全局参考链接
│   └── comparison.md                  # 跨项目对比分析
├── plans/                             # 学习计划
│   └── README.md                      # 学习路线
└── .kiro/steering/                    # Kiro 工作约定
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
