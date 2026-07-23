# database

`database` 是属性视图数据库的搜索、读取、渲染、字段、行和未使用数据库清理入口。属性视图 ID 在命令参数中写作 `--av`。

## 子命令一览

- 数据库读取：`search`、`get`、`render`、`keys`
- 字段管理：`key add`、`key remove`
- 未使用数据库：`unused`、`clean`
- 行管理：`item add`、`item remove`、`item update`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `search` | 按名称关键词搜索数据库 | `siyuan database search <keyword> -w <workspace>` | `<keyword>`：必填，搜索词；只使用第一个位置参数 | 只读；默认输出 `ID / NAME / HPATH` 表格，`-f json` 时返回搜索结果数组 | 未命中时输出 `No results found.`；未使用数据库可能不会被名称搜索命中，可用 `unused` 查看 |
| `get` | 读取指定数据库完整内容 | `siyuan database get --av <av-id> -w <workspace>` | `--av <av-id>`：必填，属性视图 ID | 只读；默认输出数据库摘要和表格，`-f json` 时返回属性视图对象 | 数据库不存在时报 `database not found: <av-id>` |
| `render` | 按视图、查询和分页渲染数据库数据 | `siyuan database render --av <av-id> [--view <view-id>] [--query <query>] [--page <n>] [--size <n>] -w <workspace>` | `--av <av-id>`：必填，属性视图 ID；`--view <view-id>`：可选，指定视图 ID；默认当前视图；`--query <query>`：可选，在视图内搜索；默认空；`--page <n>`：可选，页码；默认 `1`；`--size <n>`：可选，每页数量；默认 `50` | 只读；默认输出渲染后的表格，`-f json` 时返回渲染后的属性视图对象 | `--page` 小于 `1` 时按 `1` 处理，`--size` 小于 `1` 时按 `50` 处理；数据库不存在或渲染失败会报错 |
| `keys` | 列出数据库字段 | `siyuan database keys --av <av-id> -w <workspace>` | `--av <av-id>`：必填，属性视图 ID | 只读；默认输出 `ID / NAME / TYPE / ICON` 表格，`-f json` 时返回字段数组 | 数据库不存在时报 `database not found: <av-id>` |
| `key add` | 向数据库添加字段 | `siyuan database key add --av <av-id> --name <name> --type <type> [--icon <icon>] [--prev <key-id>] -w <workspace> [--dry-run]` | `--av <av-id>`：必填，目标属性视图 ID；`--name <name>`：必填，新字段名；`--type <type>`：必填，字段类型，支持 `block/text/number/date/select/mSelect/url/email/phone/mAsset/template/created/updated/checkbox/relation/rollup/lineNumber`；`--icon <icon>`：可选，字段图标；默认空；`--prev <key-id>`：可选，前一个字段 ID；默认空 | 写入 workspace；成功输出新字段 ID，`--dry-run` 输出计划添加字段 | `--dry-run` 不校验数据库是否存在，也不校验字段类型是否可用 |
| `key remove` | 从数据库移除字段 | `siyuan database key remove --av <av-id> --key <key-id> [--remove-relation-dest] -w <workspace> [--dry-run]` | `--av <av-id>`：必填，目标属性视图 ID；`--key <key-id>`：必填，要移除的字段 ID；`--remove-relation-dest`：可选，同时移除关联目标数据；默认 `false` | 写入 workspace；成功输出 `ok`，`--dry-run` 输出计划移除字段 | 主字段不能移除；`--remove-relation-dest` 会扩大关联数据删除范围，使用前先确认目标字段类型和关联关系 |
| `unused` | 列出未使用数据库 | `siyuan database unused -w <workspace>` | 无 | 只读；默认输出 `ITEM / NAME` 表格和数量，`-f json` 时返回未使用数据库数组 | 无未使用数据库时输出 `No unused databases found.` |
| `clean` | 清理未使用数据库，可指定单个数据库 ID | `siyuan database clean [--av <av-id>] -w <workspace> [--dry-run]` | `--av <av-id>`：可选，指定单个未使用数据库 ID；默认空，省略时清理全部未使用数据库 | 写入 workspace；指定 `--av` 时回显该 ID，未指定时输出清理数量；`--dry-run` 输出计划清理动作 | 不带 `--av` 风险较高，会清理全部未使用数据库；指定 `--av` 的输出不等同于读取确认，清理后可用 `get` 验证是否已不存在 |
| `item add` | 向数据库添加行 | `siyuan database item add --av <av-id> [--block <block-id>] [--content <text>] [--view <view-id>] [--group <group-id>] [--previous <item-id>] [--detached] [--ignore-default-fill] -w <workspace> [--dry-run]` | `--av <av-id>`：必填，目标属性视图 ID；`--block <block-id>`：条件必填，绑定块 ID；未传 `--detached` 时必须指定；`--content <text>`：可选，主字段文本；默认空；`--view <view-id>`：可选，目标视图 ID；默认空；`--group <group-id>`：可选，目标分组 ID；默认空；`--previous <item-id>`：可选，前一个行 ID；默认空；`--detached`：可选，创建不绑定块的行；默认 `false`；`--ignore-default-fill`：可选，忽略默认填充值；默认 `false` | 写入 workspace；成功输出 `ok`，`--dry-run` 输出计划添加行 | 帮助文本写着 `--block` 可自动生成，但实际非 detached 行缺少 `--block` 会报错；添加成功不输出新行 ID，需要再用 `get` 或 `render -f json` 定位 |
| `item remove` | 从数据库移除一批行 | `siyuan database item remove --av <av-id> --ids <item-id,item-id> -w <workspace> [--dry-run]` | `--av <av-id>`：必填，目标属性视图 ID；`--ids <item-id,item-id>`：必填，逗号分隔的行 ID 列表；空白会被忽略 | 写入 workspace；成功输出 `ok`，`--dry-run` 输出计划移除行数 | `--dry-run` 只按逗号拆分并统计数量，不确认行是否存在；移除绑定块行会影响块上的数据库绑定 |
| `item update` | 更新数据库单元格值 | `siyuan database item update --av <av-id> --key <key-id> --item <item-id> --value <json> -w <workspace> [--dry-run]` | `--av <av-id>`：必填，目标属性视图 ID；`--key <key-id>`：必填，字段 ID；`--item <item-id>`：必填，行 ID；`--value <json>`：必填，单元格值 JSON 对象，结构必须匹配字段类型，例如文本字段可用 `{"type":"text","text":{"content":"..."}}` | 写入 workspace；成功输出 `ok`，`--dry-run` 输出计划更新单元格 | `--value` 在 dry-run 前也会解析，非法 JSON 会报 `invalid JSON: ...` |

## 通用注意事项

- 写入类命令会修改 workspace；先用 `--dry-run` 只能确认部分必填参数和计划文本，不能当作完整存在性校验或权限校验。
- 只读命令带 `--dry-run` 时输出与普通只读执行一致。
