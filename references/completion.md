# completion

`completion` 是生成 shell 自动补全脚本的入口。

## 子命令一览

- Shell 补全：`bash`、`fish`、`powershell`、`zsh`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `bash` | 生成 Bash 自动补全脚本 | `siyuan completion bash [--no-descriptions] -w <workspace>` | `--no-descriptions`：可选，默认 `false`；省略补全候选的描述文本；优先级为显式开启高于默认关闭 | 只读；向 stdout 输出 Bash 补全脚本 | `--dry-run` 对输出无实际差异；工作空间无效时会先报错并停止 |
| `fish` | 生成 Fish 自动补全脚本 | `siyuan completion fish [--no-descriptions] -w <workspace>` | `--no-descriptions`：可选，默认 `false`；省略补全候选的描述文本；优先级为显式开启高于默认关闭 | 只读；向 stdout 输出 Fish 补全脚本 | `--dry-run` 对输出无实际差异；工作空间无效时会先报错并停止 |
| `powershell` | 生成 PowerShell 自动补全脚本 | `siyuan completion powershell [--no-descriptions] -w <workspace>` | `--no-descriptions`：可选，默认 `false`；省略补全候选的描述文本；优先级为显式开启高于默认关闭 | 只读；向 stdout 输出 PowerShell 补全脚本 | 脚本只负责生成，不会自动加载到当前会话；`--dry-run` 对输出无实际差异；工作空间无效时会先报错并停止 |
| `zsh` | 生成 Zsh 自动补全脚本 | `siyuan completion zsh [--no-descriptions] -w <workspace>` | `--no-descriptions`：可选，默认 `false`；省略补全候选的描述文本；优先级为显式开启高于默认关闭 | 只读；向 stdout 输出 Zsh 补全脚本 | 需要 shell 已启用 `compinit`；`--dry-run` 对输出无实际差异；工作空间无效时会先报错并停止 |



- `completion` 是 Cobra 自动生成的补全命令，只把脚本写到 stdout，不直接改 workspace 数据。
- 这个命令仍会走根命令的 workspace 初始化；如果报 `appearance files not found`、`directory not found` 或 `not a valid workspace`，不会生成补全脚本。
- `--dry-run` 在这里不会改变补全脚本内容；真正影响输出的是 `--no-descriptions`。
