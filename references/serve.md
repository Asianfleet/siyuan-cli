# serve

`serve` 是启动思源内核 HTTP 服务的长驻命令入口。

## 子命令一览

- 启动服务：`serve`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `serve` | 启动内核 HTTP server 并加载工作区 | `siyuan serve -w <workspace> [--wd <path>] [--port <port>] [--readonly <bool>] [--accessAuthCode <code>] [--lang <lang>] [--mode <dev\|prod>] [--ssl] [--attach-ui] [--safe-mode]` | `-w, --workspace <workspace>`：目标 workspace，建议显式传入，显式值优先；`--wd <path>`：可选，SiYuan 工作目录；默认自动解析到资源目录，显式值优先；`--port <port>`：可选，监听端口；默认 `0` 随机端口；`--readonly <bool>`：可选，只读模式；默认 `false`；`--accessAuthCode <code>`：可选，访问授权码；默认空，显式值优先于环境变量；`--lang <lang>`：可选，界面语言；默认空，显式值优先于环境变量；`--mode <dev\|prod>`：可选，运行模式；默认 `prod`；`--ssl`：可选，启用 HTTPS/WSS；默认关闭；`--attach-ui`：可选，绑定桌面 UI 生命周期；默认关闭；`--safe-mode`：可选，安全模式启动；默认关闭 | 启动长驻服务；输出启动日志；成功后持续占用 workspace、监听 HTTP/WebSocket 端口，并初始化配置、数据库、笔记本、资源监听、插件管理和定时任务 | `serve` 会打开并锁定 workspace；`--port 0` 会自动选择可用端口，实际端口以启动日志或运行后配置为准 |

## 通用注意事项

- `serve` 是长驻服务命令，不会在启动成功后自动返回；脚本中启动后需要自行管理进程生命周期。
- `--dry-run` 对 `serve` 没有预览效果；传入后仍会启动长驻服务。
- workspace 已被其他内核进程占用时会报锁定失败并以非零状态退出。
- 端口不可用或端口字符串非法时会报 `boot kernel failed: listen tcp ...` 并以非零状态退出。
- `--readonly` 是字符串参数，常见取值为 `true` 或 `false`；非法布尔值不会作为参数错误提前拒绝。
- 显式 workspace 路径不存在时，启动流程可能警告并改用默认 workspace；不要用 `serve` 作为 workspace 路径合法性检查命令。
