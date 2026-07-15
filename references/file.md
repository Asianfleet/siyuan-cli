# file

`file` 是受 workspace 边界保护的文件系统读写、查找、检索和元信息入口。

## 子命令一览

- 目录与文件读取：`list`、`read`、`stat`
- 写入与移动：`write`、`delete`、`rename`、`copy`
- 查找与检索：`grep`、`find`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `list` | 列出 workspace 内指定目录内容 | `siyuan file list <path> -w <workspace>` | `<path>` | 只读；输出 `NAME / SIZE / ISDIR / MODTIME` 表格和条目数 | `<path>` 是相对 workspace 的路径；路径会被校验，不能逃逸 workspace |
| `read` | 读取 workspace 内指定文件内容 | `siyuan file read <path> -w <workspace>` | `<path>` | 只读；直接输出文件文本内容 | 适合读取文本文件；二进制文件会按字节转字符串输出 |
| `write` | 写入文件内容，来源为 stdin 或外部文件 | `siyuan file write <path> --file <source-path> -w <workspace> --dry-run` | `<path>`、`--file` | 写入 workspace；成功输出 `ok`，`--dry-run` 只打印计划写入路径；未指定 `--file` 时从 stdin 读取 | 会修改 workspace，建议先用 `--dry-run`；会自动创建目标父目录并覆盖目标文件 |
| `delete` | 删除 workspace 内文件或目录 | `siyuan file delete <path> -w <workspace> --dry-run` | `<path>` | 写入 workspace；成功输出 `ok`，`--dry-run` 只打印计划删除路径 | 会修改 workspace，建议先用 `--dry-run`；目录会递归删除 |
| `rename` | 重命名或移动 workspace 内文件或目录 | `siyuan file rename <old> <new> -w <workspace> --dry-run` | `<old>`、`<new>` | 写入 workspace；成功输出 `ok`，`--dry-run` 只打印移动计划 | 会修改 workspace，建议先用 `--dry-run`；会自动创建新路径的父目录 |
| `copy` | 复制 workspace 内文件或目录 | `siyuan file copy <src> <dst> -w <workspace> --dry-run` | `<src>`、`<dst>` | 写入 workspace；成功输出 `ok`，`--dry-run` 只打印复制计划 | 会修改 workspace，建议先用 `--dry-run`；目录会递归复制 |
| `grep` | 用正则搜索文件内容 | `siyuan file grep --pattern <regex> --path <path> -w <workspace>` | `--pattern`、`--path`、`--include`、`--context`、`--limit` | 只读；默认输出匹配行，`-f json` 时返回匹配结果数组 | `--path` 是搜索根路径；`--include` 支持文件名 glob；`--limit` 小于等于 0 时源码会重置为 200 |
| `find` | 在指定路径下查找文件 | `siyuan file find <path> -w <workspace>` | `<path>`、`--include`、`--limit` | 只读；默认输出文件路径列表，`-f json` 时返回路径数组 | 会跳过隐藏目录和常见 VCS 目录；`--include` 按文件名 glob 匹配；`--limit` 小于等于 0 时源码会重置为 200 |
| `stat` | 查看 workspace 内文件或目录信息 | `siyuan file stat <path> -w <workspace>` | `<path>` | 只读；默认输出路径、大小、是否目录和修改时间，`-f json` 时返回同等字段对象 | 路径必须位于 workspace 内，文件不存在会报错 |

## 备注

- `file` 会把传入路径规范化为 workspace 内路径，并拒绝逃逸 workspace 的相对路径。
- 写入类命令包括 `write`、`delete`、`rename` 和 `copy`，都支持 `--dry-run` 预览。
