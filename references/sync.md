# sync

`sync` 是执行云端同步、手动上传下载和查看同步状态的入口。

## 子命令一览

- 默认同步：`（默认）`
- 手动方向：`push`、`pull`
- 状态查看：`status`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `（默认）` | 按当前同步配置执行一次云端同步 | `siyuan sync -w <workspace> --dry-run` | 无 | 可能读写 workspace 和云端数据；成功输出 `ok`；`--dry-run` 只打印计划操作 | 会修改 workspace 或云端状态，建议先用 `--dry-run`；全手动模式下必须改用 `push` 或 `pull` |
| `push` | 将本地数据上传到云端 | `siyuan sync push -w <workspace> --dry-run` | 无 | 可能写入云端同步数据；成功输出 `ok`；`--dry-run` 只打印计划操作 | 会修改云端状态，建议先用 `--dry-run`；方向是本地到云端 |
| `pull` | 从云端下载数据到本地 | `siyuan sync pull -w <workspace> --dry-run` | 无 | 可能写入本地 workspace；成功输出 `ok`；`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；方向是云端到本地 |
| `status` | 查看当前同步配置和状态 | `siyuan sync status -w <workspace>` | 无 | 只读；默认输出 enabled、mode、provider、cloud、interval 等状态字段 | 可用于同步前确认模式；`mode` 显示为 `auto`、`manual`、`full-manual` 或 `unknown(n)` |

## 备注

- `sync` 读取当前 workspace 的同步配置；云端相关命令需要已配置可用的同步服务。
- `sync status` 只查看状态，不触发同步。
