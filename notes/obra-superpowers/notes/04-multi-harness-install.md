# 跨 Harness 安装学习笔记

> superpowers 的多平台安装方式 — 7+ Harness 的统一 Skills 体验。

## 概述

superpowers 支持 7+ 种 Agent Harness 的安装和使用。基于 agentskills.io 标准，确保同一套 Skills 在不同工具中一致工作。

### 支持矩阵

| Harness | 安装方式 | 支持级别 |
|---------|---------|---------|
| Claude Code | Plugin Marketplace | 完整 |
| Codex CLI | 配置文件 | 完整 |
| Codex App | 应用配置 | 完整 |
| Gemini CLI | 配置文件 | 完整 |
| OpenCode | Plugin | 完整 |
| Cursor | 扩展/配置 | 完整 |
| GitHub Copilot CLI | 配置 | 完整 |
| Factory Droid | 原生 | 完整 |

## Claude Code 安装

### 方式: Plugin Marketplace

```bash
# 在 Claude Code 中执行
/plugin install superpowers@claude-plugins-official
```

### 安装后结构

```
.claude/
└── plugins/
    └── superpowers/
        ├── brainstorming/SKILL.md
        ├── subagent-driven-development/SKILL.md
        ├── code-reviewer/SKILL.md
        ├── systematic-debugging/SKILL.md
        └── writing-skills/SKILL.md
```

## Codex 安装

### CLI 配置

```yaml
# codex.yaml
skills:
  - source: agent-superpowers
    version: latest
```

### 效果

Codex CLI 启动时自动加载 superpowers Skills，方法论流程自动激活。

## Gemini CLI 安装

### 配置方式

```json
{
  "skills": {
    "superpowers": {
      "source": "agentskills.io/obra/superpowers",
      "auto_activate": true
    }
  }
}
```

## OpenCode 安装

### Plugin 方式

```bash
opencode plugin add superpowers
```

## 统一性保障

### agentskills.io 标准

所有安装方式最终都是让 Agent 能够读取到相同的 SKILL.md 文件。标准保证:

```
任何 Harness + SKILL.md = 一致的行为

核心机制:
1. Agent 发现 SKILL.md 文件 (路径配置)
2. Agent 读取 SKILL.md 内容 (纯文本)
3. Agent 按照指令行动 (Markdown 指令)

= 不需要特殊集成层
= 只需要能读文件的 Agent 即可
```

### 跨 Harness 兼容性

| 能力 | 是否跨 Harness 一致 |
|------|-------------------|
| Brainstorming 流程 | ✅ |
| TDD 执行 | ✅ |
| Subagent (概念) | ⚠️ 依赖 Harness 能力 |
| 自动触发 | ⚠️ 依赖 Harness 能力 |
| Review 流程 | ✅ |

### Subagent 支持差异

| Harness | Subagent 支持 | 替代方式 |
|---------|-------------|---------|
| Claude Code | ✅ 原生 | — |
| Codex | ✅ 原生 | — |
| Cursor | ⚠️ 有限 | 串行执行 |
| Gemini CLI | ⚠️ 有限 | 串行执行 |
| OpenCode | ⚠️ 有限 | 串行执行 |

## PyPI 安装 (Python 项目)

```bash
pip install agent-superpowers
```

### Python 集成用途

| 用途 | 说明 |
|------|------|
| 编程接口 | 在 Python 代码中调用 Skills 定义 |
| 自动化 | CI/CD 中集成 superpowers 流程 |
| 自定义 | 基于 superpowers 框架编写自定义 Skills |

## 安装验证

### 验证步骤

```
1. 安装完成后，给 Agent 一个新需求
2. 观察 Agent 是否进入 Brainstorming 而非直接写代码
3. 确认六阶段流程是否被遵循
4. 如果 Agent 跳过了阶段 → 安装有问题
```

## 问题与思考

- [x] 安装的本质? → 让 Agent 能读到 SKILL.md 文件
- [x] 跨 Harness 的一致性? → 纯文本指令保证高一致性
- [ ] Subagent 在不支持的 Harness 中如何降级?
- [ ] 自动触发在不同 Harness 中的表现差异?
- [ ] 版本更新的同步机制?

---

*参考: [architecture.md](../architecture.md) | [03-skills-system.md](./03-skills-system.md)*
