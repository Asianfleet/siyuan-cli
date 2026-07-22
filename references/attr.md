# attr

`attr` 是块属性读取、批量读取和设置入口。

## 子命令一览

- 查询：`get`、`batch-get`
- 写入：`set`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `get` | 读取单个块的属性 | `siyuan attr get --id <block-id> -w <workspace>` | `--id` | 只读；默认输出 `NAME / VALUE` 表格（至少包含 `id` 和 `updated` 系统属性），`-f json` 时返回属性映射 | 无自定义属性时仍会输出系统属性 `id` 和 `updated` |
| `set` | 设置一个块的一个或多个属性 | `siyuan attr set --id <block-id> --attr <name=value> -w <workspace> --dry-run` | `--id`、`--attr` | 写入 workspace；成功输出 `ok`，`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；`--attr` 可重复，格式必须是 `name=value` |
| `batch-get` | 批量读取多个块的属性 | `siyuan attr batch-get --ids <block-id>,<block-id> -w <workspace>` | `--ids` | 只读；默认按块 ID 分段输出属性表，`-f json` 时返回 `{id: attrs}` 映射 | `--ids` 使用逗号分隔；源码会修剪每个 ID 两端空白 |

## 备注

- 常见属性包括 `icon`、`title-img` 和 `tags`；`title-img` 需要 CSS `background-image:url(...)` 格式，不要只传裸资源路径。
- `set` 会触发文件树刷新；重复传入同名 `--attr` 时后出现的值会覆盖前面的值。
