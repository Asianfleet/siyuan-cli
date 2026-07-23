# system

`system` 是思源 CLI 暴露的系统信息查询入口，目前只有 `current-time`。

## 子命令一览

- 系统信息：`current-time`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `current-time` | 输出当前系统时间 | `siyuan system current-time -w <workspace>` | 无 | 只读；默认输出本机本地时区的 RFC3339 时间字符串，`-f json` 时输出 `{ "time": "..." }` | 该命令仍走普通 CLI 初始化路径；`--dry-run` 对输出没有实际差异；`-f` 传入非 `json` 值时按普通文本输出；多余位置参数会被忽略 |



- `system` 当前只有 `current-time` 一个子命令。
- 输出时间按运行环境本地时区格式化为 RFC3339 字符串。
