# ref

`ref` 是块反链、提及查询和反链刷新入口。

## 子命令一览

- 查询：`backlinks`、`mentions`
- 写入：`refresh`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `backlinks` | 查询指向指定块的反链 | `siyuan ref backlinks --id <block-id> -w <workspace>` | `--id`：块 ID，必填，默认空；`--keyword`：过滤反链结果，选填，默认空；`--sort`：反链排序，选填，默认 `0`，只影响反链结果 | 只读；默认输出 `ID / TYPE / NAME / COUNT` 表格，空结果输出 `(none)`，最后追加 `<n> backlink(s)` | 缺少 `--id` 退出并报 `--id is required`；不存在的块 ID 不报错，返回空结果；`--dry-run` 与正式查询无可见差异 |
| `mentions` | 查询指定块的未链接提及 | `siyuan ref mentions --id <block-id> -w <workspace>` | `--id`：块 ID，必填，默认空；`--keyword`：过滤提及结果，选填，默认空；`--sort`：提及排序，选填，默认 `0`，只影响提及结果 | 只读；默认输出 `ID / TYPE / NAME / COUNT` 表格，空结果输出 `(none)`，最后追加 `<n> mention(s)` | 缺少 `--id` 退出并报 `--id is required`；不存在的块 ID 不报错，返回空结果；`--dry-run` 与正式查询无可见差异 |
| `refresh` | 刷新指定块的反链数据 | `siyuan ref refresh --id <block-id> -w <workspace> --dry-run` | `--id`：块 ID，必填，默认空 | 写入 workspace；正式执行成功输出 `ok`，`--dry-run` 输出将刷新指定块反链的计划 | 缺少 `--id` 退出并报 `--id is required`；不存在的块 ID 仍输出 `ok`；CLI 会拒绝加密笔记本目标 |



- `backlinks` 和 `mentions` 的 `--sort` 取值为 `0=updated-desc`、`1=updated-asc`、`2=created-desc`、`3=created-asc`、`4=name-desc`、`5=name-asc`、`6=alphanum-desc`、`7=alphanum-asc`。
