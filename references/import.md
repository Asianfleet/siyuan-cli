# import

`import` 是把 Markdown、`.sy.zip` 和完整数据备份导入当前 workspace 的入口。

## 子命令一览

- 文档导入：`md`、`sy`
- 全量数据导入：`data`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `md` | 导入 Markdown 文件或目录到指定笔记本 | `siyuan import md --file <path> --notebook <notebook-id> -w <workspace> --dry-run` | `--file`、`--notebook`、`--path`、`--hpath` | 写入 workspace；成功输出 `ok`，并触发文件树刷新；`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；`--path` 是内部路径，`--hpath` 是人类可读路径 |
| `sy` | 导入 `.sy.zip` 文档包到指定笔记本 | `siyuan import sy --file <path> --notebook <notebook-id> -w <workspace> --dry-run` | `--file`、`--notebook`、`--path`、`--hpath` | 写入 workspace；成功输出 `ok`，并触发文件树刷新；`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；`--file` 应指向 `.sy.zip` 文件 |
| `data` | 导入完整数据备份 zip | `siyuan import data --file <path> -w <workspace> --dry-run` | `--file` | 写入 workspace；成功输出 `ok`；`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；这是全量数据导入，风险高于单文档导入 |

## 备注

- `md` 和 `sy` 都要求目标笔记本 ID；未提供 `--path` 或 `--hpath` 时按默认目标路径导入。
- `--file` 会先解析为本机绝对路径，因此相对路径按当前 shell 工作目录解释。
