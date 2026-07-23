# 块数据模型

本文说明 `siyuan block` 相关的内部块结构。命令参数和副作用仍以 [../block.md](../block.md) 为准。

## 目录

- [使用边界](#使用边界)
- [块树与常用字段](#块树与常用字段)
- [块类型映射](#块类型映射)
- [块属性](#块属性)
- [写入时的结构约束](#写入时的结构约束)

## 使用边界

- 只记 `block` 命令语法和参数时，不用读本文件。
- 需要理解块树、父子关系、类型、属性、导出结构或更新后行为时，先读本文件。
- 需要精确 SQL 列定义时，读 [sql-schema.md](sql-schema.md)。

## 块树与常用字段

`blocks` 表和块命令都围绕同一套 ID 与树结构工作。

| 字段 | 作用 |
|---|---|
| `id` | 块 ID |
| `parent_id` | 直接父块 ID；文档块通常为空 |
| `root_id` | 顶层文档块 ID |
| `box` | 笔记本 ID |
| `path` | 存储路径 |
| `hpath` | 人类可读路径 |
| `name` | 块名称 |
| `alias` | 块别名 |
| `memo` | 块备注 |
| `content` | 去除 Markdown 标记后的文本 |
| `markdown` | 原始 Markdown 文本 |
| `fcontent` | 容器块首个子块内容 |
| `type` | 块主类型 |
| `subtype` | 块子类型 |
| `ial` | 内联属性列表 |
| `sort` | 排序权重 |
| `created` / `updated` | 创建和更新时间 |

## 块类型映射

`siyuan block dom` 输出里的 HTML 注释常暴露 `id`、`data-type`、`data-subtype`，例如：

```html
<!-- id:20250930040511-3g1uwoj data-type:NodeCodeBlock data-subtype:echarts -->
```

SQL `blocks.type/subtype` 使用短码；DOM `data-type/data-subtype` 使用 AST 类型名。两套值不是同一个命名体系。

| 结构 | SQL `type` | SQL `subtype` | DOM `data-type` | DOM `data-subtype` | 容器性 |
|---|---|---|---|---|---|
| 属性表 | `av` | 空 | `NodeAttributeView` | 空 | 叶子块 |
| 音频块 | `audio` | 空 | `NodeAudio` | 空 | 叶子块 |
| 嵌入块 | `query_embed` | 空 | `NodeBlockQueryEmbed` | 空 | 叶子块 |
| 引述块 | `b` | 空 | `NodeBlockquote` | 空 | 容器块 |
| 代码块 | `c` | 空 | `NodeCodeBlock` | 语言或渲染器，如 `mermaid` | 叶子块 |
| 文档块 | `d` | 空 | `NodeDocument` | 空 | 容器块 |
| HTML 块 | `html` | 空 | `NodeHTMLBlock` | 空 | 叶子块 |
| 标题块 | `h` | `h1`-`h6` | `NodeHeading` | `h1`-`h6` | 叶子块 |
| IFrame 块 | `iframe` | 空 | `NodeIFrame` | 空 | 叶子块 |
| 列表块 | `l` | `o` / `u` / `t` | `NodeList` | `o` / `u` / `t` | 容器块 |
| 列表项 | `i` | `o` / `u` / `t` | `NodeListItem` | `o` / `u` / `t` | 容器块 |
| 公式块 | `m` | 空 | `NodeMathBlock` | 空 | 叶子块 |
| 段落块 | `p` | 空 | `NodeParagraph` | 空 | 叶子块 |
| 超级块 | `s` | 空 | `NodeSuperBlock` | 空 | 容器块 |
| 表格块 | `t` | 空 | `NodeTable` | 空 | 叶子块 |
| 分割线 | `tb` | 空 | `NodeThematicBreak` | 空 | 叶子块 |
| 视频块 | `video` | 空 | `NodeVideo` | 空 | 叶子块 |
| 挂件块 | `widget` | 空 | `NodeWidget` | 空 | 叶子块 |

常见 `subtype` 约定：

| `subtype` | 用于 | 说明 |
|---|---|---|
| `h1`-`h6` | 标题块 | 一级到六级标题 |
| `o` | 列表块、列表项 | 有序列表 |
| `u` | 列表块、列表项 | 无序列表 |
| `t` | 列表块、列表项 | 任务列表 |
| `abc` | 代码块 DOM 子类型 | 乐谱 |
| `echarts` | 代码块 DOM 子类型 | ECharts 图表 |
| `flowchart` | 代码块 DOM 子类型 | 流程图 |
| `graphviz` | 代码块 DOM 子类型 | Graphviz |
| `mermaid` | 代码块 DOM 子类型 | Mermaid |
| `mindmap` | 代码块 DOM 子类型 | 脑图 |
| `plantuml` | 代码块 DOM 子类型 | UML 图 |

## 块属性

块属性存放在块本身的属性系统里，不是 SQL 的 `attributes` 表。

| 属性 | 说明 |
|---|---|
| `custom-avs` | 关联到该块的属性视图 ID 列表，多个 ID 用逗号分隔 |
| `custom-data-assets` | 挂件块资源引用；等效于 `data-assets` |
| `custom-heading-mode` | 嵌入块标题下方内容显示模式：`0` 显示，`1` 隐藏 |
| `custom-hidden` | 文档块隐藏标记；`true` 表示在文档树中隐藏文档及下级文档 |
| `custom-liandi-articleId` | 文档块分享到链滴后的帖子 ID |
| `custom-reminder-wechat` | 微信推送提醒时间 |
| `custom-riff-decks` | 闪卡卡包 ID 列表 |
| `custom-sy-fullwidth` | 文档自适应宽度；常见值 `true` / `false` |
| `custom-sy-readonly` | 文档只读；常见值 `true` / `false` |
| `custom-dailynote-{yyyymmdd}` | Daily Note 标记；`{yyyymmdd}` 是 `YYYYMMDD` 日期 |
| `data-assets` | 挂件块引用的资源文件 |
| `data-export-html` | 挂件块导出时替换的 HTML 内容 |
| `data-export-md` | 挂件块导出时替换的 Markdown 内容 |

## 写入时的结构约束

- `insert`、`append`、`prepend`、`move` 只能落在合法的父子结构上；不是所有块都能直接装子块。
- `update` 替换的是块内容，不是简单改字符串；某些块更新后会重新生成块 ID。
- `delete` 会删除目标块及其子树。
- `dom` 和 `kramdown` 是块结构的序列化结果，不等于 SQL 原表字段。
