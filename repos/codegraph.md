# CodeGraph

> 预索引代码知识图谱 — 为 AI 编程 Agent 提供本地优先的代码理解能力

## 基本信息

| 属性 | 值 |
|------|-----|
| 仓库 | https://github.com/colbymchenry/codegraph |
| 作者 | Colby McHenry (@colbymchenry) |
| 语言 | TypeScript |
| 定位 | 本地代码知识图谱，减少 AI Agent 的 token 消耗和工具调用 |
| 接口 | MCP Server (45 tools) + VS Code 扩展 |
| 解析器 | Tree-sitter (支持 37 种语言) |
| 衍生项目 | [tokensave](https://github.com/aovestdipaperino/tokensave) (Rust 移植版) |

## 核心特点

- **预索引**: 提前构建代码的语义知识图谱（函数、类、导入、调用链）
- **大幅降低成本**: 平均减少 92% 工具调用，71% 更快的代码探索，96% 更少的 token
- **100% 本地**: 无外部数据库依赖，完全在本地运行
- **37 语言支持**: 通过 Tree-sitter 解析 TypeScript, Python, Rust, Java, Swift, C++ 等
- **45 MCP 工具**: 通过 MCP 协议暴露丰富的代码查询能力
- **持久记忆层**: 支持代码结构的持久化理解

## 解决的问题

AI 编程 Agent (如 Claude Code) 对代码库没有记忆，每次都需要通过 grep、glob、Read 命令反复扫描文件。每次工具调用都消耗 token，每个扫描的文件都占用上下文窗口。

CodeGraph 通过预先建立代码的语义图谱，让 Agent 一次工具调用就能获取入口点、相关符号和代码片段，避免昂贵的探索性扫描。

## 架构概述

```
代码库 → Tree-sitter 解析 → 语义知识图谱
                                  ↓
                        符号关系 / 调用图 / 结构 / 路由
                                  ↓
                          MCP Server (45 tools)
                                  ↓
          AI Agent (Claude Code / Cursor / Codex CLI / OpenCode)
```

核心流程：
1. **索引阶段**: Tree-sitter 解析源码 → 提取符号、关系、调用链
2. **图谱构建**: 组织成可查询的知识图谱
3. **查询服务**: 通过 MCP 协议对外暴露结构化的代码理解能力
4. **Agent 消费**: AI Agent 单次调用获取精准上下文，无需反复探索

## 兼容的 AI Agent

- Claude Code
- Cursor
- Codex CLI (OpenAI)
- OpenCode
- GitHub Copilot (通过 VS Code 扩展)
- 任何支持 MCP 的客户端

## Benchmark 数据

测试覆盖 6 个真实开源项目（VS Code、Swift 编译器等），语言涵盖 TypeScript, Python, Rust, Java, Swift, C++：

| 指标 | 改善 |
|------|------|
| 工具调用减少 | 92% (平均) |
| 探索速度提升 | 71% |
| Token 消耗减少 | ~96% |

## 适用场景

- 大型代码库的 AI 辅助开发（减少 token 成本）
- Agent Harness 的上下文工程组件
- 代码理解和导航工具

## 学习价值

- ⭐ 理解如何通过预索引减少 Agent 的探索开销
- 学习 MCP Server 的实际实现（45 个工具的设计）
- 学习 Tree-sitter 在 AI 工具链中的应用
- 理解 "上下文工程 (Context Engineering)" 的实践方案
- 对比预注入 (pre-injection) vs MCP 工具循环的成本差异

## 参考资料

- [I Cut Claude Code Exploration Time and Costs by 90% With One Tool](https://medium.com/@me_82386/i-cut-my-claude-code-api-costs-by-40-with-one-tool-12cf4306a1ab)
- [Tired of Blowing Through Tokens? This Open Source Tool Cuts Tool Calls by 92%](https://ai-engineering-trend.medium.com/tired-of-blowing-through-tokens-with-claude-code-on-large-codebases-6289873475e6)
- [What is CodeGraph? (knightli.com)](https://knightli.com/en/2026/05/23/codegraph-local-code-knowledge-graph-ai-coding-agent/)
- [CodeGraph on LobeHub MCP](https://lobehub.com/mcp/jotaeme-codegraph)
- [VS Code 扩展](https://marketplace.visualstudio.com/items?itemName=aStudioPlus.codegraph)
- [Context Strategies for AI Coding Agents (对比分析)](https://ceaksan.com/en/pre-injection-vs-mcp-context-engineering/)
- [Rust 移植版 tokensave](https://github.com/aovestdipaperino/tokensave)

---

*最后更新: 2026-05-26*
