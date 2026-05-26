# 参考资料

> 整理 CodeGraph 相关的官方资源、生态项目、学术论文和同类工具。

## 官方资源

| 资源 | 链接 |
|------|------|
| 源码仓库 | https://github.com/colbymchenry/codegraph |
| npm 包 | https://www.npmjs.com/package/@colbymchenry/codegraph |
| Releases | https://github.com/colbymchenry/codegraph/releases |
| Issues | https://github.com/colbymchenry/codegraph/issues |
| 许可证 | MIT |
| 作者 | Colby McHenry |

## 核心依赖

| 技术 | 链接 | 用途 |
|------|------|------|
| Tree-sitter | https://tree-sitter.github.io/tree-sitter/ | 源代码解析器 |
| SQLite | https://www.sqlite.org/ | 本地数据库 |
| FTS5 | https://www.sqlite.org/fts5.html | 全文搜索扩展 |
| Model Context Protocol (MCP) | https://modelcontextprotocol.io/ | AI 工具协议 |
| Node.js | https://nodejs.org/ | 运行时 (自带) |

## 学术论文

| 标题 | 来源 | 链接 | 关联 |
|------|------|------|------|
| Codebase-Memory: Tree-Sitter-Based Knowledge Graphs via MCP | arxiv:2603.27277 | https://arxiv.org/abs/2603.27277 | 同概念学术研究 |

## 相关/同类工具

| 项目 | 作者 | 说明 | 链接 |
|------|------|------|------|
| tokensave | aovestdipaperino | CodeGraph 的 Rust 移植版 | https://github.com/aovestdipaperino/tokensave |
| CartographAI/mcp-server-codegraph | CartographAI | 类似概念的代码图谱 MCP Server | https://github.com/CartographAI/mcp-server-codegraph |
| Graphify | safishamsi | Tree-sitter 代码知识图谱 (内存模式) | https://github.com/safishamsi/graphify |
| Aider | Paul Gauthier | AI 编程助手 (使用 tree-sitter) | https://github.com/paul-gauthier/aider |

## Tree-sitter 相关资源

| 资源 | 链接 | 说明 |
|------|------|------|
| Tree-sitter 官方文档 | https://tree-sitter.github.io/tree-sitter/ | 解析器文档 |
| Tree-sitter Playground | https://tree-sitter.github.io/tree-sitter/playground | 在线 AST 可视化 |
| tree-sitter-typescript | https://github.com/tree-sitter/tree-sitter-typescript | TS 语法定义 |
| tree-sitter-python | https://github.com/tree-sitter/tree-sitter-python | Python 语法定义 |
| tree-sitter-go | https://github.com/tree-sitter/tree-sitter-go | Go 语法定义 |
| tree-sitter-rust | https://github.com/tree-sitter/tree-sitter-rust | Rust 语法定义 |
| tree-sitter-swift | https://github.com/alex-pinkus/tree-sitter-swift | Swift 语法定义 |

## MCP 生态资源

| 资源 | 链接 | 说明 |
|------|------|------|
| MCP 规范 | https://spec.modelcontextprotocol.io/ | 协议规范文档 |
| MCP SDK (TypeScript) | https://github.com/modelcontextprotocol/typescript-sdk | TS 开发 SDK |
| MCP Server 列表 | https://github.com/modelcontextprotocol/servers | 官方维护的 server 列表 |
| Claude MCP 文档 | https://docs.anthropic.com/en/docs/build-with-claude/mcp | Anthropic MCP 集成文档 |

## 框架路由相关文档

| 框架 | 文档链接 | CodeGraph 支持 |
|------|----------|----------------|
| Django | https://docs.djangoproject.com/en/5.0/topics/http/urls/ | ✅ urls.py |
| Flask | https://flask.palletsprojects.com/en/3.0.x/quickstart/#routing | ✅ @route |
| FastAPI | https://fastapi.tiangolo.com/tutorial/first-steps/ | ✅ @router |
| Express | https://expressjs.com/en/guide/routing.html | ✅ router.method() |
| NestJS | https://docs.nestjs.com/controllers | ✅ @Controller |
| Laravel | https://laravel.com/docs/11.x/routing | ✅ Route:: |
| Drupal | https://www.drupal.org/docs/drupal-apis/routing-system | ✅ *.routing.yml |
| Rails | https://guides.rubyonrails.org/routing.html | ✅ routes.rb |
| Spring | https://spring.io/guides/gs/rest-service | ✅ @RequestMapping |
| Gin | https://gin-gonic.com/docs/quickstart/ | ✅ r.GET() |
| Axum | https://docs.rs/axum/latest/axum/ | ✅ .route() |
| ASP.NET | https://learn.microsoft.com/aspnet/core/web-api/ | ✅ [HttpGet] |
| Vapor | https://docs.vapor.codes/basics/routing/ | ✅ app.get() |
| React Router | https://reactrouter.com/ | ✅ 文件路由 |

## MCP 客户端兼容性

| 客户端 | 说明 | 链接 |
|--------|------|------|
| Claude Desktop | Anthropic 官方客户端 | https://claude.ai/download |
| Cursor | AI 代码编辑器 | https://cursor.sh/ |
| Windsurf | Codeium AI 编辑器 | https://codeium.com/windsurf |
| VS Code + Copilot | GitHub Copilot MCP 支持 | https://code.visualstudio.com/ |
| Kiro | AWS AI IDE | — |

## Benchmark 方法论参考

| 资源 | 链接 | 说明 |
|------|------|------|
| SWE-bench | https://www.swebench.com/ | 软件工程 benchmark |
| HumanEval | https://github.com/openai/human-eval | 代码生成评估 |
| MBPP | https://github.com/google-research/google-research/tree/master/mbpp | Python 编程 benchmark |

## 推荐阅读

| 主题 | 资源 | 说明 |
|------|------|------|
| 知识图谱基础 | Wikipedia: Knowledge Graph | 基本概念 |
| AST 与代码分析 | Tree-sitter 官方教程 | 解析器入门 |
| MCP 协议设计 | MCP 规范文档 | 理解 tool/resource/prompt |
| SQLite 性能优化 | SQLite WAL 文档 | 理解 WAL 模式 |
| 代码搜索 | Sourcegraph 博客 | 代码搜索的技术挑战 |

---

*最后更新: 2026-05-24*
