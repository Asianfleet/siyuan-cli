# document

`document` 用于查看、创建、读取、删除、重命名、移动、复制文档，并按标题/路径搜索文档。

## 子命令一览

- 查询：`list`、`get`、`info`、`search`
- 写入与组织：`create`、`remove`、`rename`、`move`、`duplicate`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `list` | 列出指定笔记本和父路径下的文档 | `siyuan document list --notebook <notebook-id> [--path <path>] [--hpath <hpath>] -w <workspace>` | `--notebook <notebook-id>`：必填，笔记本 ID；`--path <path>`：可选，内部父路径；默认 `/`，优先级高于 `--hpath`；`--hpath <hpath>`：可选，人类可读父路径；仅在 `--path` 为空时尝试解析，解析不到会回退到 `/` | 只读；表格输出 `ID / NAME / PATH / SIZE / COUNT / MTIME`，JSON 输出文档树文件数组 | `--dry-run` 对该只读命令没有特殊预览语义，仍会执行查询。 |
| `create` | 创建新文档 | `siyuan document create --notebook <notebook-id> --title <title> [--path <path>] [--markdown <markdown>] -w <workspace> [--dry-run]` | `--notebook <notebook-id>`：必填，目标笔记本 ID；`--title <title>`：必填，新文档标题；`--path <path>`：可选，父目录内部路径；默认 `/`；`--markdown <markdown>`：可选，初始 Markdown 内容；默认空 | 写入 workspace；成功输出新文档 ID；`--dry-run` 只打印将创建的标题和笔记本 | `--dry-run` 在检查必填参数后直接返回，不校验父路径是否存在。 |
| `get` | 读取文档块基础信息 | `siyuan document get --id <document-id> -w <workspace>` | `--id <document-id>`：必填，文档块 ID | 只读；表格输出 ID、Title、Type、Box、HPath、Created、Updated 和内容预览，JSON 输出块对象 | 目标不存在时报 `document not found: <id>`。当前表格输出的 `Title`、`Created`、`Updated` 可能为空；标题可从 `Content` 或 JSON 的 `content`/`ial.title` 观察。`--dry-run` 对该只读命令没有特殊预览语义。 |
| `remove` | 删除指定文档 | `siyuan document remove --id <document-id> -w <workspace> [--dry-run]` | `--id <document-id>`：必填，文档块 ID | 写入 workspace；成功输出被删除文档 ID；`--dry-run` 只打印将删除的 ID | `--dry-run` 在检查 `--id` 后直接返回，不校验文档是否存在。删除后 `get` 和 `list` 可立即反映删除；标题搜索可能仍短暂返回旧索引结果。 |
| `rename` | 重命名文档 | `siyuan document rename --id <document-id> --title <title> -w <workspace> [--dry-run]` | `--id <document-id>`：必填，文档块 ID；`--title <title>`：必填，新标题 | 写入 workspace；成功输出文档 ID；`--dry-run` 只打印将重命名的 ID 和新标题 | `--dry-run` 在检查必填参数后直接返回，不校验文档是否存在。 |
| `move` | 移动文档到目标笔记本或父文档 | `siyuan document move --id <document-id> --notebook <notebook-id> [--path <path>] [--hpath <hpath>] -w <workspace> [--dry-run]` | `--id <document-id>`：必填，要移动的文档块 ID；`--notebook <notebook-id>`：必填，目标笔记本 ID；`--path <path>`：可选，目标内部路径；默认 `/`，优先级高于 `--hpath`；`--hpath <hpath>`：可选，目标人类可读路径；仅在 `--path` 为空时尝试解析 | 写入 workspace；成功输出被移动文档 ID；`--dry-run` 只打印将移动的 ID 和目标笔记本 | 移到根路径使用 `/`。移到父文档下时，`--path` 需要传目标父文档的 `.sy` 路径，如 `/<parent-id>.sy`；传 `/<parent-id>/` 会报 `block not found`。当前 `--hpath` 解析到目录路径，子文档移动场景可能同样失败。 |
| `duplicate` | 复制文档 | `siyuan document duplicate --id <document-id> -w <workspace> [--dry-run]` | `--id <document-id>`：必填，要复制的文档块 ID | 写入 workspace；成功输出新复制文档 ID；`--dry-run` 只打印将复制的 ID | 复制件标题会追加 `(Duplicated <时间>)` 后缀。`--dry-run` 在检查 `--id` 后直接返回，不校验文档是否存在。 |
| `info` | 读取文档统计信息 | `siyuan document info --id <document-id> -w <workspace>` | `--id <document-id>`：必填，文档块 ID | 只读；表格输出 ID、RootID、Name、RefCount、SubFileCount，JSON 输出信息对象 | 适合查看引用数和子文档数量。`--dry-run` 对该只读命令没有特殊预览语义。 |
| `search` | 按关键词搜索文档标题/路径 | `siyuan document search <keyword> -w <workspace>` | `<keyword>`：必填，关键词；只使用第一个位置参数 | 只读；表格输出 `NAME / ID / HPATH` 和命中数量，JSON 输出搜索结果数组 | 无关键词会由命令行参数校验报错。删除文档后，`search` 可能仍返回已删除文档的旧索引条目；需要用 `get` 或 `list` 确认文档是否仍存在。`--dry-run` 对该只读命令没有特殊预览语义。 |

## 通用注意事项

- 写入类命令的 `--dry-run` 只做必填参数校验和计划输出，不等同于完整目标 ID、目标笔记本或目标路径校验。
- 文档路径参数存在两种语义：`list` 的 `--path` 指父目录路径；`move` 移入父文档时需要目标父文档的 `.sy` 路径。
- 当前 CLI 会拒绝加密笔记本相关文档操作，报错形式为不支持加密笔记本或加密笔记本文件。
