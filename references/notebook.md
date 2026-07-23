# notebook

`notebook` 是笔记本列表、创建、删除、重命名、打开/关闭和图标管理入口。

## 子命令一览

- 查询：`list`
- 生命周期：`create`、`remove`、`rename`、`open`、`close`
- 图标：`set-icon`、`random-icon`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `list` | 列出全部笔记本 | `siyuan notebook list -w <workspace>` | 无 | 默认输出 `ID / NAME / CLOSED / SORT`；`-f json` 返回完整笔记本对象数组 | `--dry-run` 对该查询命令不改变输出；列表会跳过保留名、非节点 ID 目录和不可解析配置的目录 |
| `create` | 创建新笔记本 | `siyuan notebook create --name <name> -w <workspace> [--dry-run]` | `--name <name>`：必填，笔记本名称；无有效默认值 | 写入 workspace；成功输出新笔记本 ID；`--dry-run` 输出拟创建名称 | `--name` 为空直接报错；实际创建会规范化名称，规范化后为空时使用默认名称；名称超过 512 个字符会失败；新笔记本默认 `closed=true`，通常需要先 `open` 再继续操作 |
| `remove` | 删除指定笔记本 | `siyuan notebook remove --id <notebook-id> -w <workspace> [--dry-run]` | `--id <notebook-id>`：必填，笔记本 ID；无有效默认值 | 写入 workspace；成功输出目标 ID；`--dry-run` 输出拟删除 ID | 删除会生成数据历史并移除笔记本目录；`--dry-run` 不校验 ID 是否存在；实际删除不存在的普通 ID 也可能退出成功并打印该 ID |
| `rename` | 重命名笔记本 | `siyuan notebook rename --id <notebook-id> --name <name> -w <workspace> [--dry-run]` | `--id <notebook-id>`：必填，笔记本 ID；无有效默认值；`--name <name>`：必填，新名称；无有效默认值 | 写入 workspace；成功输出笔记本 ID；`--dry-run` 输出拟改名动作 | `--dry-run` 不校验笔记本是否已打开；实际重命名关闭的笔记本会失败并输出用法；名称会规范化，超过 512 个字符会失败 |
| `open` | 打开/挂载笔记本 | `siyuan notebook open --id <notebook-id> -w <workspace> [--dry-run]` | `--id <notebook-id>`：必填，笔记本 ID；无有效默认值 | 修改笔记本挂载状态；成功输出笔记本 ID；`--dry-run` 输出拟打开 ID | `--dry-run` 不校验 ID 是否存在；实际打开不存在的 ID 会失败；重复打开已打开笔记本仍会成功输出 ID；命令会等待约 1 秒并刷新索引队列 |
| `close` | 关闭/卸载笔记本 | `siyuan notebook close --id <notebook-id> -w <workspace> [--dry-run]` | `--id <notebook-id>`：必填，笔记本 ID；无有效默认值 | 修改笔记本挂载状态；成功输出笔记本 ID；`--dry-run` 输出拟关闭 ID | `--dry-run` 不校验 ID 是否存在；实际关闭已关闭或不存在的普通 ID 也可能退出成功并打印该 ID；命令会等待约 1 秒并刷新索引队列 |
| `set-icon` | 设置指定笔记本图标 | `siyuan notebook set-icon --id <notebook-id> --icon <icon> -w <workspace> [--dry-run]` | `--id <notebook-id>`：必填，笔记本 ID；无有效默认值；`--icon <icon>`：必填，图标值；无有效默认值 | 写入 workspace；默认输出 `<id>\t<icon>`；`-f json` 返回 `{id, icon}`；`--dry-run` 输出拟设置动作 | 会先校验笔记本存在，因此 `--dry-run` 对不存在的 ID 也会失败；`--icon` 可用 emoji 码点、emoji 字符、自定义图片路径或动态图标 URL；包含点号的自定义图标名会被过滤 |
| `random-icon` | 随机设置内置 emoji 图标 | `siyuan notebook random-icon [--id <notebook-id>] -w <workspace> [--dry-run]` | `--id <notebook-id>`：可选，笔记本 ID；默认空，省略时处理全部笔记本，传入时只处理该笔记本 | 写入 workspace；默认输出 `ID / NAME / OLD / NEW`；`-f json` 返回变更数组；`--dry-run` 输出拟变更列表 | 省略 `--id` 的实际执行会更新全部笔记本图标；`--dry-run` 会提前选出预览的新图标，但正式执行会重新随机；不存在的 `--id` 会失败；没有可更新笔记本时失败 |

## 通用注意事项

- CLI 会拒绝直接操作加密笔记本；`random-icon` 省略 `--id` 时，如果 workspace 中存在加密笔记本，也会失败。
- 图标命令会刷新文件树；随机图标只从内置 emoji 码点中选择，不使用用户自定义图片。
