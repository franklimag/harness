# 增量更新学习笔记

> graphify 的性能优化 — SHA256 缓存、--watch 模式、git hook 自动触发。

## 概述

graphify 通过 SHA256 文件哈希实现增量更新，避免每次重新处理所有文件。配合 --watch 和 git hook，实现图谱与代码库的实时同步。

## SHA256 缓存机制

### 工作原理

```
首次运行 /graphify:
  file_a.py → SHA256("...content...") = "abc123"
  file_b.ts → SHA256("...content...") = "def456"
  
  处理 file_a.py → 提取结果 → 缓存 {hash: "abc123", entities: [...]}
  处理 file_b.ts → 提取结果 → 缓存 {hash: "def456", entities: [...]}

第二次运行:
  file_a.py → SHA256 = "abc123" → 匹配缓存 → 跳过! ✅
  file_b.ts → SHA256 = "xyz789" (内容变了) → 不匹配 → 重新处理
  file_c.md → 无缓存 (新文件) → 新处理

效果: 只处理变化的文件
```

### 缓存结构

```json
{
  "cache_version": "1.0",
  "entries": {
    "src/services/user.ts": {
      "hash": "sha256:abc123...",
      "entities": [...],
      "relations": [...],
      "timestamp": "2026-07-15T10:00:00Z"
    }
  }
}
```

### 缓存命中率

| 场景 | 命中率 | 说明 |
|------|--------|------|
| 首次运行 | 0% | 所有文件需要处理 |
| 小修改后 | 95%+ | 只有改动文件需要重处理 |
| 大重构后 | 50-70% | 部分文件变化 |
| 新分支切换 | 70-90% | 大部分文件相同 |

## --watch 模式

```
graphify --watch

文件系统监听:
  检测: 创建/修改/删除 事件
  过滤: 忽略 .git/ node_modules/ 等
  触发: 变化文件 → 增量更新图谱

工作流:
  开发者修改代码 → 文件变化事件 → graphify 增量更新 → 图谱实时更新
```

### --watch 与 --update 的区别

| 模式 | 触发方式 | 适用场景 |
|------|---------|---------|
| --watch | 自动 (文件监听) | 开发过程中 |
| --update | 手动执行 | CI/CD / 定时 |

## Git Hook 集成

```
.git/hooks/post-commit:
  #!/bin/sh
  graphify --update

效果: 每次 git commit 后自动更新图谱
```

### 配置方式

```bash
# 安装 git hook
graphify install --git-hook post-commit

# 或手动配置
echo "graphify --update" >> .git/hooks/post-commit
chmod +x .git/hooks/post-commit
```

## 性能优化效果

| 场景 | 无缓存 | 有缓存 | 节省 |
|------|--------|--------|------|
| 52 文件首次 | ~120s | ~120s | 0% |
| 修改 2 个文件后 | ~120s | ~5s | 96% |
| 添加 1 个新文件 | ~120s | ~3s | 98% |
| 无变化重跑 | ~120s | <1s | 99%+ |

## 问题与思考

- [x] SHA256 缓存的核心价值? → 避免重复 API 调用
- [x] --watch vs --update? → 自动 vs 手动触发
- [ ] 文件删除时的图谱清理?
- [ ] 缓存过期策略 (Claude API 更新后)?
- [ ] 大仓库的 --watch 性能?

---

*参考: [architecture.md](../architecture.md) | [01-multimodal-extraction.md](./01-multimodal-extraction.md)*
