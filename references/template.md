# template

`template` 是模板搜索、读取、渲染预览、删除、保存和创建入口。

## 子命令一览

- 查询与预览：`search`、`get`、`render`
- 写入：`remove`、`save-as`、`create`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `search` | 搜索模板；不传关键字时列出全部模板 | `siyuan template search [keyword] -w <workspace>` | `[keyword]`：可选位置参数，默认空字符串，最多一个 | 只读；默认输出 `NAME / PATH` 表格和数量，未命中输出 `No templates found.`；`-f json` 输出包含 `path`、`relativePath`、`content` 的数组 | `--dry-run` 不改变行为；关键字命中内容会带 `<mark>` 标记 |
| `get` | 读取模板 Markdown 内容 | `siyuan template get --path <path> -w <workspace>` | `--path`：必填；模板绝对路径，或相对 `data/templates` 的路径 | 只读；直接输出模板文件内容 | 路径必须解析在 `data/templates` 内；缺少 `--path`、路径越界或文件不存在会报错；`--dry-run` 不改变行为 |
| `remove` | 删除指定模板文件 | `siyuan template remove --path <path> -w <workspace> --dry-run` | `--path`：必填；模板绝对路径，或相对 `data/templates` 的路径 | 写入 workspace；成功输出 `ok`；`--dry-run` 输出计划删除的原始路径 | 路径必须解析在 `data/templates` 内；`--dry-run` 不检查文件是否存在；不带 `--dry-run` 会直接删除目标文件 |
| `render` | 基于指定块渲染模板并输出预览 DOM | `siyuan template render --path <path> --id <block-id> -w <workspace>` | `--path`：必填；模板绝对路径，或相对 `data/templates` 的路径。`--id`：必填，目标块 ID | 只读；输出渲染后的 DOM 文本；`--dry-run` 不改变行为 | 目标块必须能加载块树；模板解析、模板执行、缺少参数、路径越界或文件不存在会报错 |
| `save-as` | 将文档保存为模板 | `siyuan template save-as --id <doc-id> --name <name> -w <workspace> --dry-run` | `--id`：必填，源文档块 ID。`--name`：必填，不含扩展名。`--overwrite`：可选，默认 `false` | 写入 workspace；成功输出 `<name>.md`；`--dry-run` 只输出计划保存操作 | 模板名会过滤非法文件名字符并截断；目标已存在且未加 `--overwrite` 会报错；已知 bug：源 ID 不能加载块树时可能输出成功但不创建模板 |
| `create` | 用 Markdown 内容创建模板 | `siyuan template create --name <name> --data <markdown> -w <workspace> --dry-run` | `--name`：必填，不含扩展名。`--data`：可选 Markdown 内容。`--file`：可选文件路径，`-` 表示 stdin。`--overwrite`：可选，默认 `false`。内容优先级：`--data` > `--file` > stdin | 写入 workspace；成功输出 `<name>.md`；`--dry-run` 读取内容来源后输出计划创建操作 | 模板名会过滤非法文件名字符并截断；目标已存在且未加 `--overwrite` 会报错；`--file` 不存在时即使 `--dry-run` 也会报错；不传 `--data` 和 `--file` 时会读取 stdin |



- 模板文件位于 workspace 的 `data/templates` 下；读取、删除和渲染使用的路径必须留在该目录内。
- `--dry-run` 对 `search`、`get`、`render` 没有额外预览语义；`remove` 只做路径解析，`create` 会先读取内容来源，`save-as` 不校验目标是否已存在。
- `create` 和 `save-as` 的实际文件名由模板名过滤、截断后追加 `.md` 得到；命令输出仍按输入的 `name` 打印 `<name>.md`。
