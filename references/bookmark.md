# bookmark

`bookmark` 是书签统计、标签列表、删除和重命名入口。

## 子命令一览

- 查询：`list`、`labels`
- 写入：`remove`、`rename`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `list` | 列出书签及命中数量 | `siyuan bookmark list -w <workspace>` | 无 | 只读；默认输出 `NAME / COUNT` 表格，`-f json` 时返回书签集合 | 用于先查看当前有哪些书签名及对应数量 |
| `labels` | 列出全部书签标签 | `siyuan bookmark labels -w <workspace>` | 无 | 只读；默认输出 `LABEL` 表格，`-f json` 时返回标签字符串数组 | 只返回标签名，不返回每个标签的数量 |
| `remove` | 删除指定书签标签 | `siyuan bookmark remove --label <label> -w <workspace> --dry-run` | `--label` | 写入 workspace；`--dry-run` 只打印计划删除的书签标签 | 会修改 workspace，建议先用 `--dry-run`；删除后会触发文件树刷新 |
| `rename` | 将旧书签标签重命名为新标签 | `siyuan bookmark rename --old <old> --new <new> -w <workspace> --dry-run` | `--old`、`--new` | 写入 workspace；`--dry-run` 只打印计划重命名操作 | 会修改 workspace，建议先用 `--dry-run`；重命名后会触发文件树刷新 |

## 备注

- `remove` 和 `rename` 都要求标签名非空，缺少必需参数会报错。
- 书签写入操作按标签名处理，不按块 ID 或文档路径处理。
