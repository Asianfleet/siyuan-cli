# file

`file` 是受 workspace 边界保护的文件系统入口，用于列出、读取、写入、移动、复制、删除、查找和检索 workspace 内文件。

## 子命令一览

- 目录与文件读取：`list`、`read`、`stat`
- 写入与移动：`write`、`delete`、`rename`、`copy`
- 查找与检索：`grep`、`find`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `list` | 列出 workspace 内目录内容 | `siyuan file list <path> -w <workspace>` | `<path>`：必填，workspace 内相对路径或会被解析到 workspace 内的路径 | 只读；输出 `NAME / SIZE / ISDIR / MODTIME` 表格，随后输出条目数 | `--dry-run` 不改变行为，仍会实际读取目录；目标不存在、不是目录或路径逃逸时退出码为 1，并输出 `Error:` 和用法 |
| `read` | 读取 workspace 内文件内容 | `siyuan file read <path> -w <workspace>` | `<path>`：必填，workspace 内相对路径或会被解析到 workspace 内的路径 | 只读；直接输出文件内容，不额外包装 | `--dry-run` 不改变行为，仍会实际读取文件；二进制内容会按字节转文本输出；目标不存在、是目录或路径逃逸时退出码为 1 |
| `stat` | 查看 workspace 内文件或目录元信息 | `siyuan file stat <path> -w <workspace>` | `<path>`：必填，workspace 内相对路径或会被解析到 workspace 内的路径 | 只读；表格模式输出 `Path / Size / IsDir / ModTime`，`-f json` 输出同等字段对象 | `--dry-run` 不改变行为，仍会实际读取元信息；目标不存在或路径逃逸时退出码为 1 |
| `write` | 写入文件内容，来源为 stdin 或外部文件 | `siyuan file write <path> --file <source-path> -w <workspace> --dry-run` | `<path>`：必填，目标路径；`--file <source-path>`：可选，默认空；为空时读取 stdin，非空时优先读取该文件并忽略 stdin | 写入 workspace；成功输出 `ok`；会自动创建目标父目录并覆盖目标文件 | `--dry-run` 只校验目标路径边界并输出计划，不读取 `--file`、不读取 stdin、不创建父目录、不检查目标是否可写；目标路径逃逸时即使带 `--dry-run` 也会失败 |
| `delete` | 删除 workspace 内文件或目录 | `siyuan file delete <path> -w <workspace> --dry-run` | `<path>`：必填，workspace 内文件或目录路径 | 写入 workspace；成功输出 `ok`；目录会递归删除 | `--dry-run` 只校验路径边界并输出计划，不检查目标是否存在；不带 `--dry-run` 时目标不存在、无权限或路径逃逸会失败 |
| `rename` | 重命名或移动 workspace 内文件或目录 | `siyuan file rename <old> <new> -w <workspace> --dry-run` | `<old>`：必填，源路径；`<new>`：必填，目标路径 | 写入 workspace；成功输出 `ok`；会自动创建新路径父目录 | `--dry-run` 只校验两个路径边界并输出计划，不检查源是否存在；不带 `--dry-run` 时源不存在、目标不可写或路径逃逸会失败 |
| `copy` | 复制 workspace 内文件或目录 | `siyuan file copy <src> <dst> -w <workspace> --dry-run` | `<src>`：必填，源路径；`<dst>`：必填，目标路径 | 写入 workspace；成功输出 `ok`；文件复制会创建目标父目录，目录复制会递归复制内容 | `--dry-run` 只校验两个路径边界并输出计划，不检查源是否存在；复制到已有文件会覆盖内容，复制到已有目录会写入或覆盖同名文件 |
| `grep` | 用正则搜索文件内容 | `siyuan file grep --pattern <regex> --path <path> -w <workspace>` | `--pattern <regex>`：必填；`--path <path>`：必填，搜索根文件或目录；`--include <glob>`：可选，默认空，按文件名过滤，支持 `{a,b}` 展开；`--context <n>`：可选，默认 `0`；`--limit <n>`：可选，默认 `200` | 只读；表格模式输出 `Found N lines:` 和 `path:line: text`，上下文行使用 `path:line-: text`；`-f json` 输出匹配数组 | `--dry-run` 不改变行为，仍会实际搜索；`--pattern` 或 `--path` 缺失时退出码为 1；`--limit <= 0` 当前会重置为 `200`，帮助文本中的“unlimited”与实际行为不一致 |
| `find` | 在指定路径下递归查找文件 | `siyuan file find <path> -w <workspace>` | `<path>`：必填，搜索根路径；`--include <glob>`：可选，默认空，按文件名过滤，支持 `{a,b}` 展开；`--limit <n>`：可选，默认 `200` | 只读；表格模式输出 `Found N files:` 和 workspace 相对路径列表；`-f json` 输出路径数组 | `--dry-run` 不改变行为，仍会实际遍历；只返回常规文件；会跳过隐藏目录以及 `.git`、`.svn`、`.hg`；`--limit <= 0` 当前会重置为 `200`，且达到上限时输出不标明结果已截断 |

## 通用注意事项

- 所有路径都会先规范化并限制在 workspace 内，`..` 等路径逃逸会被拒绝，退出码为 1。
- `file` 不支持访问加密笔记本中的文件；命中加密笔记本路径会失败。
- 写入类命令包括 `write`、`delete`、`rename`、`copy`。这些命令支持 `--dry-run` 预览，但预览只代表路径边界检查和计划输出，不等同于完整可执行性校验。
- 只读命令包括 `list`、`read`、`stat`、`grep`、`find`。这些命令带 `--dry-run` 时不会进入预览模式。
