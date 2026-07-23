# history

`history` 是查询历史记录、读取历史文件内容、回滚历史版本和清空历史的入口。

## 子命令一览

- 历史查询：`list`、`search`
- 历史内容读取：`get`
- 历史写入操作：`rollback`、`clear`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `list` | 分页列出历史记录 | `siyuan history list -w <workspace>` | `--notebook <id>`：可选，仅对文档类历史有效，默认不过滤；`--op <op>`：可选，默认不过滤，`all` 等同不过滤；`--type, -t <0-4>`：可选，默认 `1`；`--page, -p <n>`：可选，默认 `1` | 只读；表格输出按时间戳分组列出操作、标题、历史路径和分页信息；`-f json` 输出 `timestamps`、`pageCount`、`totalCount` | 没有结果时输出 `No history found.`；`--page` 小于 `1` 时按第 `1` 页查询；`--op` 不校验取值，不匹配时只会得到空结果 |
| `search` | 按关键词搜索历史记录 | `siyuan history search <query> -w <workspace>` | `<query>`：必填，只使用第 1 个位置参数；`--notebook <id>`：可选，仅对文档名称/内容搜索有效；`--op <op>`：可选，默认不过滤，`all` 等同不过滤；`--type, -t <0-4>`：可选，默认 `1`；`--page, -p <n>`：可选，默认 `1` | 只读；输出格式与 `list` 一致，但只返回命中查询词的历史记录 | 查询词含空格时必须加引号；没有位置参数会报错；没有命中时输出 `No history found.` |
| `get` | 读取指定历史文档内容 | `siyuan history get --path <history-path> -w <workspace>` | `--path <history-path>`：必填，优先使用 `list` 或 `search` 返回的 `/history/.../*.sy` 路径 | 只读；输出历史文档渲染后的 DOM 内容到 stdout | `--dry-run` 对该只读命令没有可观察差异；`--path` 为空会报错；路径逃逸会报错；路径在 workspace 内但文件不存在时空输出并以成功状态退出 |
| `rollback` | 将文档回滚到指定历史版本 | `siyuan history rollback --path <history-path> -w <workspace> --dry-run` | `--path <history-path>`：必填，优先使用 `list` 或 `search` 返回的 `/history/.../*.sy` 路径 | 写入 workspace；`--dry-run` 输出将回滚的历史路径；正式执行成功输出 `ok` | 高风险写入操作，应先 dry-run；只支持可读取的历史文档路径；路径不存在、路径逃逸或不在历史目录下会报错 |
| `clear` | 清空 workspace 的全部历史记录 | `siyuan history clear -w <workspace> --dry-run` | 无 | 写入 workspace；`--dry-run` 输出将清空全部历史；正式执行成功输出 `ok` | 全量删除历史，无法按笔记本、时间、类型或操作筛选；正式执行前应确认已备份或不再需要历史 |



- `--type` 取值：`0=doc-name`、`1=doc-content`、`2=asset`、`3=doc-id`、`4=database`。
- `--type 3` 使用精确文档 ID 查询；其他类型使用全文查询语义。
- 常见历史操作值包括 `update`、`delete`、`clean`、`format`、`sync`、`replace`、`outline`；帮助文本提到的其他操作值如果没有对应历史记录，会表现为空结果。
