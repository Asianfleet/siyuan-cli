# notebook

`notebook` 是笔记本列表、创建、删除、重命名、打开/关闭和图标管理入口。

## 子命令一览

- 查询：`list`
- 生命周期：`create`、`remove`、`rename`、`open`、`close`
- 图标：`set-icon`、`random-icon`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `list` | 列出全部笔记本 | `siyuan notebook list -w <workspace>` | 无 | 只读；默认输出 `ID / NAME / CLOSED / SORT`，`-f json` 时返回笔记本数组 | 可用来确认笔记本 ID、关闭状态和排序值 |
| `create` | 创建新笔记本 | `siyuan notebook create --name <name> -w <workspace> --dry-run` | `--name` | 写入 workspace；成功输出新笔记本 ID，`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；`--name` 为空会报错 |
| `remove` | 删除指定笔记本 | `siyuan notebook remove --id <notebook-id> -w <workspace> --dry-run` | `--id` | 写入 workspace；成功输出被删除的笔记本 ID，`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；删除前应确认目标笔记本 ID |
| `rename` | 重命名笔记本 | `siyuan notebook rename --id <notebook-id> --name <name> -w <workspace> --dry-run` | `--id`、`--name` | 写入 workspace；成功输出笔记本 ID，`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；`--id` 或 `--name` 为空会报错 |
| `open` | 打开/挂载笔记本 | `siyuan notebook open --id <notebook-id> -w <workspace> --dry-run` | `--id` | 修改 workspace 的笔记本挂载状态；成功输出笔记本 ID，`--dry-run` 只打印计划操作 | 会修改 workspace 状态，建议先用 `--dry-run`；目标笔记本不存在或挂载失败会报错 |
| `close` | 关闭/卸载笔记本 | `siyuan notebook close --id <notebook-id> -w <workspace> --dry-run` | `--id` | 修改 workspace 的笔记本挂载状态；成功输出笔记本 ID，`--dry-run` 只打印计划操作 | 会修改 workspace 状态，建议先用 `--dry-run`；关闭后该笔记本内容不可通过已挂载树直接访问 |
| `set-icon` | 设置指定笔记本图标 | `siyuan notebook set-icon --id <notebook-id> --icon <icon> -w <workspace> --dry-run` | `--id`、`--icon` | 写入 workspace；默认输出 `<id>\t<icon>`，`-f json` 时返回 `{id, icon}`，`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；`--icon` 可用 emoji hex 码点、emoji 字符、自定义图片路径或动态图标 URL |
| `random-icon` | 为一个或全部笔记本随机设置内置 emoji 图标 | `siyuan notebook random-icon -w <workspace> --dry-run` | `--id` 可选 | 写入 workspace；默认输出 `ID / NAME / OLD / NEW`，`-f json` 时返回变更数组，`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；不传 `--id` 会更新全部笔记本图标 |

## 备注

- 图标相关命令会触发文件树刷新；随机图标只从内置 emoji 码点中选择，不使用用户自定义图片。
- `open` 和 `close` 会等待约 1 秒并刷新队列，适合脚本中预留后续读取的时序空间。
