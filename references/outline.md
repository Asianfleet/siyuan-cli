# outline

`outline` 是读取文档标题大纲树的入口。

## 子命令一览

- 查询：`get`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `get` | 读取指定文档的标题大纲 | `siyuan outline get --id <document-id> -w <workspace>` | `--id` | 只读；默认输出带缩进的大纲文本，`-f json` 时返回大纲路径数组 | 目标必须是文档块 ID；文档不存在或没有标题时会报 `document not found or has no headings` |

## 备注

- 当前命令只读取标题树，不提供写入或重排大纲的子命令。
