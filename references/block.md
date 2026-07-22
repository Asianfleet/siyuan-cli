# block

`block` 是块级内容的读写、DOM/Kramdown 导出、统计、移动和批量查询入口。

## 子命令一览

- 读取与导出：`get`、`children`、`breadcrumb`、`dom`、`kramdown`、`stat`
- 写入与移动：`insert`、`append`、`prepend`、`update`、`delete`、`move`
- 批量查询：`batch-get`、`batch-kramdown`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `get` | 读取单个块的基础信息 | `siyuan block get --id <block-id> -w <workspace>` | `--id` | 只读；`-f json` 时返回结构化块信息，默认表格/文本摘要 | 适合先确认块类型、所属笔记本和内容摘要；`--id` 为目标块 ID |
| `children` | 列出某个块的直接子块 | `siyuan block children --id <block-id> -w <workspace>` | `--id` | 只读；`-f json` 时返回子块数组，默认输出 `ID / TYPE / CONTENT` | 只返回直接子块，不递归展开整棵树；`--id` 为父块 ID |
| `breadcrumb` | 查看块所在路径的面包屑 | `siyuan block breadcrumb --id <block-id> -w <workspace>` | `--id` | 只读；`-f json` 时返回路径节点数组，默认输出 `ID / NAME / TYPE` | 适合定位块所在文档与上层结构；`--id` 为目标块 ID |
| `dom` | 导出块的 Block DOM | `siyuan block dom --id <block-id> -w <workspace>` | `--id` | 只读；返回原始 DOM 文本 | 输出不是 JSON，适合调试渲染或结构转换 |
| `kramdown` | 导出块的 Kramdown/Markdown 表示 | `siyuan block kramdown --id <block-id> -w <workspace>` | `--id`、`--mode md/textmark` | 只读；返回文本，默认 `--mode md` | `--mode` 仅支持 `md` 和 `textmark`；`--id` 为目标块 ID |
| `stat` | 统计文档树的字符、单词、块、链接等数量 | `siyuan block stat --id <block-id> -w <workspace>` | `--id` | 只读；`-f json` 时返回统计对象，默认输出多行文本统计 | 目标通常应是文档或可形成树的块；不存在或空文档会报错；`--id` 为目标文档/块 ID |
| `insert` | 在父块下插入新块，可选指定前一个兄弟块 | `siyuan block insert --parent <parent-id> --data "..." -w <workspace> --dry-run` | `--parent`、`--previous`、`--data`、`--file` | 写入 workspace；成功输出 `ok`，`--dry-run` 只打印计划操作；未提供 `--data` 或 `--file` 时从 stdin 读取 | 会修改 workspace，建议先用 `--dry-run`；仅用 `--parent` 定位时，父块必须是合法容器块；`--previous` 可指定前一个兄弟块 |
| `append` | 在父块末尾追加新块 | `siyuan block append --parent <parent-id> --data "新块内容" -w <workspace> --dry-run` | `--parent`、`--data`、`--file` | 写入 workspace；成功输出 `ok`，`--dry-run` 只打印计划操作；未提供 `--data` 或 `--file` 时从 stdin 读取 | 会修改 workspace，建议先用 `--dry-run`；父块必须是合法容器块；`--file -` 表示从 stdin 读取 |
| `prepend` | 在父块开头插入新块 | `siyuan block prepend --parent <parent-id> --data "..." -w <workspace> --dry-run` | `--parent`、`--data`、`--file` | 写入 workspace；成功输出 `ok`，`--dry-run` 只打印计划操作；未提供 `--data` 或 `--file` 时从 stdin 读取 | 会修改 workspace，建议先用 `--dry-run`；父块必须是合法容器块 |
| `update` | 用新的 Markdown 内容覆盖现有块 | `siyuan block update --id <block-id> --data "更新后的内容" -w <workspace> --dry-run` | `--id`、`--data`、`--file` | 写入 workspace；成功输出 `ok`，`--dry-run` 只打印计划操作；未提供 `--data` 或 `--file` 时从 stdin 读取 | 会修改 workspace，建议先用 `--dry-run`；`--file -` 表示从 stdin 读取 |
| `delete` | 删除指定块 | `siyuan block delete --id <block-id> -w <workspace> --dry-run` | `--id` | 写入 workspace；成功时输出被删除的块 ID，`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；执行后目标块及其树结构可能被移除 |
| `move` | 把块移动到新的父块下，可选指定前一个兄弟块 | `siyuan block move --id <block-id> --parent <parent-id> -w <workspace> --dry-run` | `--id`、`--parent`、`--previous` | 写入 workspace；成功输出 `ok`，`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；仅用 `--parent` 定位时会检查列表项嵌套和容器块合法性；`--previous` 可指定前一个兄弟块 |
| `batch-get` | 批量读取多个块对应的文档/root 信息 | `siyuan block batch-get --ids <block-id>,<block-id> -w <workspace>` | `--ids` | 只读；`-f json` 时只返回命中的信息数组，未命中 ID 会被省略；默认表格输出 `ID / NAME / ROOTID / REFCOUNT`，未命中 ID 会标成 `not found` | `--ids` 使用逗号分隔，空白项会被忽略；返回内容更接近文档信息，不等同于 `get` 的块级详情 |
| `batch-kramdown` | 批量导出多个块的 Kramdown 文本 | `siyuan block batch-kramdown --ids <block-id>,<block-id> -w <workspace>` | `--ids` | 只读；`-f json` 时返回 `{id: kramdown}` 映射，默认逐块输出文本片段 | 当前实现固定导出 `md` 模式，不提供 `--mode` 选项 |

## 备注

- `block` 继承全局参数，常用的是 `-w, --workspace`、`-f, --format` 和 `--dry-run`。
- 写入类命令的内容来源规则一致：优先 `--data`，其次 `--file <path>`，`--file -` 表示从 stdin 读取；如果两者都不提供，则直接从 stdin 读取。
