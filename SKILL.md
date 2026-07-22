---
name: siyuan-cli
description: "Use this skill whenever the user wants to operate, inspect, query, import, export, synchronize, or manage a SiYuan workspace through the `siyuan` command-line interface. Triggers include: mentions of SiYuan CLI, `siyuan --help`, `siyuan` commands, SiYuan workspace paths, notebook/document/block operations, tags, bookmarks, templates, search, SQL queries, backlinks, assets, files, snapshots, history, sync, database attribute views, or starting the SiYuan kernel HTTP server from the command line. Also use when choosing the correct SiYuan CLI command, understanding global flags and common options, or looking up offline command examples. Do NOT use for SiYuan HTTP API work, plugin development, general note-taking advice, or unrelated command-line tasks."
---

# Siyuan CLI

## Preconditions

- 检查 SiYuan 内核是否可从 PATH 调用。优先使用 `siyuan` 调用 CLI；如果 PATH 中没有可用命令，再定位安装目录下的 `resources/kernel/SiYuan-Kernel` 或 `SiYuan-Kernel.exe`。如果 PATH 和安装目录都无法定位内核，必须停下来向用户说明无法运行 CLI，并请用户提供安装目录或把内核加入 PATH。
- 确定 workspace。用户显式指定工作空间时，必须使用该工作空间；用户没有指定，或说使用默认工作空间时，检查 `SIYUAN_WORKSPACE_PATH` 是否已设置。如果已设置，后续指令不需要带 `-w`，SiYuan 会自动使用该环境变量指定的 workspace。如果未设置，必须停下来提示用户显式指定 workspace 或设置 `SIYUAN_WORKSPACE_PATH`，绝对不能猜测 workspace 路径。

### CLI Script

```powershell
# 每次调用 siyuan 时直接通过 `-w` 指定 workspace：
siyuan notebook list -w "<workspace>"
```

```powershell
# 如果在同一次 bash 调用中连续执行多条 siyuan 指令，可以先设置环境变量以减少重复：
$env:SIYUAN_WORKSPACE_PATH = "<workspace>"
siyuan notebook list
siyuan search "keyword"
```

### Commands

| 分类 | 指令 | 描述 | 示例 |
|---|---|---|---|
| Notebooks & Documents | `notebook` | 管理笔记本 | `siyuan notebook list -w <workspace>` |
| Notebooks & Documents | `document` | 管理文档 | `siyuan document list --notebook <notebook-id> -w <workspace>` |
| Notebooks & Documents | `dailynote` | 创建或写入今日 Daily Note | `siyuan dailynote create --notebook <notebook-id> -w <workspace>` |
| Content | `block` | 读取、插入、更新、移动或删除块 | `siyuan block get --id <block-id> -w <workspace>` |
| Content | `attr` | 读取或设置块属性 | `siyuan attr get --id <block-id> -w <workspace>` |
| Content | `outline` | 读取文档标题大纲 | `siyuan outline get --id <document-id> -w <workspace>` |
| Metadata | `tag` | 管理标签 | `siyuan tag list -w <workspace>` |
| Metadata | `bookmark` | 管理书签 | `siyuan bookmark list -w <workspace>` |
| Metadata | `template` | 搜索、读取、渲染或创建模板片段 | `siyuan template search "" -w <workspace>` |
| Queries | `search` | 全文、查询语法、SQL、正则、语义或资源文件内容搜索 | `siyuan search "keyword" -w <workspace>` |
| Queries | `sql` | 执行 SQL 查询 | `siyuan sql "select * from blocks limit 5" -w <workspace>` |
| References | `ref` | 查询反链、提及并刷新引用 | `siyuan ref backlinks --id <block-id> -w <workspace>` |
| Import/Export | `export` | 导出 Markdown、HTML、Word、`.sy.zip` 或完整数据备份 | `siyuan export md --id <document-id> -w <workspace>` |
| Import/Export | `import` | 导入 Markdown、`.sy.zip` 或数据备份 | `siyuan import md --file <path> --notebook <notebook-id> -w <workspace> --dry-run` |
| Import/Export | `inbox` | 管理云端收集箱剪藏 | `siyuan inbox list -w <workspace>` |
| Data Management | `repo` | 管理数据快照和快照文件 | `siyuan repo list -w <workspace>` |
| Data Management | `history` | 查询、读取、回滚或清理数据历史 | `siyuan history list -w <workspace>` |
| Data Management | `sync` | 执行云端同步、上传、下载或查看同步状态 | `siyuan sync status -w <workspace>` |
| Utilities | `asset` | 上传、查看、清理或统计资源文件 | `siyuan asset unused -w <workspace>` |
| Utilities | `file` | 在 workspace 内列出、读取、写入、查找、复制或删除文件 | `siyuan file find data -w <workspace>` |
| Database | `database` | 管理数据库，也就是属性视图 | `siyuan database search "<name>" -w <workspace>` |
| Server | `serve` | 启动 kernel HTTP server | `siyuan serve --workspace <workspace> --port 6806` |
| Workspace & System | `workspace` | 列出或检查 SiYuan workspace | `siyuan workspace info -w <workspace>` |
| Workspace & System | `system` | 输出系统信息 | `siyuan system current-time -w <workspace>` |
| Workspace & System | `completion` | 生成 shell 自动补全脚本 | `siyuan completion powershell -w <workspace>` |
| Workspace & System | `help` | 查看命令帮助 | `siyuan help block -w <workspace>` |

### Global Flags

不带参数的全局 flag：

| Flag | 说明 |
|---|---|
| `--dry-run` | 预览命令会做什么，不实际修改数据；它通常只打印计划操作，不保证校验目标 ID、父块合法性或文件是否存在 |
| `-h`, `--help` | 查看当前命令或子命令帮助；本 Skill 覆盖常用离线参考，但遇到版本不一致时以目标版本源码为准 |
| `--version` | 输出 CLI 版本 |

### Common Options

带参数的通用 flag：

| Flag | 默认值 | 说明 |
|---|---|---|
| `-w`, `--workspace <path>` | 环境变量 `SIYUAN_WORKSPACE_PATH` | 用户显式指定 workspace 时传入；如果用户未指定且环境变量未设置，停下来询问，不使用猜测的默认路径 |
| `-f`, `--format <table\|json>` | `table` | 输出格式默认保留 `table`；只有任务需要结构化解析、批处理或程序消费时才使用 `json` |
| `-v`, `--log-level <level>` | CLI 单次命令默认为 `warn` | 日志级别：`off`、`trace`、`debug`、`info`、`warn`、`error`、`fatal` |

`serve` 还有专用启动选项：

| Flag | 默认值 | 说明 |
|---|---|---|
| `--wd <path>` | 自动解析 | SiYuan 工作目录，通常是包含 `appearance/` 的资源目录 |
| `--port <port>` | `0` | HTTP server 端口；Docker 场景通常使用固定端口 |
| `--readonly <bool>` | `false` | 只读模式 |
| `--accessAuthCode <code>` | 空 | 访问授权码；Docker 部署通常必须设置 |
| `--lang <lang>` | 空 | 语言，如 `en`、`zh-CN`、`zh-TW` |
| `--mode <dev\|prod>` | `prod` | 运行模式 |
| `--ssl` | `false` | 启用 HTTPS 和 WSS |
| `--attach-ui` | `false` | 将 kernel 生命周期绑定到桌面 UI 进程 |
| `--safe-mode` | `false` | 安全模式启动 |

## Notes

- 默认使用 CLI 的表格输出；只有当任务需要结构化解析、批处理或把结果继续交给程序处理时，才加 `-f json`。
- 修改类命令包括但不限于 `notebook create/remove/rename`、`document create/remove/rename/move/duplicate`、`block insert/update/delete/move`、`attr set`、`import`、`repo checkout`、`history rollback/clear`、`sync push/pull`、`file write/delete/rename/copy`，先用 `--dry-run`；注意 `--dry-run` 通常只打印计划操作，不等同于完整校验目标 ID、父块合法性或文件是否存在。
- 如果命令报 `appearance files not found`，说明 CLI 没有从正确的 SiYuan 工作目录启动，改用安装目录中的 kernel 二进制或设置正确的工作目录。
- CLI 可直接访问 workspace 数据，不要求先启动 HTTP server。
- `help`、`system` 等看似只读的命令也会触发普通 CLI 初始化；用户未指定 workspace 且 `SIYUAN_WORKSPACE_PATH` 未设置时，停下来询问。
- 除 `workspace` 子命令外，大多数命令会初始化 workspace 和数据库；workspace 路径错误会导致命令失败或读到错误数据。
- `file` 命令只能访问 workspace 内路径，源码会阻止路径逃逸。
