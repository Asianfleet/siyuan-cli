# SQL 数据模型

本文件说明 `siyuan sql` 可查询的工作空间索引库结构。编写 SQL 前先确认本文件中的表、字段和取值；属性视图的行、列、单元格数据不通过 SQL 查询，必须使用 `database` 指令。

## 目录

- [使用边界](#使用边界)
- [表选择指南](#表选择指南)
- [`blocks` 表](#blocks-表)
- [`blocks.type` 取值](#blockstype-取值)
- [`blocks.subtype` 取值](#blockssubtype-取值)
- [`refs` 表](#refs-表)
- [`attributes` 表](#attributes-表)
- [`assets` 表](#assets-表)
- [`file_annotation_refs` 表](#file_annotation_refs-表)
- [`spans` 表](#spans-表)
- [`spans.type` 取值](#spanstype-取值)
- [`stat` 表](#stat-表)
- [常用查询模板](#常用查询模板)
- [易错点](#易错点)

## 使用边界

- `siyuan sql` 只执行单条只读 `SELECT` 或 `WITH` 查询。
- 查询目标是 SiYuan 运行时维护的 SQLite 索引库，不是直接读写 `.sy` 文档文件。
- 属性视图数据库的行、列、单元格值不要用 SQL 查询；使用 `siyuan database search/get/render/keys/item ...`。
- 所有时间字段示例均为字符串时间戳，常见格式是 `YYYYMMDDHHMMSS`，例如 `20240102153000`。

## 表选择指南

| 任务 | 优先表 | 说明 |
|---|---|---|
| 查块、文档、标题、正文、路径、标签 | `blocks` | 最常用表，绝大多数内容查询从这里开始 |
| 查反链、提及、双链关系 | `refs` + `blocks` | `refs.def_block_id` 是被引用块，`refs.block_id` 是引用所在块 |
| 查书签、自定义属性、块属性 | `attributes` + `blocks` | `attributes.block_id` 关联块 ID |
| 查资源文件引用 | `assets` + `blocks` | `assets.block_id` 关联引用资源的块 |
| 查 PDF 注释引用 | `file_annotation_refs` + `blocks` | 用于文件注释反向定位 |
| 查行内元素、行内链接、标签、高亮 | `spans` + `blocks` | `spans.block_id` 关联所在块 |
| 查索引状态或内部统计 | `stat` | 通常只用于诊断 |

## `blocks` 表

`blocks` 存储所有内容块数据。

| 字段 | 说明 |
|---|---|
| `id` | 内容块 ID |
| `parent_id` | 上级块 ID；文档块通常为空 |
| `root_id` | 顶层文档块 ID |
| `hash` | `content` 字段的 SHA256 校验和 |
| `box` | 笔记本 ID |
| `path` | 内容块所在文档路径，例如 `/doc-id/child-doc-id.sy` |
| `hpath` | 人类可读的文档路径 |
| `name` | 块名称 |
| `alias` | 块别名 |
| `memo` | 块备注 |
| `tag` | 块内标签；文档块为文档标签 |
| `content` | 去除 Markdown 标记后的文本 |
| `fcontent` | 第一个子块去除 Markdown 标记后的文本 |
| `markdown` | 包含 Markdown 标记的文本 |
| `length` | 文本长度 |
| `type` | 块主类型，见 [`blocks.type` 取值](#blockstype-取值) |
| `subtype` | 块子类型，见 [`blocks.subtype` 取值](#blockssubtype-取值) |
| `ial` | 内联属性列表，例如 `{: id="..." updated="..."}` |
| `sort` | 排序权重，数值越小越靠前 |
| `created` | 创建时间，格式通常为 `YYYYMMDDHHMMSS` |
| `updated` | 更新时间，格式通常为 `YYYYMMDDHHMMSS` |

## `blocks.type` 取值

| 值 | 说明 |
|---|---|
| `audio` | 音频块 |
| `av` | 属性表块；不要用 SQL 查询属性视图行列数据 |
| `b` | 引述块 |
| `c` | 代码块 |
| `d` | 文档块 |
| `h` | 标题块 |
| `html` | HTML 块 |
| `i` | 列表项 |
| `iframe` | iframe 块 |
| `l` | 列表块 |
| `m` | 公式块 |
| `p` | 段落块 |
| `query_embed` | 嵌入块 |
| `s` | 超级块 |
| `t` | 表格块 |
| `tb` | 分割线 |
| `video` | 视频块 |
| `widget` | 挂件块 |

## `blocks.subtype` 取值

| 值 | 关联 `type` | 说明 |
|---|---|---|
| `h1` | `h` | 一级标题 |
| `h2` | `h` | 二级标题 |
| `h3` | `h` | 三级标题 |
| `h4` | `h` | 四级标题 |
| `h5` | `h` | 五级标题 |
| `h6` | `h` | 六级标题 |
| `o` | `l` | 有序列表 |
| `u` | `l` | 无序列表 |
| `t` | `l` | 任务列表 |

## `refs` 表

`refs` 存储引用和双链关系。

| 字段 | 说明 |
|---|---|
| `id` | 引用 ID |
| `def_block_id` | 被引用块 ID |
| `def_block_parent_id` | 被引用块的父块 ID |
| `def_block_root_id` | 被引用块所在文档 ID |
| `def_block_path` | 被引用块所在文档路径 |
| `block_id` | 引用所在内容块 ID |
| `root_id` | 引用所在文档块 ID |
| `box` | 引用所在笔记本 ID |
| `path` | 引用所在文档路径 |
| `content` | 引用锚文本 |
| `markdown` | 包含完整 Markdown 标记的引用文本 |
| `type` | 引用类型，例如 `ref_id` |

## `attributes` 表

`attributes` 存储块属性。

| 字段 | 说明 |
|---|---|
| `id` | 属性 ID |
| `name` | 属性名称 |
| `value` | 属性值 |
| `type` | 属性类型 |
| `block_id` | 关联块 ID |
| `root_id` | 关联文档 ID |
| `box` | 笔记本 ID |
| `path` | 文档文件路径 |

## `assets` 表

`assets` 存储资源引用。

| 字段 | 说明 |
|---|---|
| `id` | 引用 ID |
| `block_id` | 引用资源的块 ID |
| `root_id` | 所在文档 ID |
| `box` | 笔记本 ID |
| `docpath` | 文档路径 |
| `path` | 资源文件路径，例如 `assets/name-id.png` |
| `name` | 资源文件名 |
| `title` | 资源标题 |
| `hash` | 资源哈希 |

## `file_annotation_refs` 表

`file_annotation_refs` 存储 PDF 等文件注释引用。

| 字段 | 说明 |
|---|---|
| `id` | 引用 ID |
| `file_path` | 关联文件路径 |
| `annotation_id` | 被引用注释 ID |
| `block_id` | 引用所在内容块 ID |
| `root_id` | 引用所在文档 ID |
| `box` | 笔记本 ID |
| `path` | 引用所在文档路径 |
| `content` | 引用锚文本 |
| `type` | 注释类型 |

## `spans` 表

`spans` 存储行内元素。

| 字段 | 说明 |
|---|---|
| `id` | 行内元素 ID |
| `block_id` | 元素所在内容块 ID |
| `root_id` | 元素所在文档 ID |
| `box` | 笔记本 ID |
| `path` | 元素所在文档路径 |
| `content` | 元素内容 |
| `markdown` | 包含 Markdown 标记的元素内容 |
| `type` | 行内元素类型，见 [`spans.type` 取值](#spanstype-取值) |
| `ial` | 行内属性列表 |

## `spans.type` 取值

| 值 | 说明 |
|---|---|
| `img` | 图片 |
| `tag` | 文档标签 |
| `textmark a` | 链接 |
| `textmark block-ref` | 块引用 |
| `textmark code` | 行内代码 |
| `textmark inline-memo` | 备注 |
| `textmark tag` | 行内标签 |
| `textmark inline-math` | 行内公式 |
| `textmark mark` | 高亮 |
| `textmark em` | HTML tag |
| `textmark s` | HTML tag |
| `textmark strong` | HTML tag |
| `textmark sub` | HTML tag |
| `textmark sup` | HTML tag |
| `textmark u` | HTML tag |

## `stat` 表

`stat` 存储索引库内部状态。

| 字段 | 说明 |
|---|---|
| `key` | 状态键 |
| `value` | 状态值 |

## 常用查询模板

查询所有文档块：

```sql
select * from blocks
where type = 'd'
order by updated desc
limit 100;
```

查询所有二级标题块：

```sql
select * from blocks
where type = 'h' and subtype = 'h2'
order by updated desc
limit 100;
```

查询某文档树内的子文档：

```sql
select * from blocks
where type = 'd' and path like '%/当前文档ID/%'
order by sort asc
limit 100;
```

查询含关键词的段落块：

```sql
select * from blocks
where type = 'p' and markdown like '%关键词%'
order by updated desc
limit 100;
```

查询某块的反链来源块：

```sql
select * from blocks
where id in (
  select block_id from refs
  where def_block_id = '被引用块ID'
)
order by updated desc
limit 100;
```

查询某文档树内所有块被引用的来源块：

```sql
select * from blocks
where id in (
  select block_id from refs
  where def_block_id in (
    select id from blocks
    where path like '%/当前文档ID/%' or id = '当前文档ID'
  )
)
order by updated desc
limit 100;
```

查询某书签下的块：

```sql
select * from blocks
where id in (
  select block_id from attributes
  where name = 'bookmark' and value = '书签名'
)
order by updated desc
limit 100;
```

查询引用某资源文件的块：

```sql
select * from blocks
where id in (
  select block_id from assets
  where path = 'assets/文件名-id.png'
)
order by updated desc
limit 100;
```

查询最近 7 天创建的未完成任务列表：

```sql
select * from blocks
where type = 'l'
  and subtype = 't'
  and created > strftime('%Y%m%d%H%M%S', datetime('now', '-7 day'))
  and markdown regexp '\* \[ \] \S+'
  and parent_id not in (
    select id from blocks
    where subtype = 't'
  )
order by created desc
limit 100;
```

## 易错点

- `content` 是去除 Markdown 标记后的文本；需要匹配链接、引用、任务标记、代码围栏等原始标记时用 `markdown`。
- `root_id` 是所在文档 ID；`parent_id` 是直接父块 ID。
- 文档块的 `parent_id` 通常为空；查询文档树时优先结合 `id`、`path`、`root_id` 判断。
- `path like '%/文档ID/%'` 可查文档树下级，但不包含目标文档本身；需要包含自身时追加 `or id = '文档ID'`。
- 时间字段是字符串时间戳，比较时用同样格式的字符串，例如 `created > '20240101000000'`。
- `LIKE` 中 `%` 匹配任意长度字符串，`_` 匹配单个字符；需要精确匹配 ID 时优先用 `=`。
- `refs.def_block_id` 是被引用块，`refs.block_id` 是引用所在块；写反链查询时不要反用。
- `attributes` 是块属性表，不是属性视图数据库表。属性视图行列数据使用 `database` 指令。
