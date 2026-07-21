# history

`history` 是查询历史记录、读取历史文件内容、回滚历史版本和清空历史的入口。

## 子命令一览

- 历史查询：`list`、`search`
- 历史内容读取：`get`
- 历史写入操作：`rollback`、`clear`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `list` | 分页列出全部历史记录 | `siyuan history list -w <workspace>` | `--notebook`、`--op`、`--type`、`--page` | 只读；默认按时间戳分组输出历史条目和分页信息 | `--type` 默认 `1`，含义为文档内容；`--op` 可筛选 `delete`、`update`、`create` |
| `search` | 按关键词搜索历史记录 | `siyuan history search <query> -w <workspace>` | `<query>`、`--notebook`、`--op`、`--type`、`--page` | 只读；默认按时间戳分组输出命中的历史条目和分页信息 | `<query>` 是位置参数且至少 1 个；筛选参数与 `list` 一致 |
| `get` | 读取指定历史文件内容 | `siyuan history get --path <history-path> -w <workspace>` | `--path` | 只读；输出历史文件内容到 stdout | `--path` 必填，应来自历史查询结果中的历史文件路径 |
| `rollback` | 将文档回滚到指定历史版本 | `siyuan history rollback --path <history-path> -w <workspace> --dry-run` | `--path` | 写入 workspace；成功输出 `ok`，并触发文档和文件树刷新；`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；仅回滚 `--path` 指向的历史版本 |
| `clear` | 清空全部 workspace 历史记录 | `siyuan history clear -w <workspace> --dry-run` | 无 | 写入 workspace；成功输出 `ok`；`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；这是全量清理历史，高风险 |

## 备注

- `--page` 小于 1 时按第 1 页处理。
- `--type` 取值由源码说明为 `0=doc-name`、`1=doc-content`、`2=asset`、`3=doc-id`、`4=database`。
