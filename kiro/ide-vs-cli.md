# Kiro IDE vs CLI 对比

> 基于 Windows + PowerShell + WSL2 环境的选择分析。

## 功能对比

| 能力 | Kiro IDE | Kiro CLI |
|------|----------|----------|
| MCP (Model Context Protocol) | ✅ | ✅ |
| 终端命令 allow/denylist | ✅ | ✅ |
| Steering 文件 | ✅ | ✅ |
| Hooks (事件驱动自动化) | ✅ | ✅ |
| 自定义 subagents / Skills | ✅ | ✅ |
| Powers | ✅ | ✅ |
| **Spec-driven development** | ✅ | ❌ |
| Terminal UI (TUI) | ❌ | ✅ |
| Headless mode | ❌ | ✅ |
| GUI 代码编辑 | ✅ | ❌ |

**关键区别：Specs 只在 IDE 中可用。**

## Windows 环境兼容性

| 环境 | Kiro IDE | Kiro CLI |
|------|----------|----------|
| Windows 原生 (PowerShell) | ✅ | ✅ (Windows 11) |
| WSL2 | ⚠️ 需要 Open Remote - WSL 扩展 | ✅ 原生 Linux 运行 |
| Git Bash | ✅ | 未明确支持 |

### WSL2 注意事项

- Kiro IDE 基于 VS Code fork，**不能用官方 VS Code WSL 扩展**
- 需要安装 `Open Remote - WSL` 插件（第三方）
- CLI 可以直接在 WSL2 里安装和运行（Linux 二进制）
- 如果 WSL2 集成有问题，CLI 是更稳定的后备方案

## 场景推荐

| 场景 | 推荐 | 原因 |
|------|------|------|
| 日常开发 / vibe coding | **IDE** | GUI + Specs + 完整体验 |
| 需要 Specs 功能 | **IDE** | CLI 不支持 |
| CI/CD 自动化 | CLI | headless mode |
| WSL2 内工具链 | CLI | 原生兼容最好 |
| 批量脚本任务 | CLI | 可编程 |
| 学习 Agent Harness | **IDE** | Specs 对照 Superpowers 有体感 |

## 结论

**主力用 Kiro IDE**，CLI 作为自动化和 WSL2 场景的补充。

---

*最后更新: 2026-05-26*
