# help

`help` 是查看命令帮助文本的入口，输出 Cobra 生成的使用说明。

## 子命令一览

- 直接调用：`help [command]`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `help [command]` | 输出指定命令路径或当前层级的帮助文本 | `siyuan help [command] -w <workspace>` | `<command>`：可选，命令路径；用于指定要查看的命令或子命令，默认空，省略时输出根命令帮助；命令路径按 Cobra 命令树逐级匹配。 | 只读；向 stdout 输出 Cobra 帮助文本 | `--dry-run` 对帮助输出无实际差异；命令不存在时返回 `Unknown help topic [...]`，并继续打印当前层级帮助 |



- `help` 不读取或修改笔记数据，但仍会经过普通 CLI 初始化路径。
- 也可以用任意命令的 `--help` 查看同级帮助，例如 `siyuan block get --help`。
- `help` 的错误处理遵循 Cobra：命令路径不存在时会报 `Unknown help topic [...]`，然后输出当前层级帮助。
