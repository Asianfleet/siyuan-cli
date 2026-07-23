# tag

`tag` 是标签检索、删除和重命名入口。

## 子命令一览

- 查询：`list`
- 写入：`remove`、`rename`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `list` | 列出标签，可按关键字过滤 | `siyuan tag list -w <workspace>` | `--keyword <keyword>`，默认空字符串 | 只读；默认输出 `LABEL` 表格；`-f json` 返回字符串数组 | `--keyword` 为空时列出全部标签；带关键字时匹配片段会写成 `<mark>...</mark>`，JSON 输出同样包含标记 |
| `remove` | 删除指定标签 | `siyuan tag remove --label <label> -w <workspace> --dry-run` | `--label <label>`，默认空字符串 | 写入 workspace；成功时无输出；`--dry-run` 输出 `[dry-run] Would remove tag "<label>"` | 缺少 `--label` 返回错误；`--dry-run` 不校验标签是否存在；非 dry-run 删除不存在标签也返回成功 |
| `rename` | 将旧标签名重命名为新标签名 | `siyuan tag rename --old <old-label> --new <new-label> -w <workspace> --dry-run` | `--old <old-label>`：必填，原标签名，默认空；`--new <new-label>`：必填，新标签名，默认空 | 写入 workspace；成功时无输出；`--dry-run` 输出 `[dry-run] Would rename tag "<old-label>" to "<new-label>"` | 缺少 `--old` 或 `--new` 返回错误；`--dry-run` 不校验标签是否存在或新标签格式；非 dry-run 重命名不存在标签也返回成功 |



- 写入类操作会修改块内容或文档属性中的标签，适合先用 `list --keyword <label>` 确认目标标签。
- `rename` 的非 dry-run 会清理新标签首尾 `/` 和空白；清理后为空或包含标签标记非法字符时返回错误。
