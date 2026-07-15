# export

`export` 是从 workspace 导出文档、预览内容、压缩包和完整数据备份的入口。

## 子命令一览

- 单文档导出：`md`、`html`、`preview`、`docx`
- 压缩包导出：`sy`、`md-zip`
- 全量数据导出：`data`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `md` | 将指定文档导出为 Markdown 文本 | `siyuan export md --id <block-id> -w <workspace>` | `--id`、`--output` | 只读 workspace；未指定 `--output` 时输出 Markdown 到 stdout，指定后写入目标文件 | `--id` 必填；指定 `--output` 时支持 `--dry-run` 预览写出目标 |
| `html` | 将指定文档导出为 HTML | `siyuan export html --id <block-id> -w <workspace>` | `--id`、`--output` | 只读 workspace；未指定 `--output` 时输出 HTML 到 stdout，指定后写入目标文件 | `--id` 必填；指定 `--output` 时支持 `--dry-run` 预览写出目标 |
| `preview` | 将指定文档导出为预览 HTML | `siyuan export preview --id <block-id> -w <workspace>` | `--id`、`--output` | 只读 workspace；未指定 `--output` 时输出预览 HTML 到 stdout，指定后写入目标文件 | `--id` 必填；指定 `--output` 时支持 `--dry-run` 预览写出目标 |
| `docx` | 将指定文档导出为 Word `.docx` 文件 | `siyuan export docx --id <block-id> --output <file> -w <workspace>` | `--id`、`--output` | 只读 workspace；写出 `.docx` 文件并输出生成路径 | `--output` 必填；支持 `--dry-run` 预览写出目标 |
| `sy` | 将指定文档导出为 `.sy.zip` 包 | `siyuan export sy --id <block-id> -w <workspace>` | `--id`、`--output` | 只读 workspace；未指定 `--output` 时输出临时 zip 路径，指定后复制到目标文件 | `--id` 必填；支持 `--dry-run`，未指定 `--output` 时预览导出到临时路径 |
| `md-zip` | 将指定文档导出为 Markdown zip 包 | `siyuan export md-zip --id <block-id> -w <workspace>` | `--id`、`--output` | 只读 workspace；未指定 `--output` 时输出临时 zip 路径，指定后复制到目标文件 | `--id` 必填；支持 `--dry-run`，未指定 `--output` 时预览导出到临时路径 |
| `data` | 导出完整 workspace 数据备份 | `siyuan export data -w <workspace>` | `--output` | 只读 workspace；未指定 `--output` 时输出临时备份 zip 路径，指定后复制到目标文件 | 支持 `--dry-run`；备份范围是当前 `-w <workspace>` 指向的完整数据 |

## 备注

- 本页基于 SiYuan v3.7.2-alpha.2 的 `kernel/cli/cmd/export.go`。
- `export` 类命令读取 workspace 数据；带 `--output` 时会在 workspace 外写出文件，目标文件已存在时按系统写文件行为覆盖。
- `docx` 是本组唯一强制要求 `--output` 的导出子命令。
