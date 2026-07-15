# template

`template` 是模板搜索、读取、渲染预览、删除、保存和创建入口。

## 子命令一览

- 查询与预览：`search`、`get`、`render`
- 写入：`remove`、`save-as`、`create`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `search` | 搜索模板；不传关键字时列出全部模板 | `siyuan template search -w <workspace>` | `[keyword]` | 只读；默认输出 `NAME / PATH` 表格和数量，`-f json` 时返回模板搜索结果数组 | 关键字是可选位置参数，最多传一个 |
| `get` | 读取模板 Markdown 内容 | `siyuan template get --path <path> -w <workspace>` | `--path` | 只读；直接输出模板文件内容 | `--path` 可为绝对路径或相对 `data/templates` 的路径，但不能越过模板目录 |
| `remove` | 删除指定模板文件 | `siyuan template remove --path <path> -w <workspace> --dry-run` | `--path` | 写入 workspace；成功输出 `ok`，`--dry-run` 只打印计划删除路径 | 会修改 workspace，建议先用 `--dry-run`；路径必须解析在 `data/templates` 内 |
| `render` | 基于指定块渲染模板并输出预览 DOM | `siyuan template render --path <path> --id <block-id> -w <workspace>` | `--path`、`--id` | 只读；输出渲染后的 DOM 文本 | `--id` 必填；该命令是预览，不写入目标块 |
| `save-as` | 将文档保存为模板 | `siyuan template save-as --id <doc-id> --name <name> -w <workspace> --dry-run` | `--id`、`--name`、`--overwrite` | 写入 workspace；成功输出 `<name>.md`，`--dry-run` 只打印计划保存操作 | 会修改 workspace，建议先用 `--dry-run`；模板已存在时需加 `--overwrite` |
| `create` | 用 Markdown 内容创建模板 | `siyuan template create --name <name> --data <markdown> -w <workspace> --dry-run` | `--name`、`--data`、`--file`、`--overwrite` | 写入 workspace；成功输出 `<name>.md`，`--dry-run` 只打印计划创建操作 | 会修改 workspace，建议先用 `--dry-run`；内容优先取 `--data`，其次 `--file <path>`，`--file -` 表示 stdin，未提供内容参数时也从 stdin 读取 |

## 备注

- 模板文件位于 workspace 的 `data/templates` 下，相关路径解析会拒绝越界。
- `create` 会过滤并截断模板文件名，实际写入文件名为 `<name>.md`。
- `render` 调用模板渲染预览逻辑，适合确认模板变量在目标块上下文中的展开结果。
