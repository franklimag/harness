# Graphify

> 将代码、文档、论文、图片、视频转化为可查询知识图谱的 AI 编程助手 Skill

## 基本信息

| 属性 | 值 |
|------|-----|
| 仓库 | https://github.com/safishamsi/graphify |
| 作者 | Safi Shamsi (@safishamsi) |
| 语言 | Python |
| 定位 | 多模态知识图谱 Skill —— 任意文件夹转为可查询图谱 |
| Stars | 30k+ (发布 10 天达 22k) |
| PyPI | [graphifyy](https://pypi.org/project/graphifyy/) (注意双 y) |
| 官网 | https://graphify.net/ |
| 发布 | 2026 年 4 月 |

## 核心特点

- **多模态输入**: 代码、SQL、R、Shell、文档、PDF、论文、图片、白板照片、视频 —— 全部统一建图
- **Claude Vision 集成**: 使用 Claude Vision 从图片/视频中提取概念和关系
- **统一图谱**: App 代码 + 数据库 Schema + 基础设施在一个图中
- **大幅省 Token**: 71.5x token 减少（无需每次重读原始文件）
- **持久化**: 构建一次，持续查询
- **多 Agent 支持**: Claude Code, Codex, OpenCode, Cursor, Gemini CLI, Copilot, Aider 等
- **纯 Skill 实现**: 在 Claude Code 中输入 `/graphify` 即可使用

## 解决的问题

AI 编程助手无法理解代码库的"全局结构" —— 它们靠 grep 和逐文件阅读来理解项目。对于包含多种文件类型的复杂项目（代码 + 数据库 + 文档 + 架构图），这种方式极其低效。

Graphify 一次读取所有文件（包括非代码文件），构建语义知识图谱，之后 Agent 按结构导航而非逐文件搜索。

## 架构概述

```
多模态输入
├── 代码文件 (Python/TS/Java/R/SQL/Shell...)
├── 文档 (Markdown/PDF/论文)
├── 图片 (截图/白板/架构图) → Claude Vision 分析
└── 视频/音频 → 转录 + 概念提取
         ↓
    Graphify Engine
    ├── 解析 & 概念提取
    ├── 关系识别 (调用链/依赖/数据流)
    └── 图谱构建
         ↓
    持久化知识图谱 (可查询)
         ↓
    AI Coding Agent (通过 Skill 接口查询)
    └── 用自然语言查询架构关系
```

## 与 CodeGraph 的对比

| 维度 | Graphify | CodeGraph |
|------|----------|-----------|
| 输入类型 | 多模态（代码+文档+图片+视频） | 纯代码 |
| 解析方式 | Claude Vision + 语义分析 | Tree-sitter (AST) |
| 图谱类型 | 语义知识图谱 | 代码符号图谱 |
| 接口 | Skill (agentskills.io) | MCP Server (45 tools) |
| Token 减少 | 71.5x | 96% |
| 语言支持 | 通过 LLM 理解任意语言 | 37 种 (Tree-sitter) |

## 适用场景

- 包含大量非代码文件的复杂项目
- 需要理解架构图、白板图的项目
- 文档密集型项目（论文、规范文档）
- 需要跨代码/文档/基础设施建立全局理解

## 学习价值

- ⭐ 学习多模态知识图谱的构建方法
- 理解 Claude Vision 在代码工具链中的应用
- 对比 Graphify（语义图谱）vs CodeGraph（AST 图谱）的设计取舍
- 学习 Skill 分发模式（PyPI + /命令触发）

## 参考资料

- [Graphify: The Knowledge Graph That Ends Your Codebase's "Token Tax"](https://medium.com/@jinlow1/graphify-the-knowledge-graph-that-ends-your-codebases-token-tax-819b77f2ec58)
- [Bringing Knowledge Graphs to AI-Assisted Engineering (corti.com)](https://corti.com/graphify-bringing-knowledge-graphs-to-ai-assisted-engineering/)
- [How to Build an LLM Knowledge Base in Claude Code. 71x Fewer Tokens](https://www.roborhythms.com/how-to-build-llm-knowledge-base-claude-code-2026/)
- [Knowledge Graphs for Codebases (emelia.io)](https://emelia.io/hub/knowledge-graph-graphify-guide)
- [79× Token Reduction and Zero Vector Database Overhead](https://stevescargall.com/blog/2026/05/graphify--memmachine-79-token-reduction-zero-vector-database/)

---

*最后更新: 2026-05-26*
