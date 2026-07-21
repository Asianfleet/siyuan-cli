# inbox

`inbox` 是读取云端收集箱剪藏，并将剪藏转换为本地文档的入口。

## 子命令一览

- 云端读取：`list`、`get`
- 转换写入：`convert`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `list` | 分页列出云端收集箱剪藏摘要 | `siyuan inbox list -w <workspace>` | `--page` | 只读；默认输出 `ID / TITLE / URL / CREATED` 表格和分页摘要 | 页码小于 1 时按第 1 页处理；收集箱数据来自云端，需要可用账号和会员能力 |
| `get` | 读取单条剪藏的完整 Markdown 正文 | `siyuan inbox get --id <shorthand-id> -w <workspace>` | `--id` | 只读；默认输出标题、创建时间、来源 URL 和 Markdown 正文 | `--id` 必填；正文来自云端字段 `shorthandMd` |
| `convert` | 将一条或多条云端剪藏转换为本地文档 | `siyuan inbox convert --ids <id1,id2> --notebook <notebook-id> -w <workspace> --dry-run` | `--ids`、`--notebook`、`--path`、`--remove-after` | 写入 workspace；默认还会删除成功转换的云端原件；`--dry-run` 只打印计划操作 | 会修改 workspace，建议先用 `--dry-run`；`--remove-after` 默认 `true`，不想清理云端原件时显式传 `--remove-after=false` |

## 备注

- `convert` 的 `--ids` 使用逗号分隔，源码会去空白并去重；空列表会报错。
- `convert --path` 是目标笔记本内的人类可读路径，父路径必须已存在；不传或传 `/` 时写入笔记本根目录。
