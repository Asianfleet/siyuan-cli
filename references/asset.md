# asset

`asset` 是工作区资源文件入口，用于上传文件、列出未使用资源、清理未使用资源和查看资源文件信息。

## 子命令一览

- 上传：`upload`
- 未使用资源：`unused`、`clean`
- 资源信息：`stat`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `upload` | 将本地文件复制为指定文档可用的资源文件 | `siyuan asset upload --id <block-id> --file <path>... -w <workspace> [--dry-run]` | `--id <block-id>`：必填，目标文档块 ID；`--file <path>`：必填，可重复传入，命令会先转为绝对路径 | 写入 workspace；表格输出 `FILE / ASSET_PATH`，JSON 输出源文件名到资源路径的映射；`--dry-run` 只输出将上传的文件数量和目标 ID | `--dry-run` 在必填参数通过后直接返回，不检查目标 ID 是否可用，也不检查文件是否存在。当前版本对部分未打开文档执行实际上传可能报 `插入资源文件失败，请重新打开文档`。 |
| `unused` | 列出当前 workspace 内未被引用的资源文件 | `siyuan asset unused -w <workspace>` | 无 | 只读；无结果时输出 `No unused assets found.`；有结果时表格输出 `PATH / NAME`，随后输出数量；JSON 输出未使用资源数组 | `--dry-run` 对该只读命令没有特殊预览语义，仍会执行扫描。扫描结果包含全局 `data/assets` 和非加密笔记本内的 `assets` 目录。 |
| `clean` | 清理未使用资源；可指定单个资源路径 | `siyuan asset clean [--path <asset-path>] -w <workspace> [--dry-run]` | `--path <asset-path>`：可选，指定单个资源路径；默认空，省略时批量清理全部未使用资源；非空时优先级高于批量清理 | 写入 workspace；带 `--path` 成功时输出被处理路径的绝对路径；不带 `--path` 时删除全部未使用资源并逐行输出删除路径和数量；`--dry-run` 只输出计划 | 不带 `--path` 的实际清理会删除全部未使用资源，风险高。带 `--path` 时不会先确认该路径是否真的未使用；路径不存在也不会报错，而是输出拼出的绝对路径。当前 `--path` 未限制必须位于 `assets/` 下，`..` 形式路径可能访问 data 目录之外的 workspace 文件，这是已知 bug。 |
| `stat` | 查看 data 目录下指定路径的文件或目录信息 | `siyuan asset stat --path <asset-path> -w <workspace>` | `--path <asset-path>`：必填，按 data 目录相对路径解析，例如 `assets/image.png` | 只读；表格输出 `Path / Size / IsDir / ModTime`，JSON 输出同等字段对象 | `--dry-run` 对该只读命令没有特殊预览语义，仍会读取文件信息。缺少 `--path` 或目标不存在时退出码为 1，并输出 `Error:` 和用法。当前 `--path` 未限制必须位于 `assets/` 下，`..` 形式路径可能读取 data 目录之外的 workspace 文件信息，这是已知 bug。 |

## 通用注意事项

- `asset` 子命令需要有效 workspace，示例固定显式传入 `-w <workspace>`。
- 只有 `upload` 和 `clean` 有写入语义；写入命令的 `--dry-run` 只代表参数层面的计划输出，不等同于完整可执行性校验。
- `unused` 与批量 `clean` 会跳过加密笔记本中的资源；命中加密笔记本路径的 `--path` 操作会失败。
- `clean` 删除资源前会写入历史备份；清理失败或后续手动回收测试数据时，需要同时检查资源路径和对应历史目录。
