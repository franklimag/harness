# Kiro IDE (Windows) — Steering / Skills / Hooks 配置指南

基于官方文档和社区资料整理，适用于 Windows 10/11 环境。

---

## 一、核心路径约定

| 级别 | 路径 (Windows) | 路径 (Linux/macOS) | 作用域 |
|------|---------------|-------------------|--------|
| **用户级 (全局)** | `%USERPROFILE%\.kiro\` | `~/.kiro/` | 所有项目生效 |
| **项目级 (工作区)** | `<项目根目录>\.kiro\` | `<workspace>/.kiro/` | 仅当前项目 |

> **优先级规则**：当全局和项目级存在冲突指令时，**项目级优先**。

> **自定义路径**：设置环境变量 `KIRO_HOME` 可将全局 `~/.kiro` 目录重定向到其他位置（适用于多配置切换场景）。

---

## 二、Steering（行为引导文件）

### 目录结构

```
%USERPROFILE%\.kiro\steering\       ← 用户级（全局）
<项目>\.kiro\steering\              ← 项目级
```

### 文件格式

Markdown 文件（`.md`），头部带 YAML frontmatter：

```markdown
---
inclusion: always          # 加载模式
---

# 我的编码规范

- 使用 TypeScript strict mode
- 所有 API 返回统一 Result 类型
- 错误信息使用中文
```

### 三种加载模式 (inclusion)

| 模式 | 说明 | 适用场景 |
|------|------|---------|
| `always` | **每次对话**都注入（默认） | 技术栈声明、编码规范 |
| `fileMatch` | 仅当 Kiro 读取匹配 glob 的文件时注入 | API 规范（仅在编辑 `*.controller.ts` 时生效） |
| `manual` | 用户手动触发 | 偶尔使用的特殊指南 |

### fileMatch 示例

```markdown
---
inclusion: fileMatch
globs:
  - "src/api/**/*.ts"
  - "src/routes/**/*.ts"
---

# API 设计规范

- 所有接口使用 RESTful 命名
- 响应体统一包装 { code, data, message }
```

### 引用外部文件

Steering 文件支持引用项目内其他文件：

```markdown
请按照以下 OpenAPI 规范实现接口：
#[[file:docs/openapi.yaml]]
```

---

## 三、Skills（可组合能力扩展）

### 目录结构

```
%USERPROFILE%\.kiro\skills\         ← 用户级（全局，跨项目可用）
<项目>\.kiro\skills\                ← 项目级（仅当前项目）
```

### 文件格式

每个 Skill 是一个**文件夹**，其中必须包含 `SKILL.md`：

```
.kiro/skills/
└── code-review/
    └── SKILL.md
└── deploy-check/
    └── SKILL.md
    └── checklist.md       ← 可选的辅助文件
```

### SKILL.md 格式

```markdown
---
name: code-review
description: "执行代码审查，检查最佳实践、性能问题和安全漏洞"
---

# Code Review Skill

## 步骤

1. 检查变更文件列表
2. 对每个文件执行以下检查：
   - 是否有未处理的错误
   - 是否存在性能问题（N+1 查询、内存泄漏）
   - 是否有安全漏洞（SQL 注入、XSS）
3. 生成审查报告
```

### 激活方式

| 方式 | 说明 |
|------|------|
| **自动激活** | Kiro 根据 `description` 匹配用户请求，自动加载 |
| **Slash 命令** | 在聊天中输入 `/code-review`（使用 skill 的 `name`） |

> **关键技巧**：`description` 写得具体，激活率越高。避免模糊描述。

---

## 四、Hooks（事件驱动自动化）

### 目录结构

```
%USERPROFILE%\.kiro\hooks\          ← 用户级（所有项目生效）
<项目>\.kiro\hooks\                 ← 项目级（仅当前项目）
```

### 触发事件类型

| 触发器 | 说明 | 典型用途 |
|--------|------|---------|
| **File Save** | 文件保存时触发 | 自动更新测试/文档/翻译 |
| **File Create** | 新建文件时触发 | 自动生成样板代码 |
| **File Delete** | 删除文件时触发 | 清理相关引用 |
| **Agent Stop** | Agent 完成任务后触发 | 安全扫描、代码规范检查 |
| **Prompt Submit** | 用户提交 prompt 时触发 | 日志审计 |
| **Manual** | 手动触发 | 按需执行的工作流 |

### Hook 动作类型

| 动作 | 说明 |
|------|------|
| **Agent Prompt** ("Ask Kiro") | 将指令发给 Kiro Agent 执行 |
| **Shell Command** | 直接执行 shell 命令 |

### 创建 Hook 的方式

1. **通过 IDE UI**：在 Kiro 侧边栏的 Hooks 面板中点击 `+` 创建
2. **通过聊天**：直接告诉 Kiro "创建一个 hook，当保存 `*.ts` 文件时自动运行测试"
3. **手动创建文件**：在 `.kiro/hooks/` 目录下添加配置文件

### Hook 示例：保存时自动同步翻译

```
Trigger Type: File Save
Target: src/locales/en/*.json

Agent Prompt:
当英文 locale 文件更新时：
1. 识别新增或修改的 key
2. 检查所有其他语言文件
3. 对缺失的 key 添加 "NEEDS_TRANSLATION" 标记
4. 对修改的 key 标记为 "NEEDS_REVIEW"
```

### Hook 示例：Agent 完成后安全扫描

```
Trigger Type: Agent Stop

Agent Prompt:
审查变更文件的安全问题：
1. 查找代码中的 API key、token 或凭证
2. 检查私钥或敏感证书
3. 扫描硬编码的内部 URL
4. 检测数据库连接凭证
```

---

## 五、Windows 完整目录结构总览

```
%USERPROFILE%\.kiro\                    ← 用户级全局配置
├── steering\                           ← 全局 steering 文件
│   ├── coding-standards.md
│   └── my-preferences.md
├── skills\                             ← 全局 skills
│   └── code-review\
│       └── SKILL.md
└── hooks\                              ← 全局 hooks
    └── security-scan.md

C:\Users\你\Projects\my-app\.kiro\      ← 项目级配置
├── steering\                           ← 项目 steering
│   ├── tech-stack.md
│   └── api-conventions.md
├── skills\                             ← 项目 skills
│   └── deploy\
│       └── SKILL.md
└── hooks\                              ← 项目 hooks
    └── auto-test.md
```

---

## 六、实用建议

| 场景 | 放在哪里 | 示例 |
|------|---------|------|
| 个人编码风格偏好 | 用户级 steering | 偏好函数式、使用中文注释 |
| 项目技术栈声明 | 项目级 steering | "本项目用 Next.js 14 + Prisma + PostgreSQL" |
| 通用代码审查流程 | 用户级 skills | 跨项目统一的 review 流程 |
| 项目特定部署检查 | 项目级 skills | 检查 AWS CDK 配置 |
| 保存时自动格式化 | 项目级 hooks | ESLint + Prettier |
| 安全扫描（所有项目） | 用户级 hooks | 防止提交敏感信息 |

---

## 参考来源

- [Kiro Steering 官方文档](https://kiro.dev/docs/steering/)
- [Kiro Skills 官方文档](https://kiro.dev/docs/skills/)
- [Kiro Hooks 官方文档](https://kiro.dev/docs/hooks/)
- [Kiro Hook Examples](https://kiro.dev/docs/hooks/examples/)
- [Kiro CLI Configuration (KIRO_HOME)](https://kiro.dev/docs/cli/chat/configuration)
- [Windows 路径参考 (mcgarrah.org)](https://mcgarrah.org/kiro-ide-parallel-personas-director-developer/)
- [Hooks 用户级/项目级 (ducea.com)](https://www.ducea.com/2025/10/12/howto-use-kiro-hooks-to-enforce-iac-standards/)

*Content was rephrased for compliance with licensing restrictions.*
