# graphify 版本更新追踪

> 跟踪 graphify 的版本发布、功能演进和社区增长。

## 项目概况

graphify 于 2026 年 4 月发布，在 10 天内获得 22K Stars，展示了极高的社区兴趣。

## 发展时间线

### 发布阶段 (April 2026)

| 事件 | 说明 |
|------|------|
| 首次发布 | PyPI: graphifyy |
| 核心功能 | tree-sitter + Claude + NetworkX + vis.js |
| 10 天 22K Stars | 爆发式增长 |
| `/graphify` 命令 | Claude Code 集成 |

### 功能完善阶段

| 功能 | 说明 |
|------|------|
| Claude Vision | 图片输入支持 |
| PDF 解析 | 论文引用挖掘 |
| Leiden 社区检测 | 自动分组 |
| Obsidian 导出 | Vault 格式输出 |
| --watch 模式 | 文件变化自动同步 |
| 增量更新 | SHA256 缓存 |

### 当前功能集

| 功能 | 状态 |
|------|------|
| tree-sitter 代码解析 | ✅ 稳定 |
| Claude 文档分析 | ✅ 稳定 |
| Claude Vision 图片 | ✅ 稳定 |
| PDF 引用挖掘 | ✅ 稳定 |
| graph.html 可视化 | ✅ 稳定 |
| Obsidian Vault | ✅ 稳定 |
| Wiki 导出 | ✅ 稳定 |
| GRAPH_REPORT.md | ✅ 稳定 |
| --watch 模式 | ✅ 稳定 |
| --neo4j 导出 | ✅ 稳定 |
| --mcp Server | ✅ 稳定 |
| SHA256 缓存 | ✅ 稳定 |

## 支持的语言演进

```
初始: Python + TypeScript + JavaScript
  │
  ▼
扩展: + Go + Rust
  │
  ▼
当前: + Java + 更多 tree-sitter 支持的语言
```

## 输出格式演进

```
初始: graph.html + graph.json
  │
  ▼
扩展: + obsidian/ + wiki/ + GRAPH_REPORT.md
  │
  ▼
导出: + --svg + --graphml + --neo4j + --mcp
```

## 社区增长

| 指标 | 值 |
|------|-----|
| Stars | 30K+ |
| 10 天增长 | 22K |
| PyPI 下载 | 高增长 |

## 关注的未来方向

- [ ] 更多编程语言支持
- [ ] 视频输入支持
- [ ] 实时协作图谱
- [ ] 图谱版本控制 (时间旅行)
- [ ] AI 辅助图谱查询优化
- [ ] 多仓库图谱合并

---

*最后更新: 2026-07*
