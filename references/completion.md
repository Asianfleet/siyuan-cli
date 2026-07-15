# completion

`completion` 是生成 shell 自动补全脚本的入口。

## 子命令一览

- Shell 补全：`bash`、`fish`、`powershell`、`zsh`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `bash` | 生成 Bash 自动补全脚本 | `siyuan completion bash -w <workspace>` | `--no-descriptions` | 只读；向 stdout 输出 Bash 补全脚本 | 可将输出重定向到 Bash completion 加载目录；`--no-descriptions` 会禁用补全描述 |
| `fish` | 生成 Fish 自动补全脚本 | `siyuan completion fish -w <workspace>` | `--no-descriptions` | 只读；向 stdout 输出 Fish 补全脚本 | 可将输出写入 `~/.config/fish/completions/siyuan.fish`；`--no-descriptions` 会禁用补全描述 |
| `powershell` | 生成 PowerShell 自动补全脚本 | `siyuan completion powershell -w <workspace>` | `--no-descriptions` | 只读；向 stdout 输出 PowerShell 补全脚本 | 当前会话可通过管道交给 `Invoke-Expression` 加载；`--no-descriptions` 会禁用补全描述 |
| `zsh` | 生成 Zsh 自动补全脚本 | `siyuan completion zsh -w <workspace>` | `--no-descriptions` | 只读；向 stdout 输出 Zsh 补全脚本 | 需要 shell 已启用 `compinit`；`--no-descriptions` 会禁用补全描述 |

## 备注

- `completion` 是 Cobra 自动补全命令，输出脚本而不是操作 workspace 数据。
- 为避免默认 workspace 无效导致初始化差异，脚本生成命令可显式带 `-w <workspace>`。
