# block

`block` 是块级内容的读写、DOM/Kramdown 导出、统计、移动和批量查询入口。

## 子命令一览

- 读取与导出：`get`、`children`、`breadcrumb`、`dom`、`kramdown`、`stat`
- 写入与移动：`insert`、`append`、`prepend`、`update`、`delete`、`move`
- 批量查询：`batch-get`、`batch-kramdown`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `get` | 读取单个块的基础信息 | `siyuan block get --id <block-id> -w <workspace>` | `--id <block-id>`：必填，目标块 ID | 默认输出 `ID`、`Type`、`Name`、`Box`、`HPath`，有内容时输出截断后的 `Content`/`Markdown`；`-f json` 输出完整块对象 | 目标块无法加载时返回错误，例如 `tree not found` |
| `children` | 列出直接子块 | `siyuan block children --id <block-id> -w <workspace>` | `--id <block-id>`：必填，父块 ID | 默认表格输出 `ID / TYPE / CONTENT`；`-f json` 输出直接子块数组 | 只返回直接子块，不递归；目标没有直接子块时输出空表或空数组 |
| `breadcrumb` | 查看块所在路径 | `siyuan block breadcrumb --id <block-id> -w <workspace>` | `--id <block-id>`：必填，目标块 ID | 默认表格输出 `ID / NAME / TYPE`；`-f json` 输出路径节点数组 | 用于定位块所在文档和上层路径；目标无法解析时返回底层错误 |
| `dom` | 导出块的 Block DOM | `siyuan block dom --id <block-id> -w <workspace>` | `--id <block-id>`：必填，目标块 ID | 输出原始 DOM 文本 | 输出不是 JSON；`-f json` 不改变该命令输出格式 |
| `kramdown` | 导出块的 Kramdown/Markdown 表示 | `siyuan block kramdown --id <block-id> [--mode <mode>] -w <workspace>` | `--id <block-id>`：必填，目标块 ID；`--mode <mode>`：可选，导出模式；默认 `md`，常用 `md` 或 `textmark` | 输出原始文本 | 当前实现不单独校验 `--mode` 取值，只把值传给导出逻辑 |
| `stat` | 统计文档树内容 | `siyuan block stat --id <block-id> -w <workspace>` | `--id <block-id>`：必填，目标文档/块 ID | 默认输出字符、词、块、链接、图片和引用数量；`-f json` 输出统计对象 | 统计目标找不到或为空时返回 `document not found or empty` |
| `insert` | 在父块下插入新块，可指定前一个兄弟块 | `siyuan block insert --parent <parent-id> [--previous <block-id>] [--data <markdown> \| --file <path>] -w <workspace> [--dry-run]` | `--parent <parent-id>`：必填，父块 ID；`--previous <block-id>`：可选，前一个兄弟块 ID；`--data <markdown>` / `--file <path>`：可选，新块 Markdown 内容来源，`--data` 优先，其次 `--file`，都不提供时读取 stdin | `--dry-run` 输出将插入的位置；真实写入成功输出 `ok` | 未提供 `--previous` 时会检查父块是否可作为容器；提供 `--previous` 时不会做这项容器检查 |
| `append` | 在父块末尾追加新块 | `siyuan block append --parent <parent-id> [--data <markdown> \| --file <path>] -w <workspace> [--dry-run]` | `--parent <parent-id>`：必填，父块 ID；`--data <markdown>` / `--file <path>`：可选，新块 Markdown 内容来源，`--data` 优先，其次 `--file`，都不提供时读取 stdin | `--dry-run` 输出目标父块；真实写入成功输出 `ok` | 真实写入会检查父块是否可作为容器；不输出新增块 ID |
| `prepend` | 在父块开头插入新块 | `siyuan block prepend --parent <parent-id> [--data <markdown> \| --file <path>] -w <workspace> [--dry-run]` | `--parent <parent-id>`：必填，父块 ID；`--data <markdown>` / `--file <path>`：可选，新块 Markdown 内容来源，`--data` 优先，其次 `--file`，都不提供时读取 stdin | `--dry-run` 输出目标父块；真实写入成功输出 `ok` | 真实写入会检查父块是否可作为容器；不输出新增块 ID |
| `update` | 用新的 Markdown 内容替换目标块 | `siyuan block update --id <block-id> [--data <markdown> \| --file <path>] -w <workspace> [--dry-run]` | `--id <block-id>`：必填，目标块 ID；`--data <markdown>` / `--file <path>`：可选，替换后的 Markdown 内容来源，`--data` 优先，其次 `--file`，都不提供时读取 stdin | `--dry-run` 输出目标块；真实写入成功输出 `ok` | 当前版本更新普通段落会生成新的块 ID，原 ID 随后导出 kramdown 为空；后续操作应重新查询父块确认新 ID |
| `delete` | 删除指定块 | `siyuan block delete --id <block-id> -w <workspace> [--dry-run]` | `--id <block-id>`：必填，目标块 ID | `--dry-run` 输出目标块；真实删除成功输出被删除的块 ID | 会删除目标块及其子树；当前命令本身不做交互确认 |
| `move` | 把块移动到新的父块下，可指定前一个兄弟块 | `siyuan block move --id <block-id> --parent <parent-id> [--previous <block-id>] -w <workspace> [--dry-run]` | `--id <block-id>`：必填，待移动块 ID；`--parent <parent-id>`：必填，目标父块 ID；`--previous <block-id>`：可选，目标位置的前一个兄弟块 ID | `--dry-run` 输出目标位置；真实移动成功输出 `ok` | 未提供 `--previous` 时会检查列表项嵌套和父块容器合法性；提供 `--previous` 时不会做这些检查 |
| `batch-get` | 批量读取多个块对应的文档/root 信息 | `siyuan block batch-get --ids <id1,id2> -w <workspace>` | `--ids <id1,id2>`：必填，逗号分隔；空白项会被忽略 | 默认表格输出 `ID / NAME / ROOTID / REFCOUNT`，未命中 ID 显示 `not found`；`-f json` 输出命中的信息数组 | 返回的是文档/root 信息，不等同于 `get` 的块级详情；`--ids` 为空或只包含空白项时报错 |
| `batch-kramdown` | 批量导出多个块的 Kramdown 文本 | `siyuan block batch-kramdown --ids <id1,id2> -w <workspace>` | `--ids <id1,id2>`：必填，逗号分隔；空白项会被忽略 | 默认按 ID 分段输出文本，未命中 ID 显示 `(not found)`；`-f json` 输出命中的 `{id: kramdown}` 映射 | 当前实现固定使用 `md` 模式，没有 `--mode` 参数；`--ids` 为空或只包含空白项时报错 |

## 通用注意事项

- `--dry-run` 只有写入类命令显式处理；对 `get`、`children`、`breadcrumb`、`dom`、`kramdown`、`stat`、`batch-get`、`batch-kramdown` 仍会正常读取并输出结果。
- 写入类命令的内容来源优先级一致：`--data` 优先，其次 `--file <path>`；`--file -` 从 stdin 读取；两者都不提供时也会从 stdin 读取。`--dry-run` 只打印计划，不读取这些内容。
- 写入类命令成功后通常不输出新块 ID；需要再用 `children`、`kramdown`、`search` 或其他查询命令定位新块。
