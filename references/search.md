# search

`search` 是块全文搜索、语义搜索、资源文件内容搜索和按资源路径取全文入口。

## 子命令一览

- 直接调用：不带子命令

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `（默认）` | 搜索块内容、语义结果、资源文件全文索引，或按资源路径读取已索引全文 | `siyuan search <query> [--asset] [--get-asset] [--ext <ext>]... [--notebook <notebook-id>]... [--path <path>]... [--type <type>]... [--subtype <subtype>]... [--method <n>] [--order-by <n>] [--page <n>] [--page-size <n>] [--group-by <n>] -w <workspace>` | `<query>`：必填，查询内容；默认作为块搜索查询，`--asset` 时作为资源内容查询，`--get-asset` 时作为资源路径。`--asset`：可选，切换到资源内容搜索；默认 `false`，优先级高于 `--get-asset`。`--get-asset`：可选，按 `assets/...` 路径读取单个已索引资源全文；默认 `false`。`--ext <ext>`：可选，可重复，资源模式扩展名过滤；默认不过滤，按索引值精确匹配，通常带点如 `.md`。`--notebook <notebook-id>`：可选，可重复，块模式笔记本 ID 过滤；默认不过滤。`--path <path>`：可选，可重复，块模式文档路径前缀过滤；默认不过滤。`--type <type>`：可选，可重复，块模式块类型过滤；未传时使用工作空间搜索配置，传入后覆盖类型集合。`--subtype <subtype>`：可选，可重复，块模式子类型过滤；标题用 `h1`-`h6`，列表用 `o`/`u`/`t`，只在对应父类型启用时生效。`--method <n>`：可选，搜索方法；默认 `0`；块模式 `0=keyword`、`1=query-syntax`、`2=sql`、`3=regex`、`4=semantic`，资源模式使用 `0`-`3`，传 `4` 时按关键字搜索处理。`--order-by <n>`：可选，排序；默认 `0`；块模式 `0=type`、`1=created-asc`、`2=created-desc`、`3=updated-asc`、`4=updated-desc`、`5=content`、`6=relevance-asc`、`7=relevance-desc`；资源模式 `0=relevance-desc`、`1=relevance-asc`、`2=updated-asc`、`3=updated-desc`。`--page <n>`：可选，页码；默认 `1`，小于 `1` 按 `1`。`--page-size <n>`：可选，每页数量；默认 `32`，小于 `1` 按 `32`。`--group-by <n>`：可选，块模式分组；默认 `0`，`1` 按文档分组 | 只读；块模式默认输出 `ID / TYPE / NAME / HPATH` 表格和匹配统计；资源搜索默认输出 `ID / EXT / NAME / SIZE / PATH / CONTENT` 表格；按资源路径读取默认输出资源元数据和完整索引内容；`-f json` 时返回结构化结果 | 无子命令；`--asset` 会忽略块过滤参数；`--get-asset` 不搜索内容，只读取已索引路径 |

## 通用注意事项

- `--dry-run` 对该只读命令没有可观察差异；同一查询带或不带 `--dry-run` 输出一致，错误校验也一致。
- 块模式 SQL 搜索直接执行查询并分页返回；资源模式 SQL 搜索要求单条只读查询，非只读语句会报 `SQL statement is not a read-only query` 并退出失败。
- 关键字块搜索传入单个块 ID 时会按块 ID 精确查找；关键字包含多个空格分隔词时会进入文档全文匹配模式，JSON 中 `docMode` 会变为 `true`。
- `--get-asset` 只读取资源内容索引中已有的路径；未索引或路径不匹配时输出 `No indexed content found for path: ...`，不会去文件系统即时解析。
- 资源扩展名过滤是精确匹配；索引中扩展名通常包含前导点，传 `--ext md` 可能无结果，传 `--ext .md` 才能命中 `.md` 资源。
- `--group-by 1` 仅用于块模式；当前验证环境中，同一关键词不分组可命中而分组返回 `No results found.`，遇到分组结果异常时先去掉该选项确认原始命中。
