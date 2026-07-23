# dailynote

`dailynote` 用于在指定笔记本中创建当天 Daily Note，并向当天 Daily Note 追加或前置 Markdown 块内容。

## 子命令一览

- 创建：`create`
- 写入：`append`、`prepend`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `create` | 创建或确保今天的 Daily Note 存在 | `siyuan dailynote create --notebook <notebook-id> -w <workspace> --dry-run` | `--notebook <notebook-id>`：必填；没有默认值 | `--dry-run` 输出计划；真实执行成功时输出 Daily Note 文档 ID，并可能创建今日文档 | 目标日期取命令运行时的当前日期；`--dry-run` 不校验 notebook 是否存在，只校验 `--notebook` 是否非空；不存在的 notebook 真实执行时报 `notebook not found` |
| `append` | 确保今天的 Daily Note 存在，并在文档末尾追加块 | `siyuan dailynote append --notebook <notebook-id> --data <markdown> -w <workspace> --dry-run` | `--notebook <notebook-id>`：必填；没有默认值。`--data <markdown>`：Markdown 内容，优先级最高，空字符串按未提供处理。`--file <path>`：从文件读取内容；`-` 表示 stdin；未提供 `--data` 和 `--file` 时默认读取 stdin | `--dry-run` 输出计划；真实执行成功时输出 Daily Note 文档 ID，并写入追加块 | `--dry-run` 在读取内容来源前返回，不校验 `--data`、`--file` 或 stdin；真实执行时缺失文件会报文件打开错误且不会写入；空内容会写入空段落 |
| `prepend` | 确保今天的 Daily Note 存在，并在文档开头前置块 | `siyuan dailynote prepend --notebook <notebook-id> --data <markdown> -w <workspace> --dry-run` | `--notebook <notebook-id>`：必填；没有默认值。`--data <markdown>`：Markdown 内容，优先级最高，空字符串按未提供处理。`--file <path>`：从文件读取内容；`-` 表示 stdin；未提供 `--data` 和 `--file` 时默认读取 stdin | `--dry-run` 输出计划；真实执行成功时输出 Daily Note 文档 ID，并写入前置块 | `--dry-run` 在读取内容来源前返回，不校验 `--data`、`--file` 或 stdin；真实执行时缺失文件会报文件打开错误且不会写入；空内容会写入空段落 |



- `append` 和 `prepend` 会先确保当天 Daily Note 存在，再把 Markdown 写入为块。
- Daily Note 的保存路径和模板取目标笔记本配置；如果笔记本未配置有效的 Daily Note 保存路径，真实执行会失败。
- 对同一天、同一笔记本重复执行 `create` 会返回既有 Daily Note 文档 ID，不会创建第二篇当天日记。
