# 参考资料

> 整理 mattpocock/skills 相关的官方资源、设计理念来源和社区生态。

## 官方资源

| 资源 | 链接 |
|------|------|
| 源码仓库 | https://github.com/mattpocock/skills |
| 分发 CLI | npx skills@latest (skills.sh) |
| 作者 | Matt Pocock (@maaboroshi) |
| Total TypeScript | https://totaltypescript.com |
| Newsletter | 60K+ 订阅者 |

## 安装与使用

| 命令 | 说明 |
|------|------|
| `npx skills@latest add mattpocock/skills` | 安装全部 Skills |
| `npx skills@latest add mattpocock/skills/tdd` | 安装单个 Skill |
| `npx skills@latest list` | 列出已安装 |
| `npx skills@latest remove <name>` | 移除 Skill |
| `/setup-matt-pocock-skills` | 项目初始化配置 |

## Skills 完整清单

### 工程类

| Skill | 文件 | 功能 |
|-------|------|------|
| grill-with-docs | SKILL.md | 基于文档验证需求理解 |
| tdd | SKILL.md | 测试驱动开发流程 |
| diagnose | SKILL.md | 系统化问题诊断 |
| triage | SKILL.md | Bug 分类与优先级 |
| to-issues | SKILL.md | 需求转 Issue 列表 |
| to-prd | SKILL.md | 需求转产品需求文档 |
| improve-codebase-architecture | SKILL.md | 代码库架构改善 |
| zoom-out | SKILL.md | 跳出细节看全局 |
| prototype | SKILL.md | 快速原型制作 |
| setup-matt-pocock-skills | SKILL.md | 初始化项目配置 |

### 生产力类

| Skill | 文件 | 功能 |
|-------|------|------|
| caveman | SKILL.md | 75% Token 缩减通信 |
| grill-me | SKILL.md | 编码前需求确认 |
| handoff | SKILL.md | 工作交接 |
| write-a-skill | SKILL.md | 编写新 Skill |

### 杂项类

| Skill | 文件 | 功能 |
|-------|------|------|
| git-guardrails-claude-code | SKILL.md | Git 操作安全护栏 |
| migrate-to-shoehorn | SKILL.md | Shoehorn 框架迁移 |
| scaffold-exercises | SKILL.md | 练习题脚手架 |
| setup-pre-commit | SKILL.md | Pre-commit 配置 |

## 设计理念来源

| 理念 | 来源 | 在 Skills 中的体现 |
|------|------|------------------|
| TDD | Kent Beck《Test-Driven Development》| tdd Skill |
| 渐进式披露 | 认知负载理论 (John Sweller) | SKILL.md 格式设计 |
| 需求对齐 | 需求工程 (Requirements Engineering) | grill-me / grill-with-docs |
| 架构改善 | Martin Fowler《Refactoring》| improve-codebase-architecture |
| YAGNI | 极限编程 (XP) | 整体"小而精"哲学 |
| 领域驱动设计 | Eric Evans《DDD》| CONTEXT.md 共享语言 |
| 可组合性 | Unix 哲学 | 每个 Skill 做好一件事 |

## 相关标准与生态

| 标准/工具 | 关系 |
|-----------|------|
| agentskills.io | Skills 社区标准，兼容格式 |
| skills.sh | Skills 分发系统 |
| Claude Code Skills | Anthropic 官方 Skills 系统 |
| SKILL.md | 社区通用的 Skill 定义格式 |

## 对比参考项目

| 项目 | 对比维度 |
|------|---------|
| ECC (affaan-m/ECC) | 大而全 vs 小而精 |
| superpowers (obra/superpowers) | 强制工作流 vs 自由组合 |
| claude-mem (thedotmack/claude-mem) | 记忆系统 vs 行为指令 |
| hermes-agent (NousResearch) | 自进化 Agent vs 静态 Skills |
| codegraph (colbymchenry) | 代码分析 vs 行为指导 |

## 核心概念术语表

| 术语 | 定义 |
|------|------|
| SKILL.md | Skill 定义文件，纯 Markdown 格式 |
| Progressive Disclosure | 渐进式披露，信息按需展开 |
| CONTEXT.md | 项目共享语言定义文件 |
| Alignment | 对齐，确保 Agent 理解与用户一致 |
| caveman | 极简通信模式，最大化减少 Token |
| grill | 质询/确认，编码前的需求验证 |
| handoff | 工作交接，跨会话传递上下文 |
| skills.sh | Skills 分发系统 CLI |

## 学习推荐顺序

1. **入门**: README → 理解设计哲学
2. **格式**: 阅读任意 SKILL.md → 理解文件结构
3. **实践**: 安装 grill-me + tdd → 实际使用
4. **深入**: caveman → 理解 Token 优化
5. **扩展**: write-a-skill → 自定义 Skill
6. **对比**: 与 ECC/superpowers 对比 → 理解设计取舍

---

*最后更新: 2026-07*
