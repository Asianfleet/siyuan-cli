# SiYuan CLI Skill

[SiYuan](https://github.com/siyuan-note/siyuan) 命令行参考 skill，用于 Agent 需要通过 `siyuan` CLI 操作、检查、查询、导入、导出、同步或管理 SiYuan workspace 时提供离线命令指引。当前内容整理自 SiYuan [文档](https://github.com/siyuan-note/siyuan#-command-line-interface)和[源码](https://github.com/siyuan-note/siyuan/tree/master/kernel/cli/cmd)。

## 文件结构

- `SKILL.md`：skill 入口文件，包含触发条件、前置检查、常用命令索引、全局参数和安全注意事项。
- `references/index.md`：按顶层命令组织的详细参考索引。
- `references/*.md`：每个顶层命令的离线参考页，包含子命令一览、最小调用语法、关键参数、副作用和备注。

---

| 分类 | 顶层命令 | 用途 |
|---|---|---|
| Notebooks & Documents | [`references/notebook`](references/notebook.md) | 管理笔记本 |
| Notebooks & Documents | [`references/document`](references/document.md) | 管理文档 |
| Notebooks & Documents | [`references/dailynote`](references/dailynote.md) | 管理当日笔记 |
| Content | [`references/block`](references/block.md) | 操作块内容 |
| Content | [`references/attr`](references/attr.md) | 管理块属性 |
| Content | [`references/outline`](references/outline.md) | 查询文档大纲 |
| Metadata | [`references/tag`](references/tag.md) | 管理标签 |
| Metadata | [`references/bookmark`](references/bookmark.md) | 管理书签 |
| Metadata | [`references/template`](references/template.md) | 管理模板 |
| Queries | [`references/search`](references/search.md) | 全文检索 |
| Queries | [`references/sql`](references/sql.md) | SQL 查询 |
| References | [`references/ref`](references/ref.md) | 反链与引用 |
| Import/Export | [`references/export`](references/export.md) | 导出文档或数据 |
| Import/Export | [`references/import`](references/import.md) | 导入文件或数据 |
| Import/Export | [`references/inbox`](references/inbox.md) | 管理云端收集箱 |
| Data Management | [`references/repo`](references/repo.md) | 管理快照 |
| Data Management | [`references/history`](references/history.md) | 管理数据历史 |
| Data Management | [`references/sync`](references/sync.md) | 云端同步 |
| Utilities | [`references/asset`](references/asset.md) | 管理资源文件 |
| Utilities | [`references/file`](references/file.md) | 操作 workspace 内文件 |
| Database | [`references/database`](references/database.md) | 管理属性视图数据库 |
| Server | [`references/serve`](references/serve.md) | 启动 kernel 服务 |
| Workspace & System | [`references/workspace`](references/workspace.md) | 管理工作空间 |
| Workspace & System | [`references/system`](references/system.md) | 查看系统信息 |
| Workspace & System | [`references/completion`](references/completion.md) | 生成 shell 自动补全脚本 |
| Workspace & System | [`references/help`](references/help.md) | 查看命令帮助 |
