# document

`document` 是文档树查询、创建、读取、删除、重命名、移动、复制、信息查看和标题搜索入口。

## 子命令一览

- 查询：`list`、`get`、`info`、`search`
- 写入与组织：`create`、`remove`、`rename`、`move`、`duplicate`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `list` | 列出指定笔记本和路径下的文档 | `siyuan document list --notebook <notebook-id> -w <workspace>` | `--notebook`、`--path`、`--hpath` | 只读；默认输出 `ID / NAME / PATH / SIZE / COUNT / MTIME`，`-f json` 时返回文档树文件数组 | `--path` 优先于 `--hpath`；两者都不传时读取根路径 `/` |
| `create` | 创建新文档 | `siyuan document create --notebook <notebook-id> --title <title> -w <workspace> --dry-run` | `--notebook`、`--title`、`--path`、`--markdown` | 写入 workspace；成功输出新文档 ID，`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；`--path` 默认 `/`，`--markdown` 可提供初始 Markdown 内容 |
| `get` | 读取文档块基础信息 | `siyuan document get --id <document-id> -w <workspace>` | `--id` | 只读；默认输出 ID、标题、类型、笔记本、HPath、创建/更新时间和内容预览，`-f json` 时返回块对象 | 目标必须是可加载的文档块 ID，否则报 `document not found` |
| `remove` | 删除指定文档 | `siyuan document remove --id <document-id> -w <workspace> --dry-run` | `--id` | 写入 workspace；成功输出被删除的文档 ID，`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；删除后会刷新文件树和父文档信息 |
| `rename` | 重命名文档 | `siyuan document rename --id <document-id> --title <title> -w <workspace> --dry-run` | `--id`、`--title` | 写入 workspace；成功输出文档 ID，`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；`--id` 或 `--title` 为空会报错 |
| `move` | 将文档移动到目标笔记本和路径 | `siyuan document move --id <document-id> --notebook <notebook-id> -w <workspace> --dry-run` | `--id`、`--notebook`、`--path`、`--hpath` | 写入 workspace；成功输出文档 ID，`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；`--path` 优先于 `--hpath`，两者都不传时移动到目标笔记本根路径 |
| `duplicate` | 复制文档 | `siyuan document duplicate --id <document-id> -w <workspace> --dry-run` | `--id` | 写入 workspace；成功输出原文档树 ID，`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；源码调用复制后刷新文件树，但输出值来自原文档树 ID |
| `info` | 读取文档统计信息 | `siyuan document info --id <document-id> -w <workspace>` | `--id` | 只读；默认输出 ID、RootID、Name、RefCount、SubFileCount，`-f json` 时返回信息对象 | 适合快速查看引用数和子文档数量 |
| `search` | 按关键词搜索文档标题/路径信息 | `siyuan document search <keyword> -w <workspace>` | `<keyword>` | 只读；默认输出 `NAME / ID / HPATH` 和命中数量，`-f json` 时返回搜索结果数组 | 需要至少 1 个位置参数；源码只取第一个参数作为关键词 |

## 备注

- 本页基于 SiYuan v3.7.2-alpha.2 的 `kernel/cli/cmd/document.go`。
- `list` 和 `move` 的路径解析规则一致：显式 `--path` 优先，其次用 `--hpath` 解析到内部路径，最后回退到 `/`。
- 写入类命令均支持全局 `--dry-run`；正式执行前应确认文档 ID、目标笔记本 ID 和目标路径。
