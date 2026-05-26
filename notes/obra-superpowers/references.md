# 参考资料

> 整理 obra/superpowers 相关的官方资源、标准规范和社区生态。

## 官方资源

| 资源 | 链接 |
|------|------|
| 源码仓库 | https://github.com/obra/superpowers |
| 官方文档 | https://obra-superpowers.mintlify.app/ |
| PyPI 包 | https://pypi.org/project/agent-superpowers/ |
| agentskills.io | https://agentskills.io |
| 作者 | Jesse Vincent (@obra) |
| Claude Code Plugin | `superpowers@claude-plugins-official` |

## 核心 Skills 参考

| Skill | 阶段 | 功能描述 |
|-------|------|---------|
| brainstorming | 1 | 引导式问题探索与方案评估 |
| subagent-driven-development | 4 | 子 Agent 任务分解与独立执行 |
| TDD (内置) | 4 | 测试驱动开发循环 |
| code-reviewer | 5 | 代码审查与质量把关 |
| systematic-debugging | 任意 | 系统化根因分析 |
| writing-skills | 元 | 编写新 Skill |

## 六阶段工作流参考

| 阶段 | 英文 | 核心动作 | 产出 |
|------|------|---------|------|
| 1 | Brainstorm | 探索问题与方案 | 方案选择 |
| 2 | Specify | 设计分块呈现 | 设计规格 |
| 3 | Plan | 制定实现计划 | 步骤列表 |
| 4 | Implement | Subagent 执行 | 代码产出 |
| 5 | Review | 审查产出质量 | 通过/返工 |
| 6 | Finish | 完成收尾 | 分支/PR |

## 安装参考

| Harness | 安装命令/方式 |
|---------|-------------|
| Claude Code | `/plugin install superpowers@claude-plugins-official` |
| Codex CLI | 配置文件映射 |
| Gemini CLI | 配置文件映射 |
| OpenCode | Plugin 安装 |
| Cursor | 扩展配置 |
| Copilot CLI | 配置添加 |
| Factory Droid | 原生集成 |

## 设计理念来源

| 理念 | 来源 | 在 superpowers 中的体现 |
|------|------|----------------------|
| TDD | Kent Beck | Implementation 阶段必须 |
| YAGNI | XP (极限编程) | Plan 阶段不过度设计 |
| DRY | Andy Hunt & Dave Thomas | Review 阶段检查 |
| Subagent 模式 | 微服务架构思想 | Implementation 分解 |
| 渐进式设计 | Martin Fowler | Specify 阶段分块 |

## 相关标准

| 标准 | 说明 | 链接 |
|------|------|------|
| agentskills.io | Agent Skills 社区标准 | https://agentskills.io |
| SKILL.md | 通用 Skill 定义格式 | agentskills.io 规范 |

## 对比项目参考

| 项目 | 对比维度 | superpowers 的立场 |
|------|---------|------------------|
| ECC | 大 vs 聚焦 | superpowers 聚焦方法论 |
| mattpocock/skills | 自由 vs 强制 | superpowers 强制流程 |
| hermes-agent | 静态 vs 自学习 | superpowers 是静态方法论 |
| claude-mem | 行为 vs 记忆 | superpowers 关注行为 |

## 核心概念术语表

| 术语 | 定义 |
|------|------|
| Methodology | 方法论，完整的开发流程体系 |
| Brainstorming | 头脑风暴，编码前的结构化探索 |
| Digestible Chunks | 可消化的块，设计规格的呈现方式 |
| Subagent | 子 Agent，独立执行特定任务的 Agent 实例 |
| Junior-Friendly | 新手友好，计划书应初级工程师可读 |
| Auto-trigger | 自动触发，基于上下文自动激活 Skill |

---

*最后更新: 2026-07*
