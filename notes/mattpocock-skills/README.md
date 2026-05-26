# mattpocock/skills 学习笔记工程

> 用于系统学习 [mattpocock/skills](https://github.com/mattpocock/skills) 架构、设计哲学与实践方法的知识库。

## 项目简介

**mattpocock/skills** 是由 Matt Pocock（Total TypeScript 创建者）开发的**可组合 Agent Skills 集合**。不同于 ECC 的"大而全"策略，skills 追求"小而精、易适配、可组合"的设计哲学，强调基于数十年工程经验的实用型技能。

### 核心数据

| 属性 | 值 |
|------|-----|
| GitHub Stars | 20K+ |
| Newsletter 订阅 | 60K+ |
| 作者 | Matt Pocock（Total TypeScript 创建者）|
| 安装方式 | `npx skills@latest add mattpocock/skills` |
| 分发系统 | skills.sh (npx skills@latest) |
| 格式 | 纯 SKILL.md 文件（无 hooks/runtime）|
| 哲学 | Small, easy to adapt, composable. Work with any model. |
| 许可证 | MIT |
| 源码 | https://github.com/mattpocock/skills |

### 核心设计哲学

> "Small, easy to adapt, composable. Work with any model. Based on decades of engineering experience."

- **小**: 每个 Skill 是独立的 Markdown 文件，无运行时依赖
- **易适配**: 纯文本格式，可直接编辑定制
- **可组合**: Skills 之间可自由组合使用
- **模型无关**: 适用于任何 LLM 模型
- **经验驱动**: 基于数十年真实工程经验

### 解决的四大核心问题

| 问题 | 表现 | 解决方案 |
|------|------|---------|
| Agent 不按我想的做 | 编码前不确认，直接动手 | grill-me / grill-with-docs（编码前对齐） |
| Agent 太啰嗦 | 输出冗长无效 | CONTEXT.md 共享语言 + 领域术语 |
| 代码不能用 | 写了但跑不通 | tdd (red-green-refactor) + diagnose |
| 代码变成泥球 | 架构劣化失控 | improve-codebase-architecture + zoom-out + to-prd |

### 架构模式

```
┌─────────────────────────────────────────────────┐
│              skills.sh 分发层                     │
│         npx skills@latest add/remove/list        │
└───────────────────────────┬─────────────────────┘
                            │ 下载 / 安装
                            ▼
┌─────────────────────────────────────────────────┐
│            SKILL.md 文件 (纯 Markdown)            │
│    无 hooks / 无 runtime / 无 YAML frontmatter    │
│    渐进式披露 (Progressive Disclosure)            │
└───────────────────────────┬─────────────────────┘
                            │ 被 Agent 读取
                            ▼
┌─────────────────────────────────────────────────┐
│           任意 Agent Harness                      │
│  Claude Code / Cursor / Codex / 任何支持 Skills  │
└─────────────────────────────────────────────────┘
```

## Skills 完整列表

### 工程类 (Engineering)

| Skill | 功能 | 解决的核心问题 |
|-------|------|---------------|
| grill-with-docs | 基于文档进行需求对齐 | Agent 不按我想的做 |
| tdd | 测试驱动开发 (Red-Green-Refactor) | 代码不能用 |
| diagnose | 系统化问题诊断 | 代码不能用 |
| triage | Bug 分类和优先级判定 | 工作流管理 |
| to-issues | 需求转化为 Issue 列表 | 工作流管理 |
| to-prd | 需求转化为产品需求文档 | 架构劣化 |
| improve-codebase-architecture | 代码库架构改善 | 代码变成泥球 |
| zoom-out | 跳出细节看全局 | 架构劣化 |
| prototype | 快速原型制作 | 探索验证 |
| setup-matt-pocock-skills | 配置 issue 追踪器、标签、文档位置 | 初始化 |

### 生产力类 (Productivity)

| Skill | 功能 | 解决的核心问题 |
|-------|------|---------------|
| caveman | 75% Token 缩减的极简通信 | 成本控制 |
| grill-me | 编码前的需求确认对话 | Agent 不按我想的做 |
| handoff | 工作交接（跨会话/跨人）| 协作效率 |
| write-a-skill | 编写新 Skill 的元技能 | 扩展系统 |

### 杂项类 (Misc)

| Skill | 功能 |
|-------|------|
| git-guardrails-claude-code | Git 操作安全护栏 |
| migrate-to-shoehorn | 迁移至 Shoehorn 框架 |
| scaffold-exercises | 脚手架练习题生成 |
| setup-pre-commit | 配置 pre-commit hooks |

## 学习目录

```
.
├── README.md                  # 本文件
├── architecture.md            # 架构总览 (纯 SKILL.md + skills.sh 分发 + 设计哲学)
├── changelog.md               # 版本更新追踪
├── references.md              # 参考资料汇总
├── learning-guide.md          # 学习计划指南 (四阶段路线)
└── notes/
    ├── 01-skill-format.md     # SKILL.md 格式与渐进式披露模式
    ├── 02-alignment-skills.md # 对齐技能 (grill-me, grill-with-docs, CONTEXT.md)
    ├── 03-quality-skills.md   # 质量技能 (tdd, diagnose, improve-codebase-architecture)
    └── 04-workflow-skills.md  # 工作流技能 (to-prd, to-issues, triage, handoff, prototype)
```

## 学习路线

1. **阶段一** — 理解设计哲学（小/可组合/模型无关/经验驱动）
2. **阶段二** — 掌握 SKILL.md 格式和渐进式披露模式
3. **阶段三** — 学习核心对齐和质量技能
4. **阶段四** — 理解工作流技能和系统扩展方式

详见 [learning-guide.md](./learning-guide.md)

## 学习目标

- [x] 理解 Skills 的设计原则（小而可组合）
- [ ] 掌握 SKILL.md 格式和渐进式披露
- [ ] 学习 npx skills CLI 分发机制
- [ ] 理解 grill-me / grill-with-docs 对齐模式
- [ ] 掌握 tdd + diagnose 质量保障工作流
- [ ] 学习 caveman (75% Token 缩减) 实现
- [ ] 对比 ECC（大而全）vs Skills（小而精）

## 关键差异化

与其他 Harness 项目相比，mattpocock/skills 的独特之处：

| 差异点 | 说明 |
|--------|------|
| 纯 Markdown | 无 hooks/runtime/YAML，极简设计 |
| 渐进式披露 | 信息按需展开，不一次性灌输 |
| 模型无关 | 不绑定任何特定 LLM |
| 经验驱动 | 20 年+工程经验的实践总结 |
| 75% Token 缩减 | caveman Skill 的极限优化 |
| 元技能 | write-a-skill 支持自定义扩展 |

## 相关链接

- 源码仓库: https://github.com/mattpocock/skills
- 分发系统: skills.sh (npx skills@latest)
- 作者: Matt Pocock (Total TypeScript)
- Setup 命令: `/setup-matt-pocock-skills`

---

*最后更新: 2026-07*
*项目信息见: [repos/mattpocock-skills.md](../../repos/mattpocock-skills.md)*
