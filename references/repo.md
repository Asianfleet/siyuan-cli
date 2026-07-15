# repo

`repo` 是数据快照的创建、查询、标记、回滚、清理和快照文件级操作入口。

## 子命令一览

- 快照列表与查询：`list`、`diff`、`search`
- 快照写入与回滚：`create`、`tag`、`untag`、`checkout`、`purge`
- 快照文件操作：`file get`、`file rollback`、`file open`、`file export`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `list` | 分页列出数据快照或标签快照 | `siyuan repo list -w <workspace>` | `--page`、`--tag` | 只读；默认输出快照表格和分页信息，`--tag` 时列出标签快照 | `--page` 小于 1 时按第 1 页处理；`--tag` 不返回普通分页统计 |
| `create` | 创建新的数据快照 | `siyuan repo create -w <workspace> --dry-run` | `--memo` | 写入 workspace 的数据仓库；成功输出创建的快照 ID；`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；`--memo` 可记录快照说明 |
| `tag` | 给指定快照增加标签 | `siyuan repo tag --id <snapshot-id> --name <tag-name> -w <workspace> --dry-run` | `--id`、`--name` | 写入 workspace 的数据仓库标签；成功输出 `ok`；`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；`--id` 和 `--name` 都必填 |
| `untag` | 移除指定快照标签 | `siyuan repo untag --name <tag-name> -w <workspace> --dry-run` | `--name` | 写入 workspace 的数据仓库标签；成功输出 `ok`；`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；只按标签名移除 |
| `checkout` | 将 workspace 回滚到指定快照 | `siyuan repo checkout --id <snapshot-id> -w <workspace> --dry-run` | `--id` | 写入 workspace；成功输出 `ok`，并触发文件树和 UI 刷新；`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；这是整库回滚，高风险 |
| `diff` | 比较两个快照差异 | `siyuan repo diff --left <snapshot-id> --right <snapshot-id> -w <workspace>` | `--left`、`--right` | 只读；输出 JSON 格式的快照差异 | `--left` 和 `--right` 都必填；源码固定 JSON 输出 |
| `search` | 在快照文件中搜索关键词 | `siyuan repo search <keyword> -w <workspace>` | `<keyword>`、`--page` | 只读；默认输出文件表格和分页信息 | `<keyword>` 是位置参数且至少 1 个；`--page` 小于 1 时按第 1 页处理 |
| `purge` | 清理旧数据快照 | `siyuan repo purge -w <workspace> --dry-run` | 无 | 写入 workspace 的数据仓库；成功输出 `ok`；`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；具体清理范围由内核仓库策略决定 |
| `file get` | 读取快照中的文件内容 | `siyuan repo file get --id <file-id> -w <workspace>` | `--id`、`--output` | 只读 workspace；未指定 `--output` 时输出路径、大小和内容，指定后写入目标文件 | `--id` 是快照文件 ID；指定 `--output` 时支持 `--dry-run` 预览写出目标 |
| `file rollback` | 从快照回滚单个文件 | `siyuan repo file rollback --id <file-id> -w <workspace> --dry-run` | `--id` | 写入 workspace；成功输出 `ok`，可能触发对应文档刷新；`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；这是单文件回滚 |
| `file open` | 预览快照中的文件内容 | `siyuan repo file open --id <file-id> -w <workspace>` | `--id` | 只读；输出标题和内容 | `--id` 是快照文件 ID；用于查看内容，不写出文件 |
| `file export` | 将快照文件导出到临时文件 | `siyuan repo file export --id <file-id> -w <workspace>` | `--id` | 只读 workspace；成功输出导出的临时文件路径 | 支持 `--dry-run`；导出文件位于内核选择的临时路径 |

## 备注

- 本页基于 SiYuan v3.7.2-alpha.2 的 `kernel/cli/cmd/repo.go`。
- `repo checkout`、`repo file rollback` 和 `repo purge` 都会改动 workspace 或其数据仓库，执行前应确认 `-w <workspace>` 指向正确目录。
- `repo diff` 在源码中不按 `-f` 切换格式，固定输出缩进 JSON。
