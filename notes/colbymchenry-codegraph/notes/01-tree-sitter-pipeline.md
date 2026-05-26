# Tree-sitter 解析流水线

> CodeGraph 的数据入口 — 源代码如何变成知识图谱中的节点和边。

## 一、概述

Tree-sitter 是 CodeGraph 的核心解析引擎。它将源代码解析为 AST（抽象语法树），然后通过语言特定查询从 AST 中提取有意义的代码实体（节点）和关系（边）。

### 流水线总览

```
源代码文件
    │
    ▼
┌──────────────────────────────────────┐
│  1. Language Detection (语言检测)      │
│     根据文件扩展名选择 Parser          │
└───────────────────┬──────────────────┘
                    │
                    ▼
┌──────────────────────────────────────┐
│  2. Tree-sitter Parse (AST 生成)      │
│     源代码 → 具体语法树 (CST)         │
└───────────────────┬──────────────────┘
                    │
                    ▼
┌──────────────────────────────────────┐
│  3. Query Execution (查询执行)        │
│     S-expression 查询 → 匹配捕获     │
└───────────────────┬──────────────────┘
                    │
                    ▼
┌──────────────────────────────────────┐
│  4. Entity Extraction (实体提取)      │
│     捕获结果 → Nodes + Edges          │
└──────────────────────────────────────┘
```

## 二、Tree-sitter 基础

### 2.1 什么是 Tree-sitter？

Tree-sitter 是一个增量解析器生成框架，具有以下特点：

| 特性 | 说明 |
|------|------|
| 增量解析 | 文件修改时只重新解析变化部分 |
| 错误恢复 | 代码有语法错误也能生成部分 AST |
| 多语言 | 每种语言有独立的 grammar 定义 |
| 高性能 | C 编写的运行时，毫秒级解析 |
| 查询支持 | S-expression 模式匹配 |

### 2.2 AST 示例

给定 TypeScript 代码：

```typescript
function handleRequest(req: Request): Response {
  const data = parseBody(req);
  return formatResponse(data);
}
```

Tree-sitter 生成的 AST 结构（简化）：

```
(program
  (function_declaration
    name: (identifier) "handleRequest"
    parameters: (formal_parameters
      (required_parameter
        name: (identifier) "req"
        type: (type_annotation (type_identifier) "Request")))
    return_type: (type_annotation (type_identifier) "Response")
    body: (statement_block
      (lexical_declaration
        (variable_declarator
          name: (identifier) "data"
          value: (call_expression
            function: (identifier) "parseBody"
            arguments: (arguments (identifier) "req"))))
      (return_statement
        (call_expression
          function: (identifier) "formatResponse"
          arguments: (arguments (identifier) "data"))))))
```

## 三、语言查询 (S-expression Queries)

### 3.1 查询语法基础

Tree-sitter 查询使用 S-expression 模式匹配语法：

```scheme
; 匹配函数声明并捕获名称
(function_declaration
  name: (identifier) @function.name) @function.def
```

#### 核心语法元素

| 语法 | 含义 | 示例 |
|------|------|------|
| `(node_type)` | 匹配特定节点类型 | `(function_declaration)` |
| `@capture` | 捕获匹配的节点 | `@function.name` |
| `field:` | 匹配命名字段 | `name: (identifier)` |
| `"literal"` | 匹配文本内容 | `"async"` |
| `_` | 匹配任意节点 | `(_)` |
| `?` | 可选匹配 | `(type_annotation)?` |
| `*` | 零次或多次 | `(parameter)*` |
| `+` | 一次或多次 | `(statement)+` |

### 3.2 节点提取查询示例

#### 提取函数声明

```scheme
; TypeScript 函数声明
(function_declaration
  name: (identifier) @node.name
  parameters: (formal_parameters) @node.params
  return_type: (type_annotation)? @node.return_type
  body: (statement_block) @node.body) @node.def

; Python 函数定义
(function_definition
  name: (identifier) @node.name
  parameters: (parameters) @node.params
  body: (block) @node.body) @node.def
```

#### 提取类声明

```scheme
; TypeScript 类
(class_declaration
  name: (type_identifier) @node.name
  (class_heritage
    (extends_clause
      value: (identifier) @node.extends))?
  (class_heritage
    (implements_clause
      (type_identifier) @node.implements))?
  body: (class_body) @node.body) @node.def
```

#### 提取方法

```scheme
; 类中的方法定义
(method_definition
  name: (property_identifier) @node.name
  parameters: (formal_parameters) @node.params
  body: (statement_block) @node.body) @node.def
```

### 3.3 边提取查询示例

#### 提取函数调用

```scheme
; 函数调用
(call_expression
  function: (identifier) @edge.callee) @edge.call

; 方法调用
(call_expression
  function: (member_expression
    object: (_) @edge.object
    property: (property_identifier) @edge.method)) @edge.call
```

#### 提取导入关系

```scheme
; ES 模块导入
(import_statement
  (import_clause
    (named_imports
      (import_specifier
        name: (identifier) @edge.imported)))
  source: (string) @edge.source) @edge.import

; Python 导入
(import_from_statement
  module_name: (dotted_name) @edge.source
  name: (dotted_name) @edge.imported) @edge.import
```

#### 提取继承关系

```scheme
; extends 关系
(class_declaration
  name: (type_identifier) @edge.child
  (class_heritage
    (extends_clause
      value: (identifier) @edge.parent))) @edge.extends
```

## 四、语言特定处理

### 4.1 不同语言的差异

| 语言 | 函数节点类型 | 类节点类型 | 导入语法 |
|------|-------------|-----------|----------|
| TypeScript | `function_declaration`, `arrow_function` | `class_declaration` | `import_statement` |
| Python | `function_definition` | `class_definition` | `import_from_statement` |
| Go | `function_declaration`, `method_declaration` | `type_declaration` (struct) | `import_declaration` |
| Rust | `function_item` | `struct_item`, `impl_item` | `use_declaration` |
| Java | `method_declaration` | `class_declaration` | `import_declaration` |
| Swift | `function_declaration` | `class_declaration` | `import_declaration` |

### 4.2 TypeScript/JavaScript 特殊处理

TypeScript/JavaScript 有特别丰富的函数形式：

```typescript
// 以下全部是函数，都需要提取
function foo() {}                    // function_declaration
const bar = () => {}                 // arrow_function
const baz = function() {}           // function_expression
class X { method() {} }             // method_definition
export default function() {}        // function_declaration (unnamed)
```

### 4.3 Python 特殊处理

Python 的装饰器包含重要元数据：

```python
@app.route('/users')        # 装饰器 → 框架路由边
def get_users():            # 函数定义 → 节点
    return User.query.all() # 方法调用 → 边
```

## 五、提取结果

### 5.1 节点 (Nodes)

提取完成后，每个节点包含：

| 字段 | 来源 | 示例 |
|------|------|------|
| name | `@node.name` 捕获 | `"handleRequest"` |
| kind | 节点类型推断 | `"function"` |
| file | 当前解析文件路径 | `"src/server.ts"` |
| line | AST 节点位置 | `42` |
| language | 文件扩展名 | `"typescript"` |
| signature | 参数+返回类型 | `"(req: Request): Response"` |
| body | `@node.body` 捕获 | 完整函数体 |

### 5.2 边 (Edges)

| 字段 | 来源 | 示例 |
|------|------|------|
| source | 当前上下文节点 ID | 调用者函数 |
| target | 待解析 (Resolution 阶段) | 被调用函数名 |
| kind | 查询类型标记 | `"calls"` / `"imports"` |
| file | 边所在文件 | `"src/server.ts"` |
| line | 调用/导入位置 | `44` |

> 注意: 边的 `target` 在提取阶段可能只是一个名称字符串，需要 Resolution 阶段将其解析为精确的节点 ID。

## 六、增量解析

### 6.1 Tree-sitter 增量解析能力

当文件被修改时，Tree-sitter 可以只重新解析变化的部分：

```
原始 AST (完整文件)
    │
    ▼ 文件修改 (edit event)
    │
局部重解析 (只解析变化区域)
    │
    ▼
新 AST (复用未变化的子树)
```

### 6.2 CodeGraph 的增量策略

CodeGraph 在 Auto-Sync 中利用此能力：

1. 文件变更被检测到
2. 删除该文件之前的所有节点和边
3. 用 Tree-sitter 重新解析修改后的文件
4. 提取新的节点和边
5. Resolution 重新运行受影响的边

## 七、性能考量

| 因素 | 影响 | CodeGraph 的处理 |
|------|------|------------------|
| 文件数量 | 首次索引时间 | 并行解析多文件 |
| 文件大小 | 单文件解析时间 | Tree-sitter 线性复杂度 |
| AST 深度 | 查询匹配时间 | 查询优化 (限制深度) |
| 语言数量 | 内存占用 (多个 parser) | 按需加载 parser |
| 修改频率 | 增量更新压力 | 2s 防抖合并 |

## 八、待深入研究

- [ ] CodeGraph 具体使用哪些 tree-sitter 查询文件？
- [ ] 如何处理动态语言中无法静态确定的调用？
- [ ] 查询匹配的性能上限在哪里？
- [ ] 错误恢复时生成的部分 AST 如何影响提取质量？
- [ ] 对于超大文件 (>10000 行) 有没有特殊处理？

---

*前置阅读: [architecture.md](../architecture.md)*
*后续阅读: [02-knowledge-graph.md](./02-knowledge-graph.md)*
*最后更新: 2026-05-24*
