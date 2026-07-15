# help

`help` 是查看顶层命令或子命令帮助文本的入口。

## 子命令一览

- 直接调用：`help [command]`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `help [command]` | 输出指定命令路径的帮助文本 | `siyuan help <command> -w <workspace>` | `<command>` | 只读；向 stdout 输出 Cobra 帮助文本 | 例如 `<command>` 可为 `block`、`block get` 或 `completion`；默认 workspace 无效时必须显式传入 `-w <workspace>` |

## 备注

- `help` 不读取或修改笔记数据，但仍会经过普通 CLI 初始化路径。
- 也可以用任意命令的 `--help` 查看帮助，例如 `siyuan block get --help`；`--help` 路径通常不需要初始化 workspace。
