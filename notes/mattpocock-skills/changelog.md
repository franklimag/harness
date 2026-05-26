# mattpocock/skills 版本更新追踪

> 跟踪 mattpocock/skills 的版本发布、Skills 新增和生态系统变化。

## 项目概况

mattpocock/skills 作为轻量级 Skills 集合，更新主要体现在新 Skill 的添加和现有 Skill 的迭代优化。

## 发布模式

不同于传统的语义版本号发布，mattpocock/skills 采用**持续交付**模式：
- 新 Skill 直接添加到仓库
- 现有 Skill 直接更新文件
- 通过 `npx skills@latest` 始终获取最新版本

## Skills 演进时间线

### 初始核心集 — 工程基础

第一批 Skills 聚焦于解决最常见的 Agent 协作问题：

| Skill | 类别 | 解决的核心问题 |
|-------|------|---------------|
| grill-me | 对齐 | Agent 不确认就动手 |
| tdd | 质量 | 代码写了不能用 |
| diagnose | 质量 | 问题诊断无章法 |

### 扩展集 — 工作流完善

第二批 Skills 补充了工作流管理能力：

| Skill | 类别 | 关键特性 |
|-------|------|---------|
| to-prd | 工作流 | 需求 → PRD 文档化 |
| to-issues | 工作流 | PRD → Issue 拆分 |
| triage | 工作流 | Bug 分类和优先级 |
| handoff | 工作流 | 跨会话工作交接 |
| prototype | 工作流 | 快速原型验证 |

### 优化集 — 效率与架构

第三批 Skills 聚焦于长期效率和代码质量：

| Skill | 类别 | 关键特性 |
|-------|------|---------|
| caveman | 效率 | 75% Token 缩减 |
| improve-codebase-architecture | 架构 | 增量架构改善 |
| zoom-out | 架构 | 全局视角审视 |
| grill-with-docs | 对齐 | 文档验证理解 |

### 工具集 — 安全与扩展

最新的 Skills 补充了工具链和安全能力：

| Skill | 类别 | 关键特性 |
|-------|------|---------|
| git-guardrails-claude-code | 安全 | Git 操作护栏 |
| setup-pre-commit | 工具 | Pre-commit 配置 |
| migrate-to-shoehorn | 迁移 | 框架迁移辅助 |
| scaffold-exercises | 教育 | 练习题脚手架 |
| write-a-skill | 元技能 | 编写新 Skill |
| setup-matt-pocock-skills | 初始化 | 项目配置 |

## 生态系统里程碑

| 事件 | 说明 |
|------|------|
| skills.sh 发布 | 分发系统上线，npx skills@latest 可用 |
| 20K Stars | GitHub Stars 突破 20K |
| 60K Newsletter | 订阅者突破 60K |
| agentskills.io 兼容 | 与社区标准对接 |
| 多 Harness 支持 | 不限 Claude Code，支持任何 Agent |

## 设计哲学演进

```
早期: 单一 Skill 解决单一问题
  │
  ├── grill-me (对齐)
  ├── tdd (质量)
  └── diagnose (调试)
      │
      ▼
中期: 问题域驱动的 Skill 族
  │
  ├── 对齐族: grill-me + grill-with-docs + CONTEXT.md
  ├── 质量族: tdd + diagnose
  └── 工作流族: to-prd + to-issues + triage
      │
      ▼
当前: 完整工程循环覆盖
  │
  ├── 编码前: grill-me / grill-with-docs
  ├── 编码中: tdd / caveman
  ├── 问题时: diagnose / triage
  ├── 架构层: improve-codebase-architecture / zoom-out
  ├── 管理层: to-prd / to-issues / handoff
  └── 工具层: git-guardrails / setup-pre-commit
```

## 与 Total TypeScript 的协同

Matt Pocock 同时运营 Total TypeScript 教育平台：

| 维度 | Total TypeScript | mattpocock/skills |
|------|-----------------|-------------------|
| 目标 | 教人学 TypeScript | 教 Agent 做工程 |
| 形式 | 视频课程 + 练习 | Markdown 指令文件 |
| 受众 | 开发者 | Agent (+ 开发者) |
| 理念 | 渐进式学习 | 渐进式披露 |
| 共同点 | 工程经验驱动，实践导向 |

scaffold-exercises Skill 直接来自 Total TypeScript 的教学经验。

## 关注的未来方向

- [ ] 更多工程实践 Skills（性能优化、可观测性等）
- [ ] 团队协作相关 Skills（code review、pair programming）
- [ ] 特定语言/框架 Skills（TypeScript 专项等）
- [ ] skills.sh 分发系统增强
- [ ] 社区贡献 Skills 机制

## 如何追踪更新

1. **GitHub**: https://github.com/mattpocock/skills (Watch)
2. **Newsletter**: Matt Pocock Newsletter (60K 订阅者)
3. **CLI**: `npx skills@latest list` 查看最新可用 Skills
4. **Twitter/X**: @maaboroshi (Matt Pocock)

---

*最后更新: 2026-07*
