# 参考资料

> 整理 ECC (Everything Claude Code) 相关的官方资源、文章和社区项目。

## 官方资源

| 资源 | 链接 |
|------|------|
| 源码仓库 | https://github.com/affaan-m/ECC |
| 仓库 (旧名) | https://github.com/affaan-m/everything-claude-code |
| npm: ecc-universal | https://www.npmjs.com/package/ecc-universal |
| npm: ecc-agentshield | https://www.npmjs.com/package/ecc-agentshield |
| Claude Code Marketplace | `ecc@ecc` (Plugin 安装) |
| Releases | https://github.com/affaan-m/ECC/releases |
| CHANGELOG | https://github.com/affaan-m/ECC/blob/main/CHANGELOG.md |
| LICENSE (MIT) | https://github.com/affaan-m/ECC/blob/main/LICENSE |

## 官方指南

| 指南 | 说明 | 链接 |
|------|------|------|
| Shortform Guide | 快速入门指南 | 仓库内 docs/ 目录 |
| Longform Guide | 完整使用指南 | 仓库内 docs/ 目录 |
| Security Guide | 安全使用指南 (AgentShield) | 仓库内 docs/ 目录 |
| AGENTS.md | 跨工具通用入口文件 | 仓库根目录 |
| install-plan.js | 选择性安装 — 分析阶段 | 仓库根目录 |
| install-apply.js | 选择性安装 — 执行阶段 | 仓库根目录 |

## 核心目录

| 目录 | 内容 | 数量 |
|------|------|------|
| `agents/` | 专业子 Agent 定义 | 61 |
| `skills/` | 技能工作流定义 | 246 |
| `commands/` | 斜杠命令 | 76 |
| `hooks/` / `scripts/hooks/` | Hook 脚本 | — |
| `rules/` | 规则 (通用 + 语言特定) | 34 |
| `mcp/` | MCP Server 配置 | 9 |
| `contexts/` | 动态上下文 (dev/review/research) | 3 |

## AgentShield 相关

| 资源 | 说明 |
|------|------|
| npm: ecc-agentshield | AgentShield 独立包 |
| Red-Team Pipeline | 攻击模拟 (Opus 4.6 驱动) |
| Blue-Team Pipeline | 防御验证 |
| Auditor Pipeline | 审计报告生成 |
| 测试套件 | 1282 自动化测试 |
| 静态规则 | 102 条安全规则 |

## 跨 Harness 支持参考

| Harness | 配置位置 | 适配说明 |
|---------|---------|---------|
| Claude Code | 原生 | Primary target |
| Cursor IDE | cursor/ 目录 | DRY Adapter (复用 CC hooks) |
| Codex | codex/ 目录 | macOS + CLI 适配 |
| OpenCode | opencode/ 目录 | Plugin 方式 |
| Gemini CLI | gemini/ 目录 | 配置映射 |
| Zed | zed/ 目录 | 配置映射 |
| GitHub Copilot | copilot/ 目录 | 配置映射 |
| Antigravity | antigravity/ 目录 | 配置映射 |
| JoyCode/CodeBuddy | joycode/ 目录 | 配置映射 |
| Qwen CLI | qwen/ 目录 | 配置映射 |

## MCP Server 参考

| MCP Server | 用途 | 文档 |
|------------|------|------|
| GitHub MCP | 仓库操作 | https://github.com/modelcontextprotocol |
| Supabase MCP | 数据库 | Supabase 官方 |
| Vercel MCP | 部署 | Vercel 官方 |
| Railway MCP | 基础设施 | Railway 官方 |
| Context7 MCP | 上下文管理 | Context7 官方 |
| Exa MCP | 搜索引擎 | Exa 官方 |
| Playwright MCP | 浏览器自动化 | Playwright 官方 |
| Sequential Thinking | 推理辅助 | — |
| Memory MCP | 记忆管理 | — |

## ECC 2.0 (Rust Control-Plane)

| 资源 | 说明 |
|------|------|
| Rust 控制平面源码 | 仓库内 ecc-2.0/ 或 control-plane/ 目录 |
| Dashboard GUI | 可视化仪表板 |
| Daemon 管理 | start/stop/resume/sessions/status |
| Operator Workflows | 操作员工作流编排 |

## 相关技术

| 技术 | 在 ECC 中的用途 |
|------|---------------|
| Claude Code | 主要目标 Harness |
| Anthropic API | Agent 推理引擎 |
| SQLite | State Store (直觉/学习/状态) |
| Node.js | Hook 脚本执行环境 |
| Rust | ECC 2.0 控制平面 |
| YAML | Agent/Skill/Command 定义格式 |
| Markdown | 文档 + 指令 + 配置 |

## Token 优化参考

| 配置项 | 推荐值 | 效果 |
|--------|--------|------|
| model | sonnet | 60% 成本降低 |
| MAX_THINKING_TOKENS | 10000 | 70% 隐藏思考成本降低 |
| CLAUDE_AUTOCOMPACT_PCT_OVERRIDE | 50 | 更早压缩上下文 |
| 活跃 MCP 数量 | ≤ 10 | 减少 token 消耗 |
| 活跃工具数量 | ≤ 80 | 减少 token 消耗 |

## 社区与生态

| 项目/人物 | 说明 |
|-----------|------|
| @affaan-m | 项目作者，Anthropic Hackathon 获奖者 |
| 170+ Contributors | 活跃贡献者社区 |
| 182K+ Stars | GitHub 星标数 |
| 28K+ Forks | GitHub Fork 数 |

## 相关 Harness 项目 (对比学习)

| 项目 | 仓库 | 关注点 |
|------|------|--------|
| OpenClaw | github.com/openclaw/openclaw | 自托管 AI Agent (对标学习) |
| mattpocock/skills | github.com/mattpocock/skills | Claude Code Skills 集合 |
| obra/superpowers | github.com/obra/superpowers | Claude Code 增强 |
| codegraph | github.com/colbymchenry/codegraph | 代码图谱 MCP |

---

*最后更新: 2026-07*
