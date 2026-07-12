# Siyuan CLI skill 设计稿

## 背景
思源笔记新增了专属 CLI，但现有文档分散在 README、`siyuan --help` 和源码注释里。目标是做一个离线 skill，让 agent 在常见 CLI 任务里直接查 skill，就能知道该用哪个命令、需要哪些参数、有哪些示例，不必现场再跑 `--help` 或上网找资料。

## 目标
1. 提供一份可直接给 agent 使用的离线参考手册。
2. 让 `SKILL.md` 成为入口，只保留高频、结构化、易扫描的信息。
3. 让 `references/` 承载逐命令细节，便于按命令快速定位。
4. 明确当前 skill 只覆盖一个版本快照，不混写历史版本。

## 适用版本
本 skill 只覆盖当前版本，基线为 `siyuan-note/siyuan` 仓库当前最新 tag：`v3.7.2-alpha.2`，发布时间为 `2026-07-07`。`SKILL.md` 的 `# Siyuan CLI` 标题下需要明确写出这一点。

## 信息来源与优先级
内容以三类官方来源为准：
1. README 中的 `Command-line Interface` 章节
2. `siyuan --help` 输出的完整命令树与 flags
3. `kernel/cli/cmd/*.go` 中每个命令定义的 Long/Short 描述、参数说明和示例

本地源码已克隆到 `D:\Workspace\sandbox\siyuan`。实现时应优先读取这个本地源码目录中的 README、CLI 帮助输出和 `kernel/cli/cmd/*.go`，避免依赖在线文档。

写入 skill 时，优先级如下：
1. 命令定义源码
2. `--help`
3. README

## 目录结构
skill 采用两层结构：
1. `SKILL.md`：入口页，包含使用前检查、调用方式、顶层命令总览、全局 flag、通用 option、注意事项。
2. `references/`：详细页，按命令拆分，每个顶层命令一份文件，外加索引页。

命名规则：
- `references/index.md`
- `references/<command>.md`

其中 `<command>` 使用 `siyuan --help` 的实际顶层命令名做文件名，保持一一对应。

## `SKILL.md` 内容设计
`SKILL.md` 只放一个总入口，不展开长篇命令细节。

### `# Siyuan CLI`
标题下方增加一行版本说明，例如：
`本手册基于 SiYuan v3.7.2-alpha.2。`

### `## Setup`
用于运行前检查，内容应短、明确，覆盖：
- CLI 二进制名称与调用方式
- 是否已加入 PATH
- workspace 路径是否已准备好
- Docker 场景下的调用入口
- 如果命令失败，先检查安装位置和 `--help`

### `### CLI Script`
说明推荐的调用形式，例如：
- 本机安装后直接用 `siyuan`
- macOS/Linux 需要时可用符号链接暴露为 `siyuan`
- Docker 场景下要显式带 `serve`

### `### Commands`
列出 `siyuan --help` 暴露的所有顶层指令，按 README 的命令类别分组展示。

每个条目至少包含：
- 命令名
- 一句话用途
- 一个简短示例

分组建议沿用 README 的语义分类：
- Notebooks & Documents
- Content
- Metadata
- Queries
- References
- Import/Export
- Data Management
- Utilities
- Database
- Server
- Workspace & System

### `### Global Flags`
只列不带参数的全局 flag。

### `### Common Options`
只列带参数的通用 flag。

### `## Notes`
收纳容易踩坑的点，例如：
- `-f json` 适合脚本化输出
- 大多数会修改数据的命令支持 `--dry-run`
- `--workspace` 是所有 workspace 相关操作的关键前提
- Docker 与本机安装的调用差异
- 当前 skill 只覆盖此版本快照，后续版本需要重新整理

## `references/` 内容设计
`references/` 目录按命令拆分，每个文件只讲一个顶层命令，避免把总手册写成一大坨。

每个命令文件固定包含：
1. 命令简介
2. 适用场景
3. 常用子命令或参数
4. 示例
5. 备注与限制
6. 来源说明

示例内容要求：
- 至少 1 个最小可运行示例
- 如果该命令有常见的 JSON 输出或只读用法，优先给出
- 如果命令会修改数据，要明确提醒

`references/index.md` 负责：
- 汇总全部命令文件
- 给出跳转说明
- 标明本套参考的版本快照

## 内容边界
本 skill 不做这些事：
- 不覆盖多版本并行文档
- 不补写非官方推测内容
- 不把使用指南写成教学文章
- 不把 `references/` 变成重复的 README

## 测试验证约束
写完 skill 后必须进行测试验证，避免把 CLI 用法、参数或示例写错。

验证只能使用 `D:\Workspace\siyuan\dev` 这个思源 workspace。不得在其他 workspace 中运行会读取或修改思源数据的 CLI 测试命令。

测试时应优先选择只读命令或支持 `--dry-run` 的命令。对于任何可能修改数据的命令，必须确认目标 workspace 是 `D:\Workspace\siyuan\dev`，并优先使用 `--dry-run`。

## 验收标准
1. 用户或 agent 只读 `SKILL.md` 和 `references/`，就能知道常用 CLI 怎么调用。
2. `SKILL.md` 中的命令总览与当前版本命令树一致。
3. `references/` 覆盖全部顶层命令，不遗漏。
4. 每个命令页都给出可操作示例，而不是只写概念描述。
5. 版本说明明确，且只针对当前版本。
6. 关键示例经过 CLI 测试验证，且测试只发生在 `D:\Workspace\siyuan\dev`。
