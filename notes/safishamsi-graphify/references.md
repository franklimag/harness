# 参考资料

> 整理 graphify 相关的官方资源、技术文档和对比项目。

## 官方资源

| 资源 | 链接 |
|------|------|
| 源码仓库 | https://github.com/safishamsi/graphify |
| PyPI | https://pypi.org/project/graphifyy/ |
| 安装 | `pip install graphifyy && graphify install` |
| 触发命令 | `/graphify` (Claude Code) |

## 核心技术参考

| 技术 | 文档 | 在 graphify 中的用途 |
|------|------|-------------------|
| NetworkX | https://networkx.org/ | 图数据结构和算法 |
| Leiden (graspologic) | https://graspologic.readthedocs.io/ | 社区检测 |
| tree-sitter | https://tree-sitter.github.io/ | 代码 AST 解析 |
| Claude API | Anthropic 官方 | 语义提取 + Vision |
| vis.js | https://visjs.org/ | 交互式可视化 |

## 支持的文件类型

### 代码文件 (tree-sitter)

| 语言 | 扩展名 | tree-sitter grammar |
|------|--------|-------------------|
| Python | .py | tree-sitter-python |
| TypeScript | .ts | tree-sitter-typescript |
| JavaScript | .js | tree-sitter-javascript |
| Go | .go | tree-sitter-go |
| Rust | .rs | tree-sitter-rust |
| Java | .java | tree-sitter-java |

### 文档文件 (Claude)

| 格式 | 扩展名 | 提取方式 |
|------|--------|---------|
| Markdown | .md | Claude 语义分析 |
| 纯文本 | .txt | Claude 语义分析 |
| reStructuredText | .rst | Claude 语义分析 |
| PDF | .pdf | 文本提取 + Claude 引用挖掘 |

### 图片文件 (Claude Vision)

| 格式 | 扩展名 | 识别内容 |
|------|--------|---------|
| PNG | .png | 架构图/UML/流程图/白板 |
| JPEG | .jpg | 架构图/UML/流程图/白板 |

## 输出格式参考

| 输出 | 格式 | 用途 |
|------|------|------|
| graph.html | vis.js 交互页面 | 人类浏览 |
| obsidian/ | Markdown + [[links]] | Obsidian 知识库 |
| wiki/ | Markdown 结构化 | Agent 爬取 |
| GRAPH_REPORT.md | Markdown 报告 | 分析摘要 |
| graph.json | JSON 序列化 | 持久化/程序读取 |
| .svg | SVG 矢量图 | 文档嵌入 |
| .graphml | GraphML XML | 通用图格式 |
| Neo4j | Cypher 导出 | 图数据库 |

## 对比项目

| 项目 | 对比维度 |
|------|---------|
| codegraph (colbymchenry) | AST 图谱 vs 多模态语义图谱 |
| GraphRAG (Microsoft) | 大规模文本 vs 多模态混合 |
| Sourcegraph | 代码搜索 vs 知识图谱 |
| Obsidian Graph | 手动链接 vs 自动构建 |

## 核心概念术语表

| 术语 | 定义 |
|------|------|
| God Node | 连接数极多的核心枢纽节点 |
| Community | Leiden 算法检测出的节点分组 |
| EXTRACTED | 直接从源文件提取的确定关系 |
| INFERRED | 通过 AI 推理出的可能关系 |
| AMBIGUOUS | 不确定的模糊关系 |
| SHA256 Cache | 基于文件哈希的增量更新缓存 |

---

*最后更新: 2026-07*
