# 多模态提取学习笔记

> graphify 的输入层 — 从代码(tree-sitter)、文档(Claude)、图片(Claude Vision)、PDF 中提取实体和关系。

## 概述

graphify 的核心差异化在于**多模态输入**。不仅处理代码（像 codegraph），还能处理文档、论文、图片甚至视频，构建统一的知识图谱。

### 输入类型全景

```
┌──────────────────────────────────────────────────────┐
│                    graphify 输入                       │
├──────────────────────────────────────────────────────┤
│                                                      │
│  代码文件 ─── tree-sitter AST 解析                   │
│  .py .ts .js .go .rs .java                          │
│                                                      │
│  文档文件 ─── Claude 语义提取                        │
│  .md .txt .rst                                      │
│                                                      │
│  PDF 论文 ─── 文本提取 + Claude 引用挖掘             │
│  .pdf                                               │
│                                                      │
│  图片文件 ─── Claude Vision 视觉识别                 │
│  .png .jpg                                          │
│                                                      │
└──────────────────────────────────────────────────────┘
```

## tree-sitter 代码解析

### 工作原理

```
源代码 → tree-sitter 解析 → AST → 查询 → 实体/关系

优势:
  - 极快 (增量解析)
  - 精确 (语法级别)
  - 多语言统一接口
  - 容错 (部分代码也可解析)
```

### 提取内容

| 语言 | 实体 | 关系 |
|------|------|------|
| Python | 函数、类、方法、模块 | 调用、继承、导入 |
| TypeScript | 接口、类型、函数、类 | 实现、导出、导入 |
| JavaScript | 函数、模块、组件 | 依赖、调用、导出 |
| Go | 结构体、方法、包 | 接口实现、包引用 |
| Rust | trait、impl、mod、fn | 实现、使用、所有权 |
| Java | 类、接口、注解 | 继承、实现、注入 |

### tree-sitter Query 示例

```scheme
;; Python: 提取函数定义
(function_definition
  name: (identifier) @function.name
  parameters: (parameters) @function.params
  return_type: (type) @function.return_type)

;; TypeScript: 提取接口
(interface_declaration
  name: (type_identifier) @interface.name
  body: (interface_body) @interface.body)
```

## Claude 文档语义提取

### 提取流程

```
文档文件 (.md/.txt/.rst)
    │
    ▼
Claude API 调用 (prompt: "提取实体和关系")
    │
    ▼
结构化输出:
  entities: [
    { name: "微服务", type: "concept" },
    { name: "API Gateway", type: "component" }
  ]
  relations: [
    { from: "API Gateway", to: "微服务", type: "routes_to" }
  ]
```

### 与 tree-sitter 的互补

| 维度 | tree-sitter | Claude 语义 |
|------|------------|------------|
| 精确度 | 极高 (语法级) | 中高 (语义级) |
| 速度 | 极快 | 较慢 (API) |
| 成本 | 免费 | Token 计费 |
| 能力 | 代码结构 | 概念/关系/含义 |
| 适用 | 代码文件 | 自然语言文档 |

## Claude Vision 图片识别

### 支持的图片类型

| 图片类型 | 提取内容 | 示例 |
|---------|---------|------|
| 架构图 | 组件、连接、数据流 | 系统设计图 |
| UML 图 | 类、接口、关系 | 类图/序列图 |
| 流程图 | 步骤、决策、分支 | 业务流程 |
| 白板照片 | 概念、箭头、分组 | 会议白板 |
| 图表 | 数据点、趋势、标签 | 性能图表 |

### Vision 提取流程

```
图片文件 (.png/.jpg)
    │
    ▼
Claude Vision API (prompt: "识别图中的实体和关系")
    │
    ▼
结构化输出:
  entities: [
    { name: "Web Server", type: "component", position: "left" },
    { name: "Database", type: "component", position: "right" }
  ]
  relations: [
    { from: "Web Server", to: "Database", type: "queries", confidence: 0.9 }
  ]
```

## PDF 引用挖掘

```
PDF 输入
    │
    ├── 1. 文本提取 (PyPDF2/pdfminer)
    │
    ├── 2. 元数据提取 (标题/作者/年份)
    │
    ├── 3. Claude 分析:
    │       - 关键概念提取
    │       - 引用关系识别
    │       - 方法论实体
    │
    └── 4. 输出: 论文节点 + 概念节点 + 引用边
```

## 与 codegraph 的对比

| 维度 | graphify | codegraph |
|------|---------|-----------|
| 输入类型 | 代码+文档+PDF+图片 | 仅代码 |
| 解析方式 | tree-sitter + Claude | tree-sitter |
| 图谱内容 | 代码+语义+概念 | 代码结构 |
| AI 依赖 | Claude API (必须) | 可选 |
| 成本 | Token 费用 | 免费 |
| 适用场景 | 混合项目/研究 | 纯代码项目 |

## 问题与思考

- [x] 为什么多模态? → 现实项目不止有代码
- [x] tree-sitter vs Claude? → 代码用前者，文档用后者
- [ ] Vision 的准确度在复杂图中如何?
- [ ] PDF 中公式的提取?
- [ ] 多语言混合项目的处理策略?

---

*参考: [architecture.md](../architecture.md) | [02-graph-construction.md](./02-graph-construction.md)*
