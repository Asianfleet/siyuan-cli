# serve

`serve` 是启动思源内核 HTTP 服务的长驻命令入口。

## 子命令一览

- 启动服务：`serve`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `serve` | 启动内核 HTTP server 并加载工作区 | `siyuan serve -w <workspace>` | `-w, --workspace`、`--wd`、`--port`、`--readonly`、`--accessAuthCode`、`--lang`、`--mode`、`--ssl`、`--attach-ui`、`--safe-mode` | 启动长驻服务；初始化配置、数据库、笔记本、资源监听、插件管理和定时任务 | 会打开并持续占用 workspace；Docker 场景从 v3.7.0 起需要显式传入 `serve`；`--dry-run` 对该命令没有预览分支 |

## 备注

- `serve` 会绕过普通 CLI 的一次性 workspace 初始化路径，改由内核启动参数处理服务生命周期。
- `--wd` 默认由可执行文件位置推导到包含 `appearance/langs` 的工作目录；打包、开发态和 macOS app bundle 路径会有差异。
- `--readonly` 在源码中是字符串参数，常见取值是 `true` 或 `false`。
