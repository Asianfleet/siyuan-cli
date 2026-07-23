# bookmark

`bookmark` 是书签列表、标签列表、删除和重命名入口。

## 子命令一览

- 查询：`list`、`labels`
- 写入：`remove`、`rename`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `list` | 列出书签及命中数量 | `siyuan bookmark list -w <workspace>` | 无 | 只读；默认输出 `NAME / COUNT` 表格，`-f json` 时返回含 `name`、`blocks`、`type`、`depth`、`count` 的书签集合 | `--dry-run` 对该只读命令无特殊效果 |
| `labels` | 列出全部书签标签 | `siyuan bookmark labels -w <workspace>` | 无 | 只读；默认输出 `LABEL` 表格，`-f json` 时返回标签字符串数组 | 只返回标签名，不返回每个标签的数量；`--dry-run` 对该只读命令无特殊效果 |
| `remove` | 删除指定书签标签 | `siyuan bookmark remove --label <label> -w <workspace> --dry-run` | `--label <label>`：必填，目标书签标签，默认空 | 写入 workspace；`--dry-run` 输出 `[dry-run] Would remove bookmark "<label>"`；真实执行成功时无标准输出 | 缺少 `--label` 会报错；不存在的标签按成功无变化处理；真实执行会移除所有匹配标签的书签属性 |
| `rename` | 将旧书签标签重命名为新标签 | `siyuan bookmark rename --old <old> --new <new> -w <workspace> --dry-run` | `--old <old>`：必填，原书签标签，默认空；`--new <new>`：必填，新书签标签，默认空，真实执行前会 trim 并校验 Markdown 标记符 | 写入 workspace；`--dry-run` 输出 `[dry-run] Would rename bookmark "<old>" to "<new>"`；真实执行成功时无标准输出 | 缺少 `--old` 或 `--new` 会报错；不存在的旧标签按成功无变化处理；已知 bug：`--dry-run` 不校验新标签的 Markdown 标记符或 trim 后为空 |



- 书签写入操作按标签名处理，不按块 ID 或文档路径处理。
- `remove` 和 `rename` 会修改匹配块的书签属性，并刷新文件树。
