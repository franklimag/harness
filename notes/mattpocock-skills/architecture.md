# mattpocock/skills 架构总览

> 基于 GitHub 仓库 (mattpocock/skills) 和公开资料整理。最后更新: 2026-07

## 零、项目基本信息

| 属性 | 值 |
|------|-----|
| 仓库 | https://github.com/mattpocock/skills |
| 作者 | Matt Pocock (Total TypeScript 创建者) |
| Stars | 20K+ |
| Newsletter | 60K 订阅者 |
| 安装 | `npx skills@latest add mattpocock/skills` |
| 格式 | 纯 SKILL.md 文件 |
| 运行时 | 无（纯 Markdown 指令） |
| 分发 | skills.sh CLI |
| 许可证 | MIT |

## 一、整体架构

### 核心定位

```
mattpocock/skills = 可组合的 Agent 行为指令集
                  = 纯 Markdown 文件，无运行时
                  = "给 Agent 的工程经验手册"
```

### 与其他系统的本质区别

```
┌───────────────────────────────────────────────────────┐
│  传统方式: 复杂系统                                     │
│                                                       │
│  配置(YAML) + 运行时(Node.js) + Hooks + 数据库 + API  │
│  = ECC, claude-mem, superpowers 等                    │
└───────────────────────────────────────────────────────┘

┌───────────────────────────────────────────────────────┐
│  mattpocock/skills: 极简方式                           │
│                                                       │
│  纯 SKILL.md 文件 → Agent 读取 → 按指令行动            │
│  无 hooks / 无 runtime / 无数据库 / 无 API             │
└───────────────────────────────────────────────────────┘
```

### 架构层次图

```
┌─────────────────────────────────────────────────────────┐
│                 分发层 (Distribution)                     │
│                                                         │
│  npx skills@latest add mattpocock/skills                │
│  npx skills@latest list                                 │
│  npx skills@latest remove <skill-name>                  │
│                                                         │
│  底层: skills.sh CLI 工具                                │
└─────────────────────────────┬───────────────────────────┘
                              │ 下载到本地
                              ▼
┌─────────────────────────────────────────────────────────┐
│                 文件层 (Files)                            │
│                                                         │
│  .skills/                                               │
│  ├── grill-me/SKILL.md                                  │
│  ├── tdd/SKILL.md                                       │
│  ├── diagnose/SKILL.md                                  │
│  ├── caveman/SKILL.md                                   │
│  └── ...                                                │
│                                                         │
│  特点: 纯 Markdown, 无 frontmatter, 渐进式披露           │
└─────────────────────────────┬───────────────────────────┘
                              │ Agent 读取
                              ▼
┌─────────────────────────────────────────────────────────┐
│                 执行层 (Execution)                        │
│                                                         │
│  任意 Agent (Claude Code / Cursor / Codex / ...)        │
│  读取 SKILL.md → 理解指令 → 按步骤执行                   │
│  无需特殊集成，只要能读文件即可                            │
└─────────────────────────────────────────────────────────┘
```

## 二、SKILL.md 格式设计

### 设计原则

1. **无 YAML Frontmatter** — 不像 ECC 使用元数据头，纯 Markdown
2. **渐进式披露** — 信息按需展开，避免一次性信息过载
3. **自包含** — 每个 Skill 文件完整描述一个行为模式
4. **人可读** — 开发者可以直接阅读理解，不仅是 Agent 使用

### 渐进式披露 (Progressive Disclosure) 模式

```
Level 1: 标题 + 一句话描述 (Agent 快速判断是否适用)
    │
    ▼
Level 2: 触发条件 + 基本步骤 (Agent 决定如何开始)
    │
    ▼
Level 3: 详细指导 + 边界情况 (执行中遇到问题时参考)
    │
    ▼
Level 4: 示例 + 反模式 (深度理解时使用)
```

### 格式示例

```markdown
# TDD (Test-Driven Development)

> 编写代码前先写测试。Red-Green-Refactor 循环。

## 何时使用
- 实现新功能
- 修复 Bug
- 重构已有代码

## 流程

### 1. Red — 编写失败测试
描述预期行为，运行确认失败。

### 2. Green — 最小实现
写最少的代码让测试通过。不做多余设计。

### 3. Refactor — 优化重构
测试保护下安全重构。消除重复，改善命名。

## 关键原则
- 每次只测一件事
- 测试名描述行为，不描述实现
- 先写断言，再写 setup
...
```

## 三、skills.sh 分发系统

### CLI 命令

| 命令 | 功能 |
|------|------|
| `npx skills@latest add mattpocock/skills` | 添加整个 Skills 集合 |
| `npx skills@latest add mattpocock/skills/tdd` | 添加单个 Skill |
| `npx skills@latest list` | 列出已安装的 Skills |
| `npx skills@latest remove <name>` | 移除 Skill |

### 安装流程

```
npx skills@latest add mattpocock/skills
    │
    ├── 1. 从 GitHub 获取 SKILL.md 文件列表
    │
    ├── 2. 下载到本地 .skills/ 目录
    │
    ├── 3. 可选: 配置 Agent 自动发现路径
    │
    └── 4. 完成 (无需重启/编译/构建)
```

### 本地目录结构

```
项目根目录/
├── .skills/
│   ├── mattpocock/
│   │   ├── grill-me/SKILL.md
│   │   ├── grill-with-docs/SKILL.md
│   │   ├── tdd/SKILL.md
│   │   ├── diagnose/SKILL.md
│   │   ├── caveman/SKILL.md
│   │   ├── to-prd/SKILL.md
│   │   ├── to-issues/SKILL.md
│   │   ├── triage/SKILL.md
│   │   ├── handoff/SKILL.md
│   │   └── ...
│   └── 其他作者/
│       └── ...
├── CONTEXT.md    ← 共享语言/领域术语文件
└── 项目代码...
```

## 四、Setup 流程

运行 `/setup-matt-pocock-skills` 命令会配置:

| 配置项 | 说明 |
|--------|------|
| Issue Tracker | 配置 issue 追踪器位置 |
| Labels | 设置标签体系 (bug/feature/improvement 等) |
| Docs Location | 文档存放路径配置 |
| CONTEXT.md | 创建共享语言文件模板 |

### CONTEXT.md 共享语言

```markdown
# CONTEXT.md — 项目共享语言

## 领域术语
- "用户" = 使用产品的最终用户
- "客户" = 付费企业客户
- "工单" = 客户提交的支持请求

## 架构术语
- "网关" = API Gateway 层
- "核心" = 业务逻辑层
- "存储" = 数据持久化层

## 团队约定
- PR 必须经过 1 人 review
- 测试覆盖率 > 80%
- 命名使用英文
```

## 五、设计哲学深度解读

### 哲学 1: 小 (Small)

```
ECC: 246 Skills + 61 Agents + 76 Commands + Hooks + Rules + Instincts
     = 庞大系统，学习曲线陡峭

mattpocock/skills: ~15 Skills
     = 每个都可以独立理解和使用
     = 5 分钟上手一个 Skill
```

### 哲学 2: 易适配 (Easy to Adapt)

```
传统: 修改需要理解 YAML schema / hook 事件 / 运行时配置
      = 入门成本高

skills: 打开 SKILL.md → 直接修改 Markdown → 完成
      = 任何人都会改
```

### 哲学 3: 可组合 (Composable)

```
┌──────────┐  ┌──────────┐  ┌──────────┐
│ grill-me │  │   tdd    │  │ diagnose │
└────┬─────┘  └────┬─────┘  └────┬─────┘
     │              │              │
     └──────────────┼──────────────┘
                    │
            自由组合使用
         无依赖关系约束
```

### 哲学 4: 模型无关 (Work with Any Model)

Skills 是纯文本指令，不调用特定 API，不依赖特定模型能力：

| 模型 | 是否可用 |
|------|---------|
| Claude (Sonnet/Opus/Haiku) | ✅ |
| GPT-4o / o1 / o3 | ✅ |
| Gemini | ✅ |
| Llama / Mistral | ✅ |
| 任何能读文件的 LLM | ✅ |

### 哲学 5: 经验驱动 (Based on Decades of Engineering Experience)

每个 Skill 封装的不是"AI 技巧"，而是"软件工程最佳实践"：

| Skill | 背后的工程原则 |
|-------|---------------|
| tdd | Kent Beck 的测试驱动开发 |
| grill-me | 需求工程中的利益相关者对齐 |
| diagnose | 系统化根因分析 (RCA) |
| improve-codebase-architecture | Martin Fowler 的重构思想 |
| zoom-out | 架构评审中的"鹰眼视角" |

## 六、四大核心问题域

### 问题域 1: Agent 不做我想要的事

```
症状: Agent 直接开始编码，不确认需求
      写出的代码与期望不符
      反复修改仍不对

根因: Agent 缺乏对齐 (Alignment) 步骤

解法: 
┌──────────────────────────────────────────────┐
│  grill-me — 编码前的需求确认对话              │
│  grill-with-docs — 基于文档验证理解           │
│  CONTEXT.md — 建立共享语言消除歧义            │
└──────────────────────────────────────────────┘
```

### 问题域 2: Agent 太啰嗦

```
症状: Agent 输出大量解释文字
      Token 消耗过高
      信噪比低

根因: Agent 默认倾向详细解释

解法:
┌──────────────────────────────────────────────┐
│  caveman — 指令 Agent 使用极简通信            │
│  CONTEXT.md — 预定义术语避免重复解释          │
│  效果: 75% Token 缩减                        │
└──────────────────────────────────────────────┘
```

### 问题域 3: 代码不能用

```
症状: Agent 写的代码编译不过
      逻辑错误，测试不通过
      修复引入新问题

根因: 缺乏验证循环

解法:
┌──────────────────────────────────────────────┐
│  tdd — Red-Green-Refactor 循环保障            │
│  diagnose — 系统化问题诊断                    │
│  效果: 代码始终在测试保护下演进               │
└──────────────────────────────────────────────┘
```

### 问题域 4: 代码变成泥球 (Ball of Mud)

```
症状: 架构日益复杂
      模块边界模糊
      修改一处牵动全局

根因: 缺乏架构视角

解法:
┌──────────────────────────────────────────────┐
│  improve-codebase-architecture — 增量改善     │
│  zoom-out — 全局视角审视                      │
│  to-prd — 用 PRD 明确边界                    │
│  效果: 持续保持架构清晰                       │
└──────────────────────────────────────────────┘
```

## 七、与其他系统的架构对比

| 维度 | mattpocock/skills | ECC | superpowers |
|------|-------------------|-----|-------------|
| 文件格式 | 纯 Markdown | YAML + Markdown | SKILL.md |
| 运行时 | 无 | Node.js + Rust | 无（方法论） |
| Hooks | 无 | 8-15 种事件 | 自动触发 |
| 学习系统 | 无 | Instincts (CL v2) | 无 |
| 安全 | git-guardrails | AgentShield | 无 |
| Skills 数量 | ~15 | 246 | ~20 |
| 安装复杂度 | 1 条命令 | Plugin/npm/Selective | Plugin |
| 可定制性 | 直接改 MD | 理解系统后修改 | 配置参数 |
| Token 优化 | caveman (75%) | 多策略 (75%) | 无专门优化 |

## 八、关键设计决策

### 决策 1: 不用 YAML Frontmatter

**原因**: 
- 减少格式复杂度
- Agent 不需要解析元数据
- 开发者修改无需了解 schema
- Markdown 本身足够表达结构

### 决策 2: 不实现 Hook 系统

**原因**:
- Hooks 增加部署复杂度
- 需要运行时环境
- 可能引入安全问题
- Skills 的价值在于"指导"而非"自动化"

### 决策 3: 使用 skills.sh 而非 npm

**原因**:
- 避免 node_modules 依赖
- 更轻量的分发方式
- 支持按 Skill 粒度安装
- 跨语言项目可用（不限 Node.js 项目）

### 决策 4: 渐进式披露

**原因**:
- Agent 上下文窗口有限
- 避免信息过载降低效果
- 按需展开提高相关性
- 符合认知负载理论

## 九、待深入研究

- [ ] skills.sh 的内部分发机制（Git 子模块？直接下载？）
- [ ] caveman Skill 的具体 Token 缩减实现
- [ ] 渐进式披露的 Agent 识别机制
- [ ] write-a-skill 元技能的模板结构
- [ ] 与 agentskills.io 标准的兼容性
- [ ] CONTEXT.md 的最佳实践模板

---

*参考来源见 [references.md](./references.md)*
*最后更新: 2026-07*
