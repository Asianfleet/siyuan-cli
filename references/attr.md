# attr

`attr` 是块属性读取、批量读取和设置入口。

## 子命令一览

- 查询：`get`、`batch-get`
- 写入：`set`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `get` | 读取单个块的属性 | `siyuan attr get --id <block-id> -w <workspace>` | `--id <block-id>`：必填，块 ID；无有效默认值 | 只读；默认输出按属性名排序的 `NAME / VALUE` 表格，`-f json` 时返回属性映射 | `--dry-run` 仍会真实读取；找不到块或无属性时输出 `(empty)`，不报错 |
| `set` | 设置一个块的一个或多个属性 | `siyuan attr set --id <block-id> --attr <name=value>... -w <workspace> [--dry-run]` | `--id <block-id>`：必填，块 ID；无有效默认值；`--attr <name=value>`：必填，属性键值；可重复，无有效默认值，同名属性以后出现的值为准 | 写入 workspace；成功输出 `ok`，`--dry-run` 输出计划操作；`-f json` 不改变成功输出格式 | `--attr` 必须包含 `=`，否则报 `invalid attr format`；属性名按小写形式校验并以小写保存，只能使用英文字母、数字和连字符并以英文字母开头；空值会删除该属性；真实写入找不到块时报错，`--dry-run` 不校验块是否存在或属性名是否合法 |
| `batch-get` | 批量读取多个块的属性 | `siyuan attr batch-get --ids <block-id>,<block-id> -w <workspace>` | `--ids <block-id>,<block-id>`：必填，逗号分隔的块 ID 列表；无有效默认值，每个 ID 两端空白会被修剪 | 只读；默认按块 ID 分段输出属性表，`-f json` 时返回 `{id: attrs}` 映射 | `--dry-run` 仍会真实读取；表格分段顺序不保证等同 `--ids` 输入顺序；找不到的 ID 会被跳过 |

## 通用注意事项

- 常见属性包括 `icon`、`title-img` 和 `tags`；`title-img` 需要 CSS `background-image:url(...)` 格式，不要只传裸资源路径。
- `set` 会触发文件树刷新；设置 `icon`、`name` 等影响显示文本的属性时，还可能触发界面或引用显示刷新。
