# search

`search` 是块全文搜索、语义搜索、资源文件内容搜索和按资源路径取全文入口。

## 子命令一览

- 直接调用：`search <query>`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `search <query>` | 搜索块内容、语义结果或资源文件全文索引 | `siyuan search <query> -w <workspace>` | `<query>`、`--asset`、`--get-asset`、`--ext`、`--notebook`、`--path`、`--type`、`--subtype`、`--method`、`--order-by`、`--page`、`--page-size`、`--group-by` | 只读；块模式默认输出 `ID / TYPE / NAME / HPATH` 表格和匹配统计；资源模式默认输出 `ID / EXT / NAME / SIZE / PATH / CONTENT` 表格；`-f json` 时返回结构化结果 | 无子命令；`--asset` 将 `<query>` 作为资源内容查询，`--get-asset` 将 `<query>` 作为资源路径如 `assets/foo.pdf` |

## 备注

- `--method` 支持 `0=keyword`、`1=query-syntax`、`2=sql`、`3=regex`、`4=semantic`；资源模式只使用 `0-3`。
- 块模式的 `--order-by` 为 `0=type`、`1=created-asc`、`2=created-desc`、`3=updated-asc`、`4=updated-desc`、`5=content`、`6=relevance-asc`、`7=relevance-desc`。
- 资源模式的 `--order-by` 为 `0=relevance-desc`、`1=relevance-asc`、`2=updated-asc`、`3=updated-desc`。
- `--page` 小于 1 时按 1 处理，`--page-size` 小于 1 时按 32 处理。
- `--group-by` 仅用于块模式，`0=none`、`1=document`。
