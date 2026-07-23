# import

`import` 用于把 Markdown、`.sy.zip` 文档包或完整数据备份导入当前 workspace。

## 子命令一览

- 文档导入：`md`、`sy`
- 全量数据导入：`data`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `md` | 导入 Markdown 文件或目录到指定笔记本 | `siyuan import md --file <path> --notebook <notebook-id> -w <workspace> --dry-run` | `--file`：必填，本机 Markdown 文件或目录；相对路径按当前 shell 工作目录解析。`--notebook`/`-n`：必填，目标笔记本 ID。`--path`：可选，目标内部路径；默认 `/`；优先级高于 `--hpath`。`--hpath`：可选，目标人类可读路径；仅在未传 `--path` 时使用，找不到时回退 `/`。 | `--dry-run` 输出计划且不写入；正式导入成功输出 `ok`，写入目标笔记本并刷新文件树。 | `--dry-run` 只检查 `--file` 和 `--notebook` 是否填写，不检查文件是否存在、目标笔记本是否存在或目标路径是否有效。正式执行时，文件不存在会报 `open ... The system cannot find the file specified.`。已知 bug：`--path` 指向不存在的内部路径时仍输出 `ok`，但不会导入文档。 |
| `sy` | 导入 `.sy.zip` 文档包到指定笔记本 | `siyuan import sy --file <path> --notebook <notebook-id> -w <workspace> --dry-run` | `--file`：必填，本机 `.sy.zip` 文档包；相对路径按当前 shell 工作目录解析。`--notebook`/`-n`：必填，目标笔记本 ID。`--path`：可选，目标内部路径；默认 `/`；优先级高于 `--hpath`。`--hpath`：可选，目标人类可读路径；仅在未传 `--path` 时使用，找不到时回退 `/`。 | `--dry-run` 输出计划且不写入；正式导入成功输出 `ok`，写入目标笔记本并刷新文件树。 | `--dry-run` 只检查 `--file` 和 `--notebook` 是否填写，不检查文件是否存在、目标笔记本是否存在或压缩包结构是否有效。正式执行时，文件不存在会报 `open ... The system cannot find the file specified.`；压缩包不是有效文档包会报“这不是一个有效的 .sy.zip 压缩包”。已知 bug：`--path` 指向不存在的内部路径时仍输出 `ok`，但不会导入文档。 |
| `data` | 导入完整数据备份 zip | `siyuan import data --file <path> -w <workspace> --dry-run` | `--file`：必填，本机 Data 备份 zip；相对路径按当前 shell 工作目录解析。 | `--dry-run` 输出计划且不写入；正式导入成功输出 `ok`，写入 workspace `data/` 并触发重建索引。 | 全量数据导入风险高，会把备份内容复制进当前 workspace `data/`，可能覆盖同名文件；只在确认备份来源和目标 workspace 后正式执行。`--dry-run` 只检查 `--file` 是否填写，不检查文件是否存在或压缩包结构是否有效。正式执行时，文件不存在会报 `open ... The system cannot find the file specified.`；压缩包不是有效 Data 备份会报“这不是一个有效的 Data 压缩包”。 |

## 通用注意事项

- 缺少必填参数时，`--dry-run` 和正式执行都会在写入前报错并显示当前子命令用法：`md`/`sy` 缺 `--file` 报 `--file is required`，缺 `--notebook` 报 `--notebook is required`；`data` 缺 `--file` 报 `--file is required`。
- `-f json` 不改变 `import` 成功输出；成功仍输出纯文本 `ok`，dry-run 仍输出纯文本计划。
