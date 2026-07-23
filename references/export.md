# export

`export` 从 workspace 导出文档内容、文档压缩包或完整数据备份。

## 子命令一览

- 单文档内容：`md`、`html`、`preview`
- Word 文档：`docx`
- 文档压缩包：`sy`、`md-zip`
- 全量数据备份：`data`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `md` | 将指定文档导出为 Markdown 文本 | `siyuan export md --id <block-id> [--output <file>] -w <workspace>` | `--id <block-id>`：必填，文档块 ID；`--output <file>`：可选，指定后优先写入该文件；默认输出到 stdout | 只读 workspace；省略 `--output` 时打印 Markdown；指定 `--output` 时写入精确文件路径且不打印内容 | `--dry-run` 只在指定 `--output` 时预览写出目标；省略 `--output` 时即使带 `--dry-run` 也会打印实际导出内容；缺少 `--id` 退出 1 并打印用法；不会自动创建父目录 |
| `html` | 将指定文档导出为 HTML 片段 | `siyuan export html --id <block-id> [--output <file>] -w <workspace>` | `--id <block-id>`：必填，文档块 ID；`--output <file>`：可选，指定后优先写入该文件；默认输出到 stdout | 只读 workspace；省略 `--output` 时打印 HTML；指定 `--output` 时写入精确文件路径且不打印内容 | `--dry-run` 只在指定 `--output` 时预览写出目标；省略 `--output` 时即使带 `--dry-run` 也会打印实际导出内容；缺少 `--id` 退出 1 并打印用法；不会自动创建父目录 |
| `preview` | 将指定文档导出为预览 HTML | `siyuan export preview --id <block-id> [--output <file>] -w <workspace>` | `--id <block-id>`：必填，文档块 ID；`--output <file>`：可选，指定后优先写入该文件；默认输出到 stdout | 只读 workspace；省略 `--output` 时打印预览 HTML；指定 `--output` 时写入精确文件路径且不打印内容 | `--dry-run` 只在指定 `--output` 时预览写出目标；省略 `--output` 时即使带 `--dry-run` 也会打印实际导出内容；缺少 `--id` 退出 1 并打印用法；不会自动创建父目录 |
| `docx` | 将指定文档导出为 Word `.docx` | `siyuan export docx --id <block-id> --output <dir> -w <workspace>` | `--id <block-id>`：必填，文档块 ID；`--output <dir>`：必填，导出目录；导出文件名由文档标题生成 | 只读 workspace；成功时在目标目录下生成 `.docx` 并打印完整生成路径；同名文件会自动避让 | 帮助文本显示 `--output <file>`，但当前行为按目录处理；需要可用的 Pandoc 和导出模板，否则退出 1；缺少 `--id` 或 `--output` 退出 1 并打印用法 |
| `sy` | 将指定文档导出为 `.sy.zip` 包 | `siyuan export sy --id <block-id> [--output <file>] -w <workspace>` | `--id <block-id>`：必填，文档块 ID；`--output <file>`：可选，预期指定后优先复制到该文件；默认打印临时导出路径 | 只读 workspace；省略 `--output` 时打印 `/export/<name>.sy.zip`，实际文件位于当前 workspace 的 `temp/export/` 下 | 临时文件可能在下一次 CLI 初始化时被清理，需要立即使用；当前版本指定 `--output` 会因把 `/export/...` 当成本机路径读取而失败；缺少 `--id` 退出 1 并打印用法 |
| `md-zip` | 将指定文档导出为 Markdown zip 包 | `siyuan export md-zip --id <block-id> [--output <file>] -w <workspace>` | `--id <block-id>`：必填，文档块 ID；`--output <file>`：可选，预期指定后优先复制到该文件；默认打印临时导出路径 | 只读 workspace；省略 `--output` 时打印 `/export/<name>.md.zip`，实际文件位于当前 workspace 的 `temp/export/` 下 | 临时文件可能在下一次 CLI 初始化时被清理，需要立即使用；当前版本指定 `--output` 会因把 `/export/...` 当成本机路径读取而失败；缺少 `--id` 退出 1 并打印用法 |
| `data` | 导出完整 workspace 数据备份 | `siyuan export data [--output <file>] -w <workspace>` | `--output <file>`：可选，预期指定后优先复制到该文件；默认打印临时备份路径 | 读取并压缩当前 `-w <workspace>` 指向的完整 `data`；省略 `--output` 时打印 `/export/<workspace-name>-<timestamp>.zip`，实际文件位于当前 workspace 的 `temp/export/` 下 | 备份可能包含完整工作空间数据，注意明文外泄风险；临时文件可能在下一次 CLI 初始化时被清理；当前版本指定 `--output` 会因把 `/export/...` 当成本机路径读取而失败 |

## 通用注意事项

- `--output` 对 `md`、`html`、`preview` 是精确文件路径；对 `docx` 当前实际是目录；对 `sy`、`md-zip`、`data` 当前存在写出 bug。
- `/export/...` 是 CLI 打印的下载式临时路径，不是可直接传给本机文件 API 的绝对路径；对应文件在 workspace 的 `temp/export/` 中，且不保证跨 CLI 进程保留。
