# ref

`ref` 是块反链、提及查询和反链刷新入口。

## 子命令一览

- 查询：`backlinks`、`mentions`
- 写入：`refresh`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `backlinks` | 查询指向指定块的反链 | `siyuan ref backlinks --id <block-id> -w <workspace>` | `--id`、`--keyword`、`--sort` | 只读；默认输出 `ID / TYPE / NAME / COUNT` 表格并追加反链数量，`-f json` 时返回反链路径数组后仍输出数量行 | `--keyword` 过滤反链；`--sort` 默认 0 |
| `mentions` | 查询指定块的未链接提及 | `siyuan ref mentions --id <block-id> -w <workspace>` | `--id`、`--keyword`、`--sort` | 只读；默认输出 `ID / TYPE / NAME / COUNT` 表格并追加提及数量，`-f json` 时返回提及路径数组后仍输出数量行 | `--keyword` 过滤提及；`--sort` 默认 0 |
| `refresh` | 刷新指定块的反链数据 | `siyuan ref refresh --id <block-id> -w <workspace> --dry-run` | `--id` | 写入 workspace；成功输出 `ok`，`--dry-run` 只打印计划刷新操作 | 会修改 workspace，建议先用 `--dry-run` |

## 备注

- `backlinks` 和 `mentions` 的 `--sort` 取值为 `0=updated-desc`、`1=updated-asc`、`2=created-desc`、`3=created-asc`、`4=name-desc`、`5=name-asc`、`6=alphanum-desc`、`7=alphanum-asc`。
- `--id` 是块 ID，缺少时会报错。
