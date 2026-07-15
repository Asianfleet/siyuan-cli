# asset

`asset` 是工作区资源文件的上传、未使用资源检查、清理和文件信息查询入口。

## 子命令一览

- 上传资源：`upload`
- 未使用资源：`unused`、`clean`
- 资源信息：`stat`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `upload` | 将本地文件上传到指定文档的工作区资源中 | `siyuan asset upload --id <block-id> --file <path> -w <workspace> --dry-run` | `--id`、`--file` | 写入 workspace；成功时默认输出 `FILE / ASSET_PATH` 表格，`-f json` 时返回源文件到资源路径的映射；`--dry-run` 只打印计划上传数量 | 会修改 workspace，建议先用 `--dry-run`；`--file` 可重复传入多个本地文件，命令会先转为绝对路径 |
| `unused` | 列出未被引用的资源文件 | `siyuan asset unused -w <workspace>` | 无 | 只读；默认输出 `PATH / NAME` 表格和数量，`-f json` 时返回未使用资源数组 | 扫描结果来自当前 workspace 的资源引用状态 |
| `clean` | 清理未使用资源，可指定单个资源路径 | `siyuan asset clean --path <asset-path> -w <workspace> --dry-run` | `--path` | 写入 workspace；指定 `--path` 时输出移除结果，未指定时输出被移除路径列表和数量；`--dry-run` 只打印计划清理动作 | 会修改 workspace，建议先用 `--dry-run`；不带 `--path` 会清理全部未使用资源 |
| `stat` | 查看工作区数据目录下资源文件或目录信息 | `siyuan asset stat --path <asset-path> -w <workspace>` | `--path` | 只读；默认输出路径、大小、是否目录和修改时间，`-f json` 时返回同等字段对象 | `--path` 是相对 `data` 目录的路径，例如 `assets/image/xxx.png`；缺失路径或文件不存在会报错 |

## 备注

- `asset` 子命令需要有效 workspace，常用全局参数是 `-w, --workspace`、`-f, --format` 和 `--dry-run`。
- `upload` 会调用本地资源插入逻辑，目标 `--id` 应是可接收资源的文档块 ID。
