# sync

`sync` 是执行云端同步、手动上传下载和查看同步状态的入口。

## 子命令一览

- 默认同步：不带子命令
- 手动方向：`push`、`pull`
- 状态查看：`status`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `（默认）` | 按当前同步配置执行一次云端同步 | `siyuan sync -w <workspace> --dry-run` | 无 | `--dry-run` 输出 `[dry-run] Would sync data with cloud`；正式执行会尝试按配置同步并输出 `ok` | 可能读写 workspace 和云端数据；全手动模式下即使带 `--dry-run` 也会报错，必须改用 `push` 或 `pull`；同步关闭时正式执行仍输出 `ok`，不代表实际同步发生 |
| `push` | 将本地数据上传到云端 | `siyuan sync push -w <workspace> --dry-run` | 无 | `--dry-run` 输出 `[dry-run] Would upload to cloud`；正式执行会尝试上传并输出 `ok` | 可能写入云端同步数据；同步关闭时正式执行仍输出 `ok`，不代表实际上传发生 |
| `pull` | 从云端下载数据到本地 | `siyuan sync pull -w <workspace> --dry-run` | 无 | `--dry-run` 输出 `[dry-run] Would download from cloud`；正式执行会尝试下载并输出 `ok` | 可能写入本地 workspace；同步关闭时正式执行仍输出 `ok`，不代表实际下载发生 |
| `status` | 查看当前同步配置和状态 | `siyuan sync status -w <workspace>` | 无 | 默认输出 `Enabled`、`Mode`、`Provider`、`Cloud`、`Perception`、`Interval`，有最近同步或统计信息时还会输出 `Synced`、`Stat` | 只查看状态，不触发同步；`--dry-run` 对 `status` 没有可见影响；`Mode` 显示为 `auto`、`manual`、`full-manual` 或 `unknown(n)` |



- `sync` 读取当前 workspace 的同步配置；云端相关命令需要已配置可用的同步服务。
- `sync`、`sync push`、`sync pull` 的同步失败通常不会直接表现为 CLI 非零退出或错误文本，命令可能仍输出 `ok`；需要结合 `sync status` 的 `Stat`、日志或应用内提示判断真实同步结果。
