# inbox

`inbox` 用于读取云端收集箱剪藏，并把剪藏转换为本地文档。收集箱数据来自思源云端，运行时需要当前 workspace 中有可用账号和相应云端能力。

## 子命令一览

- 云端读取：`list`、`get`
- 转换写入：`convert`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `list` | 分页列出云端收集箱剪藏摘要 | `siyuan inbox list -w <workspace>` | `--page` / `-p`：页码，默认 `1`；小于 `1` 时按 `1` 处理 | 只读；`table` 输出 `ID / TITLE / URL / CREATED` 和当前页条数；`json` 输出当前页剪藏数组 | 需要可用云端账号；未登录或云端服务不可用时，命令可能 panic 而不是返回普通错误；`--dry-run` 不会跳过云端读取 |
| `get` | 读取单条剪藏详情和完整 Markdown 正文 | `siyuan inbox get --id <shorthand-id> -w <workspace>` | `--id`：剪藏 ID，必填 | 只读；`table` 输出 ID、标题、创建时间、来源 URL 和 Markdown；`json` 输出剪藏对象 | 未传 `--id` 返回 `--id is required`；需要可用云端账号；未登录或云端服务不可用时，命令可能 panic；`--dry-run` 不会跳过云端读取 |
| `convert` | 将一条或多条剪藏转换为本地文档 | `siyuan inbox convert --ids <id1,id2> --notebook <notebook-id> -w <workspace> --dry-run` | `--ids`：逗号分隔剪藏 ID，必填，去空白并去重；`--notebook`：目标笔记本 ID，必填；`--path`：目标笔记本内路径，默认 `/`；`--remove-after`：转换成功后删除云端原件，默认 `true` | 写入 workspace；`table` 输出每条剪藏的文档 ID 和状态；`json` 输出结果数组；`--dry-run` 只打印转换计划 | 实际转换会创建本地文档；默认会删除成功转换的云端原件，不想删除时传 `--remove-after=false`；未传 `--notebook` 返回 `--notebook is required`，未传有效 `--ids` 返回 `--ids is required (comma-separated shorthand IDs)`；`--path` 的父路径必须存在，即使 `--dry-run` 也会先校验父路径；未登录或云端服务不可用时，非 dry-run 转换可能 panic |

## 通用注意事项

- `inbox` 依赖云端账号状态；未登录或云端服务不可用时，`list`、`get` 和非 dry-run `convert` 可能 panic，而不是返回普通错误。
- `--dry-run` 不会跳过 `list` 和 `get` 的云端读取；`convert --dry-run` 只生成转换计划，不读取剪藏正文，也不会删除云端原件。
- `convert` 会对 `--ids` 去空白、去重；空列表会报 `--ids is required (comma-separated shorthand IDs)`。
- `convert --path` 的父路径必须存在；父路径不存在时即使带 `--dry-run` 也会报 `parent path not found: ...`。
