# tag

`tag` 是标签检索、删除和重命名入口。

## 子命令一览

- 查询：`list`
- 写入：`remove`、`rename`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `list` | 列出标签，可按关键字过滤 | `siyuan tag list -w <workspace>` | `--keyword` | 只读；默认输出 `LABEL` 表格，`-f json` 时返回标签字符串数组 | `--keyword` 为空时列出全部标签 |
| `remove` | 删除指定标签 | `siyuan tag remove --label <label> -w <workspace> --dry-run` | `--label` | 写入 workspace；`--dry-run` 只打印计划删除的标签 | 会修改 workspace，建议先用 `--dry-run`；删除后会触发标签刷新 |
| `rename` | 将旧标签名重命名为新标签名 | `siyuan tag rename --old <old-label> --new <new-label> -w <workspace> --dry-run` | `--old`、`--new` | 写入 workspace；`--dry-run` 只打印计划重命名操作 | 会修改 workspace，建议先用 `--dry-run`；重命名后会触发标签刷新 |

## 备注

- `remove` 和 `rename` 都要求标签名非空，缺少必需参数会报错。
- `tag` 的写入类操作直接作用于工作空间标签数据，适合先用 `list` 确认目标标签。
