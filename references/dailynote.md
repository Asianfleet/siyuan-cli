# dailynote

`dailynote` 是创建当天日记并向当天日记追加或前置块内容的入口。

## 子命令一览

- 创建：`create`
- 写入：`append`、`prepend`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `create` | 在指定笔记本中创建或确保今天的日记存在 | `siyuan dailynote create --notebook <notebook-id> -w <workspace> --dry-run` | `--notebook` | 写入 workspace；成功输出日记文档 ID，`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；命令按当前日期创建当天日记 |
| `append` | 确保今天的日记存在，并在末尾追加块 | `siyuan dailynote append --notebook <notebook-id> --data <markdown> -w <workspace> --dry-run` | `--notebook`、`--data`、`--file` | 写入 workspace；成功输出日记文档 ID，`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；内容来源优先 `--data`，其次 `--file <path>`，`--file -` 表示 stdin |
| `prepend` | 确保今天的日记存在，并在开头插入块 | `siyuan dailynote prepend --notebook <notebook-id> --data <markdown> -w <workspace> --dry-run` | `--notebook`、`--data`、`--file` | 写入 workspace；成功输出日记文档 ID，`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；内容来源优先 `--data`，其次 `--file <path>`，`--file -` 表示 stdin |

## 备注

- 本页基于 SiYuan v3.7.2-alpha.2 的 `kernel/cli/cmd/dailynote.go`。
- `append` 和 `prepend` 会先调用日记创建逻辑，再把 Markdown 转成块 DOM 后写入。
- 日记目标日期由运行时当前日期决定；批处理脚本中应确认执行环境时间。
