# repo

`repo` 是数据快照的创建、查询、标记、回滚、清理和快照文件级操作入口。所有命令都需要有效 workspace；除少数 CLI 层 `--dry-run` 短路外，还需要先初始化数据仓库密钥。

## 子命令一览

- 快照列表与查询：`list`、`diff`、`search`
- 快照写入与回滚：`create`、`tag`、`untag`、`checkout`、`purge`
- 快照文件操作：`file get`、`file rollback`、`file open`、`file export`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `list` | 分页列出普通快照或标签快照 | `siyuan repo list -w <workspace>` | `--page`：页码，默认 `1`，小于 `1` 按 `1`；`--tag`：只列标签快照，默认 `false` | 只读；表格输出 `ID`、`CREATED`、`MEMO`，普通快照追加 `Page <page>/<pageCount>, <totalCount> total`；无结果输出 `No snapshots found.`；`-f json` 输出结构化结果 | `--dry-run` 不短路，仍会读取仓库；未初始化数据仓库密钥时报错并打印 usage；`--tag` 不输出分页统计 |
| `create` | 创建新的数据快照 | `siyuan repo create -w <workspace> --memo <memo> --dry-run` | `--memo`：快照说明，默认空；正式创建时必须非空；`--dry-run` 优先于 memo 校验 | 写入数据仓库；成功输出 `created snapshot <id>`；`--dry-run` 输出计划文本 | 会修改 workspace；未初始化数据仓库密钥时报错并打印 usage |
| `tag` | 给指定快照增加标签 | `siyuan repo tag -w <workspace> --id <snapshot-id> --name <tag-name> --dry-run` | `--id`：快照 ID，必填；`--name`：标签名，必填，会去除不可见字符、首尾空白和非法文件名字符；缺参校验优先于 `--dry-run` | 写入数据仓库标签；成功输出 `ok`；`--dry-run` 输出计划文本 | 标签名清洗后为空或不是有效文件名会报错；未初始化数据仓库密钥时报错并打印 usage |
| `untag` | 移除指定标签快照 | `siyuan repo untag -w <workspace> --name <tag-name> --dry-run` | `--name`：标签名，必填；缺参校验优先于 `--dry-run` | 写入数据仓库标签；成功输出 `ok`；`--dry-run` 输出计划文本 | 只按标签名移除；未初始化数据仓库密钥时报错并打印 usage |
| `checkout` | 将整个 workspace 回滚到指定快照 | `siyuan repo checkout -w <workspace> --id <snapshot-id> --dry-run` | `--id`：快照 ID，必填；缺参校验优先于 `--dry-run` | 写入 workspace；执行前会为当前数据创建 `Backup before checkout` 快照并暂停同步；CLI 成功路径输出 `ok`；`--dry-run` 输出计划文本 | 高风险整库回滚；已知 bug：底层回滚失败时 CLI 仍可能输出 `ok`，例如未初始化数据仓库密钥或无效快照 ID 这类失败不会可靠反映在退出状态 |
| `diff` | 比较两个快照差异 | `siyuan repo diff -w <workspace> --left <snapshot-id> --right <snapshot-id>` | `--left`：左侧快照 ID，必填；`--right`：右侧快照 ID，必填；缺参校验优先于读取仓库 | 只读；固定输出缩进 JSON，不受 `-f` 影响 | `--dry-run` 不短路，仍会读取仓库；未初始化数据仓库密钥时报错并打印 usage |
| `search` | 在快照文件中搜索关键词 | `siyuan repo search -w <workspace> <keyword>` | `<keyword>`：搜索词，至少 1 个位置参数，只使用第 1 个；`--page`：页码，默认 `1`，小于 `1` 按 `1` | 只读；表格输出 `FILE`、`TITLE`、`PATH`、`HSIZE`、`UPDATED`，并追加分页统计；无结果输出 `No files found.`；`-f json` 输出结构化结果 | `--dry-run` 不短路，仍会读取仓库；未初始化数据仓库密钥时报错并打印 usage |
| `purge` | 清理旧数据快照和无引用对象 | `siyuan repo purge -w <workspace> --dry-run` | 无 | 写入数据仓库；成功输出 `ok`；`--dry-run` 输出计划文本 | 高风险清理；当前可观察到的已知 bug：未初始化数据仓库密钥时正式执行可能 panic，而不是返回普通错误 |
| `file get` | 读取快照中的原始文件内容 | `siyuan repo file get -w <workspace> --id <file-id> [--output <path>]` | `--id`：快照文件 ID，必填；`--output`：写出路径，默认空并输出到 stdout；带 `--output` 时 `--dry-run` 优先于读取仓库 | 不带 `--output` 时输出 `Path`、`Size` 和内容；带 `--output` 时写入指定路径且成功无额外输出 | `--output` 可写到 workspace 外部路径；不带 `--output` 时即使加 `--dry-run` 也会读取仓库；未初始化数据仓库密钥时报错并打印 usage |
| `file rollback` | 从快照回滚单个文件 | `siyuan repo file rollback -w <workspace> --id <file-id> --dry-run` | `--id`：快照文件 ID，必填；缺参校验优先于 `--dry-run` | 写入 workspace；执行前会为当前数据创建 `Backup before checkout` 快照；成功输出 `ok`；`--dry-run` 输出计划文本 | 高风险单文件回滚；未初始化数据仓库密钥时报错并打印 usage |
| `file open` | 预览快照文件内容 | `siyuan repo file open -w <workspace> --id <file-id>` | `--id`：快照文件 ID，必填；缺参校验优先于读取仓库 | 只读；输出 `Title: <title>`、分隔线和预览内容 | `--dry-run` 不短路，仍会读取仓库；不可直接展示的资源文件可能只返回路径或临时预览路径；未初始化数据仓库密钥时报错并打印 usage |
| `file export` | 将快照文件导出到内核临时导出目录 | `siyuan repo file export -w <workspace> --id <file-id> --dry-run` | `--id`：快照文件 ID，必填；缺参校验优先于 `--dry-run` | 只读 workspace、写入临时导出文件；成功输出可访问的 `/export/...` 路径；`--dry-run` 输出计划文本 | `.sy` 文件会导出为 `.sy.zip`；加密笔记本锁定时会拒绝导出；未初始化数据仓库密钥时报错并打印 usage |



- `--dry-run` 只有在 `create`、`tag`、`untag`、`checkout`、`purge`、`file rollback`、`file export`，以及带 `--output` 的 `file get` 中会提前返回计划文本。
- `list`、`diff`、`search`、`file open` 和不带 `--output` 的 `file get` 会忽略 `--dry-run` 的预览意图，继续初始化并读取数据仓库。
- 分页类命令每页固定最多返回 `32` 条；`--page` 小于 `1` 时按第 `1` 页处理。
- `checkout`、`purge` 和 `file rollback` 会修改 workspace 或数据仓库；执行前必须确认 `-w <workspace>` 指向正确目录，并确保已经有可恢复的外部备份或隔离快照。
- 未初始化数据仓库密钥时，多数 repo 命令会返回错误并打印 usage；`checkout` 可能误报 `ok`，`purge` 可能 panic。
