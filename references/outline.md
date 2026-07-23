# outline

`outline` 是读取文档标题大纲树的入口。

## 子命令一览

- 查询：`get`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `get` | 读取指定文档的标题大纲 | `siyuan outline get --id <block-id> -w <workspace>` | `--id <block-id>`：必填，默认空；用于定位文档，传入文档内任意块 ID 时会解析到所属文档 | 只读；默认输出带缩进的大纲文本，`-f json` 时返回大纲路径数组 | 缺少 `--id` 报 `--id is required`；文档不存在或没有标题时都会报 `document not found or has no headings`；`--dry-run` 对该只读命令无输出差异；CLI 不支持加密笔记本及其块 |

## 通用注意事项

- 当前命令只读取标题树，不提供写入或重排大纲的子命令。
