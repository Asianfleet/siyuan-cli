# database

`database` 是属性视图数据库的搜索、读取、渲染、字段、行和未使用数据库清理入口。

## 子命令一览

- 数据库读取：`search`、`get`、`render`、`keys`
- 字段管理：`key add`、`key remove`
- 未使用数据库：`unused`、`clean`
- 行管理：`item add`、`item remove`、`item update`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `search` | 按名称关键词搜索数据库 | `siyuan database search <keyword> -w <workspace>` | `<keyword>` | 只读；默认输出 `ID / NAME / HPATH` 表格，`-f json` 时返回搜索结果数组 | 至少传入一个关键词参数 |
| `get` | 读取指定数据库完整内容 | `siyuan database get --av <av-id> -w <workspace>` | `--av` | 只读；默认输出数据库摘要和表格，`-f json` 时返回属性视图对象 | `--av` 是 attribute view ID；不存在会报错 |
| `render` | 按视图、查询和分页渲染数据库数据 | `siyuan database render --av <av-id> -w <workspace>` | `--av`、`--view`、`--query`、`--page`、`--size` | 只读；默认输出渲染后的表格，`-f json` 时返回渲染后的属性视图对象 | `--page` 小于 1 时按 1 处理，`--size` 小于 1 时按 50 处理 |
| `keys` | 列出数据库字段 | `siyuan database keys --av <av-id> -w <workspace>` | `--av` | 只读；默认输出 `ID / NAME / TYPE / ICON` 表格，`-f json` 时返回字段数组 | 数据库不存在会报错 |
| `key add` | 向数据库添加字段 | `siyuan database key add --av <av-id> --name <name> --type <type> -w <workspace> --dry-run` | `--av`、`--name`、`--type`、`--icon`、`--prev` | 写入 workspace；成功输出新字段 ID，`--dry-run` 只打印计划添加字段 | 会修改 workspace，建议先用 `--dry-run`；`--type` 支持 `block/text/number/date/select/mSelect/url/email/phone/mAsset/template/created/updated/checkbox/relation/rollup/lineNumber` |
| `key remove` | 从数据库移除字段 | `siyuan database key remove --av <av-id> --key <key-id> -w <workspace> --dry-run` | `--av`、`--key`、`--remove-relation-dest` | 写入 workspace；成功输出 `ok`，`--dry-run` 只打印计划移除字段 | 会修改 workspace，建议先用 `--dry-run`；`--remove-relation-dest` 会同时移除关联数据库中的相关数据 |
| `unused` | 列出未使用数据库 | `siyuan database unused -w <workspace>` | 无 | 只读；默认输出 `ITEM / NAME` 表格和数量，`-f json` 时返回未使用数据库数组 | 扫描结果来自当前 workspace 的属性视图引用状态 |
| `clean` | 清理未使用数据库，可指定单个数据库 ID | `siyuan database clean --av <av-id> -w <workspace> --dry-run` | `--av` | 写入 workspace；指定 `--av` 时输出该 ID，未指定时输出清理数量；`--dry-run` 只打印计划清理动作 | 会修改 workspace，建议先用 `--dry-run`；不带 `--av` 会清理全部未使用数据库 |
| `item add` | 向数据库添加行 | `siyuan database item add --av <av-id> --block <block-id> -w <workspace> --dry-run` | `--av`、`--block`、`--content`、`--view`、`--group`、`--previous`、`--detached`、`--ignore-default-fill` | 写入 workspace；成功输出 `ok`，`--dry-run` 只打印计划添加行 | 会修改 workspace，建议先用 `--dry-run`；非 detached 行必须提供 `--block`，使用 `--detached` 时可创建不绑定块的行 |
| `item remove` | 从数据库移除一批行 | `siyuan database item remove --av <av-id> --ids <item-id,item-id> -w <workspace> --dry-run` | `--av`、`--ids` | 写入 workspace；成功输出 `ok`，`--dry-run` 只打印计划移除数量 | 会修改 workspace，建议先用 `--dry-run`；`--ids` 使用逗号分隔，源码会 trim 空白 |
| `item update` | 更新数据库单元格值 | `siyuan database item update --av <av-id> --key <key-id> --item <item-id> --value <json> -w <workspace> --dry-run` | `--av`、`--key`、`--item`、`--value` | 写入 workspace；成功输出 `ok`，`--dry-run` 只打印计划更新单元格 | 会修改 workspace，建议先用 `--dry-run`；`--value` 必须是可解析为对象的 JSON 字符串 |

## 备注

- `database` 对应思源的 attribute view；文档中的 `--av` 均指属性视图 ID。
- 写入类命令会在成功后触发属性视图刷新入口，并在 CLI 退出前刷新 SQL 队列。
