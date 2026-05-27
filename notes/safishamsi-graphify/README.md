# safishamsi/graphify 学习笔记工程

> 用于系统学习 [graphify](https://github.com/safishamsi/graphify) 多模态知识图谱构建与查询系统的知识库。

## Tracking Snapshot

| 字段 | 值 |
|------|-----|
| 追踪分支 | `main` |
| 最后同步 commit | _待填入_ |
| 同步日期 | _待填入_ |
| 对应版本 | _待填入_ |

## 项目简介

**graphify** 是由 Safi Shamsi 开发的**多模态知识图谱**工具。不同于只处理代码的 AST 图谱工具，graphify 能从任何文件类型（代码、文档、PDF、图片、视频）中提取实体和关系，构建统一的知识图谱。

### 核心数据

| 属性 | 值 |
|------|-----|
| GitHub Stars | 30K+ (22K in 10 days) |
| 作者 | Safi Shamsi |
| 语言 | Python |
| PyPI | graphifyy (双 y) |
| 发布 | April 2026 |
| 安装 | `pip install graphifyy && graphify install` |
| 触发 | `/graphify` 在 Claude Code 中 |
| 许可证 | MIT |
| 源码 | https://github.com/safishamsi/graphify |

### 核心技术栈

| 技术 | 用途 |
|------|------|
| NetworkX | 图数据结构 |
| Leiden (graspologic) | 社区检测算法 |
| tree-sitter | 代码解析 (AST) |
| Claude | 文档/图片语义提取 |
| vis.js | 交互式可视化 |

### 关键特性

```
┌─────────────────────────────────────────────────────────────┐
│  输入: 任何文件                                              │
│  代码 (.py .ts .js .go .rs .java ...) — tree-sitter 解析    │
│  文档 (.md .txt .rst) — Claude 语义提取                     │
│  论文 (.pdf) — 引用挖掘                                      │
│  图片 (.png .jpg) — Claude Vision 识别                      │
└───────────────────────────────┬─────────────────────────────┘
                                │ 提取实体和关系
                                ▼
┌─────────────────────────────────────────────────────────────┐
│  图谱构建: NetworkX + Leiden                                 │
│  节点: 实体 (函数/类/概念/人物/...)                           │
│  边: 关系 (调用/继承/引用/关联/...)                           │
│  边类型: EXTRACTED / INFERRED / AMBIGUOUS (透明度)           │
│  社区: Leiden 算法自动检测                                    │
│  SHA256: 增量更新缓存                                        │
└───────────────────────────────┬─────────────────────────────┘
                                │ 多格式输出
                                ▼
┌─────────────────────────────────────────────────────────────┐
│  输出: 多种格式                                              │
│  graph.html — 交互式可视化 (vis.js)                          │
│  obsidian/ — Obsidian Vault                                 │
│  wiki/ — Agent 可爬取的知识库                                │
│  GRAPH_REPORT.md — 分析报告 (god nodes, 意外连接)            │
│  graph.json — 持久化数据                                     │
└─────────────────────────────────────────────────────────────┘
```

### 核心差异化: 71.5x Token 缩减

在 52 文件混合语料库（代码+论文+图片）上实现 **71.5 倍** token 缩减。

## 学习目录

```
.
├── README.md                  # 本文件
├── architecture.md            # 架构总览 (输入→图谱→输出 流水线)
├── changelog.md               # 版本更新追踪
├── references.md              # 参考资料汇总
├── learning-guide.md          # 学习计划指南 (四阶段路线)
└── notes/
    ├── 01-multimodal-extraction.md  # 多模态提取 (tree-sitter, Claude, Vision)
    ├── 02-graph-construction.md     # 图谱构建 (NetworkX, Leiden, 边类型)
    ├── 03-output-formats.md         # 输出格式 (HTML, Obsidian, wiki, 导出)
    └── 04-incremental-updates.md    # 增量更新 (SHA256, --watch, git hook)
```

## 学习目标

- [x] 理解 graphify 的多模态定位
- [ ] 深入 tree-sitter 代码解析流水线
- [ ] 理解 Claude Vision 图片分析集成
- [ ] 学习 NetworkX + Leiden 社区检测
- [ ] 掌握边类型透明度设计 (EXTRACTED/INFERRED/AMBIGUOUS)
- [ ] 理解 SHA256 增量更新机制
- [ ] 对比 graphify (语义) vs codegraph (AST)

## 关键差异化

| 差异点 | 说明 |
|--------|------|
| 多模态 | 不仅代码，还有文档/PDF/图片/视频 |
| 无服务器 | 无 Neo4j，本地 NetworkX |
| 透明度 | 边类型区分确定/推断/模糊 |
| 71.5x 缩减 | 超高 token 压缩比 |
| 增量更新 | SHA256 缓存避免重复处理 |
| 多输出 | HTML/Obsidian/Wiki/Report/JSON |

## 相关链接

- 源码: https://github.com/safishamsi/graphify
- PyPI: `pip install graphifyy`
- 命令: `/graphify` (Claude Code 中触发)

---

*最后更新: 2026-07*
*项目信息见: [repos/safishamsi-graphify.md](../../repos/safishamsi-graphify.md)*
