# 属性视图数据模型

本文说明 `siyuan database` 背后的 Attribute View 结构。命令语法和副作用仍以 [../database.md](../database.md) 为准。

## 目录

- [使用边界](#使用边界)
- [核心 ID 速查](#核心-id-速查)
- [字段类型约定](#字段类型约定)
- [属性视图结构](#属性视图结构)
- [字段、行与单元格](#字段行与单元格)
- [视图结构](#视图结构)
- [渲染结果结构](#渲染结果结构)
- [字段类型与值结构](#字段类型与值结构)
- [字段配置子结构](#字段配置子结构)
- [值子结构](#值子结构)
- [视图配置取值](#视图配置取值)
- [过滤、排序、分组与计算](#过滤排序分组与计算)
- [新增行模板](#新增行模板)
- [常见写入约束](#常见写入约束)

## 使用边界

- 只记 `database` 命令语法时，不用读本文件。
- 需要理解 `--av`、`--view`、`--key`、`--item`、`--block`、`--value` 的含义时，先读本文件。
- 需要写 `item update`、`item add`、`batch` 更新或解析 `render -f json` 输出时，必须读本文件。

## 核心 ID 速查

| 名称 | 含义 |
|---|---|
| `avID` | 属性视图 ID，也就是数据库本体 ID |
| `blockID` | 承载数据库块或绑定块的块 ID |
| `viewID` | 当前属性视图里的某个视图 ID |
| `keyID` | 字段 ID |
| `itemID` | 行条目 ID |
| `rowID` | 旧名，已废弃；当前以 `itemID` 为准 |
| `valueID` | 某个单元格值对象的 ID |

两个容易混淆的点：

- `Value.BlockID` 不是绑定块 ID，而是行条目 ID。
- `Value.Block.ID` 才是绑定块 ID；独立行会把这个字段留空。

## 字段类型约定

- `string`：字符串。
- `bool`：布尔值。
- `int` / `int64`：整数；时间戳字段通常是 Unix milliseconds。
- `number`：JSON number；源码里通常是 Go `float64`。
- `[]T`：数组。
- `T?`：可省略或为 `null`。
- `object`：对象；具体字段见下方对应结构。

## 属性视图结构

`AttributeView` 是整个数据库对象。

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `spec` | `int` | 格式版本 |
| `id` | `string` | `avID` |
| `name` | `string` | 数据库名称 |
| `keyValues` | `KeyValues[]` | 全部字段及其行值 |
| `keyIDs` | `string[]` | 字段顺序 |
| `viewID` | `string` | 当前视图 ID |
| `views` | `View[]` | 视图列表 |
| `newItemTemplates` | `NewItemTemplate[]?` | 新增条目模板 |
| `defaultTemplateID` | `string?` | 默认新增条目模板 ID |

`KeyValues` 是一组字段和值的聚合：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `key` | `Key` | 字段定义 |
| `values` | `Value[]?` | 该字段下所有行的单元格值 |

## 字段、行与单元格

`Key` 描述字段定义。

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `id` | `string` | 字段 ID |
| `name` | `string` | 字段名 |
| `type` | `KeyType` | 字段类型，见 [字段类型与值结构](#字段类型与值结构) |
| `icon` | `string` | 字段图标 |
| `desc` | `string` | 字段描述 |
| `options` | `SelectOption[]?` | 单选/多选选项 |
| `numberFormat` | `NumberFormat` | 数字格式 |
| `template` | `string` | 模板字段内容 |
| `relation` | `Relation?` | 关联字段配置 |
| `rollup` | `Rollup?` | 汇总字段配置 |
| `date` | `Date?` | 日期字段配置 |
| `created` | `Created?` | 创建时间字段配置 |
| `updated` | `Updated?` | 更新时间字段配置 |

`Value` 是单元格值对象。

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `id` | `string?` | 单元格值 ID |
| `keyID` | `string?` | 所属字段 ID |
| `blockID` | `string?` | 所属行条目 ID |
| `type` | `KeyType?` | 字段类型 |
| `isDetached` | `bool?` | 是否独立行；仅主键值使用 |
| `createdAt` / `updatedAt` | `int64?` | 值对象时间戳，Unix milliseconds |
| `block` | `ValueBlock?` | 主键值 |
| `text` | `ValueText?` | 文本值 |
| `number` | `ValueNumber?` | 数字值 |
| `date` | `ValueDate?` | 日期值 |
| `mSelect` | `ValueSelect[]?` | 单选/多选值 |
| `url` | `ValueURL?` | URL 值 |
| `email` | `ValueEmail?` | Email 值 |
| `phone` | `ValuePhone?` | 电话值 |
| `mAsset` | `ValueAsset[]?` | 资源值 |
| `template` | `ValueTemplate?` | 模板值 |
| `created` | `ValueCreated?` | 创建时间值 |
| `updated` | `ValueUpdated?` | 更新时间值 |
| `checkbox` | `ValueCheckbox?` | 复选框值 |
| `relation` | `ValueRelation?` | 关联值 |
| `rollup` | `ValueRollup?` | 汇总值 |

`Value.Block` 的关键字段：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `id` | `string?` | 绑定块 ID |
| `content` | `string` | 主键显示文本 |
| `icon` | `string?` | 块图标 |
| `created` / `updated` | `int64?` | 绑定块值的时间戳 |

对主键值而言：

- 绑定块行：`isDetached = false`，`block.id` 有值。
- 独立行：`isDetached = true`，`block.id` 为空。
- `block.content` 可以是动态锚文本，也可以是显式静态文本。

## 视图结构

`View` 是数据库的一个视图。

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `id` | `string` | 视图 ID |
| `name` | `string` | 视图名称 |
| `icon` | `string` | 视图图标 |
| `desc` | `string` | 视图描述 |
| `hideAttrViewName` | `bool` | 是否隐藏数据库名 |
| `filters` | `ViewFilter[]?` | 过滤条件 |
| `sorts` | `ViewSort[]?` | 排序条件 |
| `pageSize` | `int` | 分页大小 |
| `type` | `LayoutType`：`table` / `gallery` / `kanban` | 布局类型 |
| `table` | `LayoutTable?` | 表格布局配置 |
| `gallery` | `LayoutGallery?` | 卡片布局配置 |
| `kanban` | `LayoutKanban?` | 看板布局配置 |
| `itemIds` | `string[]?` | 项目 ID 顺序 |
| `group` | `ViewGroup?` | 分组规则 |
| `groupCreated` | `int64` | 分组生成时间 |
| `groups` | `View[]?` | 分组后的子视图 |
| `groupItemIds` | `string[]` | 分组项目 ID |
| `groupCalc` | `GroupCalc?` | 分组计算规则 |
| `groupKey` | `Key?` | 分组字段 |
| `groupVal` | `Value?` | 分组值 |
| `groupFolded` | `bool` | 分组是否折叠 |
| `groupHidden` | `int`：`0` 显示，`1` 空白隐藏，`2` 手动隐藏 | 分组隐藏状态 |
| `groupSort` | `int` | 手动分组排序值 |

## 渲染结果结构

`renderAttributeView`、`getAttributeView`、`renderSnapshotAttributeView` 一类接口返回的视图数据，通常会落到以下结构：

| 视图 | 项目集合 | 字段集合 | 计数 |
|---|---|---|---|
| 表格 | `rows` | `columns` | `rowCount` |
| 卡片 | `cards` | `fields` | `cardCount` |
| 看板 | `cards` | `fields` | `cardCount` |

表格项：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `id` | `string` | 行 ID，也就是 `itemID` |
| `cells` | `TableCell[]` | 单元格列表 |

卡片/看板项：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `id` | `string` | 卡片 ID，也就是 `itemID` |
| `values` | `GalleryFieldValue[]` 或 `KanbanFieldValue[]` | 字段值列表 |
| `coverURL` | `string` | 封面链接 |
| `coverContent` | `string` | 封面内容 |

## 字段类型与值结构

`KeyType` 的常见取值：

| 类型 | 说明 | 值结构主字段 |
|---|---|---|
| `block` | 主键 | `block` |
| `text` | 文本 | `text` |
| `number` | 数字 | `number` |
| `date` | 日期 | `date` |
| `select` | 单选 | `mSelect` |
| `mSelect` | 多选 | `mSelect` |
| `url` | 链接 | `url` |
| `email` | 邮箱 | `email` |
| `phone` | 电话 | `phone` |
| `mAsset` | 资源 | `mAsset` |
| `template` | 模板 | `template` |
| `created` | 创建时间 | `created` |
| `updated` | 更新时间 | `updated` |
| `checkbox` | 复选框 | `checkbox` |
| `relation` | 关联 | `relation` |
| `rollup` | 汇总 | `rollup` |
| `lineNumber` | 行号 | 通常只读 |

结构要点：

- `select` 实际复用 `mSelect` 容器，只保留第一个选项。
- `relation` 的值里保存 `blockIDs`，`contents` 多数时候由内核自动回填。
- `rollup` 的值完全依赖关联字段与目标字段，通常不应手工拼装。
- `created` / `updated` / `lineNumber` 一般由内核生成或计算，不按普通输入字段处理。

## 字段配置子结构

`SelectOption`：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `name` | `string` | 选项名称 |
| `color` | `string`，常见 `1`-`14` | 选项颜色 |
| `desc` | `string` | 选项描述 |

`NumberFormat` 取值：

| 取值 | 说明 |
|---|---|
| `""` | 无格式 |
| `commas` | 千分位 |
| `percent` | 百分比 |
| `USD` / `CNY` / `EUR` / `GBP` / `JPY` / `RUB` / `INR` / `KRW` / `TRY` / `CAD` / `CHF` / `THB` / `AUD` / `HKD` / `TWD` / `MOP` / `SGD` / `NZD` / `ILS` / `SKK` | 货币格式 |

`Relation`：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `avID` | `string` | 关联目标属性视图 ID |
| `isTwoWay` | `bool` | 是否双向关联 |
| `backKeyID` | `string` | 双向关联回链字段 ID |

`Rollup`：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `relationKeyID` | `string` | 本库里的关联字段 ID |
| `keyID` | `string` | 目标库里的被汇总字段 ID |
| `calc` | `RollupCalc` | 汇总计算方式和结果 |

`Date` / `Created` / `Updated`：

| 结构 | 字段 | 类型 / 取值 | 作用 |
|---|---|---|---|
| `Date` | `autoFillNow` | `bool` | 默认填充当前时间 |
| `Date` | `fillSpecificTime` | `bool` | 默认值是否包含具体时间 |
| `Created` | `includeTime` | `bool` | 是否显示具体时间 |
| `Updated` | `includeTime` | `bool` | 是否显示具体时间 |

## 值子结构

| 结构 | 字段 | 类型 / 取值 | 作用 |
|---|---|---|---|
| `ValueText` | `content` | `string` | 文本 |
| `ValueURL` | `content` | `string` | URL |
| `ValueEmail` | `content` | `string` | 邮箱 |
| `ValuePhone` | `content` | `string` | 电话 |
| `ValueTemplate` | `content` | `string` | 模板渲染内容 |
| `ValueCheckbox` | `checked` | `bool` | 是否勾选 |

`ValueNumber`：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `content` | `number` | 数字值 |
| `isNotEmpty` | `bool` | 是否有值；`false` 时按空值处理 |
| `format` | `NumberFormat` | 数字格式 |
| `formattedContent` | `string` | 内核生成的格式化显示文本 |

`ValueDate`：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `content` | `int64` | 起始时间，Unix milliseconds |
| `isNotEmpty` | `bool` | 起始时间是否有值 |
| `hasEndDate` | `bool` | 是否有结束时间 |
| `isNotTime` | `bool` | 是否仅日期、不含时间 |
| `content2` | `int64` | 结束时间，Unix milliseconds |
| `isNotEmpty2` | `bool` | 结束时间是否有值 |
| `formattedContent` | `string` | 内核生成的格式化显示文本 |

`ValueSelect`：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `content` | `string` | 选项名 |
| `color` | `string`，常见 `1`-`14` | 选项颜色 |

`ValueAsset`：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `type` | `file` / `image` | 资源类型；链接也用 `file` |
| `name` | `string` | 资源名 |
| `content` | `string` | 资源路径或链接 |

`ValueCreated` / `ValueUpdated`：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `content` | `int64` | 起始时间，Unix milliseconds |
| `isNotEmpty` | `bool` | 起始时间是否有值 |
| `content2` | `int64` | 结束时间，Unix milliseconds |
| `isNotEmpty2` | `bool` | 结束时间是否有值 |
| `formattedContent` | `string` | 内核生成的格式化显示文本 |

`ValueRelation` / `ValueRollup`：

| 结构 | 字段 | 类型 / 取值 | 作用 |
|---|---|---|---|
| `ValueRelation` | `blockIDs` | `string[]` | 关联的目标 item ID / 块 ID 列表 |
| `ValueRelation` | `contents` | `Value[]` | 内核渲染出的关联显示内容，写入时通常不要传 |
| `ValueRollup` | `contents` | `Value[]` | 汇总结果，通常只读 |

## 视图配置取值

`BaseLayout`：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `spec` | `int` | 布局格式版本 |
| `id` | `string` | 布局 ID |
| `showIcon` | `bool` | 是否显示字段图标 |
| `wrapField` | `bool` | 字段内容是否换行 |
| `filters` / `sorts` / `pageSize` | 已废弃 | 过滤、排序、分页已迁移到 `View` |

`LayoutTable`：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `columns` | `ViewTableColumn[]` | 表格列 |
| `rowIds` | 已废弃 | 行顺序旧字段，改用 `View.itemIds` |

`ViewTableColumn`：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `id` | `string` | 字段 ID |
| `wrap` | `bool` | 是否换行 |
| `hidden` | `bool` | 是否隐藏 |
| `desc` | `string?` | 字段描述 |
| `calc` | `FieldCalc?` | 列计算 |
| `pin` | `bool` | 是否固定列 |
| `width` | `string` | 列宽 |
| `align` | `""` / `left` / `center` / `right` | 对齐方式 |

`LayoutGallery` 和 `LayoutKanban` 共享的主要取值：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `coverFrom` | `0` 无封面，`1` 内容图，`2` 资源字段，`3` 内容块 | 封面来源 |
| `coverFromAssetKeyID` | `string?` | 资源字段 ID |
| `cardAspectRatio` | `0` 16:9，`1` 9:16，`2` 4:3，`3` 3:4，`4` 3:2，`5` 2:3，`6` 1:1 | 卡片宽高比 |
| `cardSize` | `0` 小，`1` 中，`2` 大 | 卡片尺寸 |
| `fitImage` | `bool` | 图片是否适应封面 |
| `displayFieldName` | `bool` | 是否显示字段名 |
| `fields` | `ViewGalleryCardField[]` 或 `ViewKanbanField[]` | 卡片字段 |
| `fillColBackgroundColor` | `bool` | 看板专用，是否填充列背景 |

## 过滤、排序、分组与计算

`ViewFilter`：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `column` | `string` | 字段 ID，叶子节点有效 |
| `quantifier` | `""` / `Any` / `All` / `None` | 多值字段量词；空值等同 `Any` |
| `operator` | `FilterOperator` | 操作符 |
| `value` | `Value?` | 过滤值 |
| `relativeDate` / `relativeDate2` | `RelativeDate?` | 相对日期 |
| `combination` | `and` / `or` | 分组节点组合方式 |
| `filters` | `ViewFilter[]?` | 子过滤节点 |

`FilterOperator` 取值：

| 取值 | 说明 |
|---|---|
| `=` | 等于 |
| `!=` | 不等于 |
| `>` / `>=` / `<` / `<=` | 数字或时间比较 |
| `Contains` | 包含 |
| `Does not contains` | 不包含 |
| `Is empty` | 为空 |
| `Is not empty` | 非空 |
| `Starts with` | 以某内容开头 |
| `Ends with` | 以某内容结尾 |
| `Is between` | 在区间内 |
| `Is true` / `Is false` | 复选框真假 |

`RelativeDate`：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `count` | `int` | 数量 |
| `unit` | `0` 天，`1` 周，`2` 月，`3` 年 | 单位 |
| `direction` | `-1` 前，`0` 当前，`1` 后 | 相对方向 |

`ViewSort`：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `column` | `string` | 字段 ID |
| `order` | `ASC` / `DESC` | 排序方向 |

`ViewGroup`：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `field` | `string` | 分组字段 ID |
| `method` | `0` 按值，`1` 数字范围，`2` 相对日期，`3` 按天，`4` 按周，`5` 按月，`6` 按年 | 分组方式 |
| `range` | `GroupRange?` | 数字范围配置 |
| `order` | `0` 升序，`1` 降序，`2` 手动，`3` 按选择字段选项顺序 | 分组排序 |
| `hideEmpty` | `bool` | 是否隐藏空分组 |

`GroupRange`：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `numStart` | `number` | 数字范围起点 |
| `numEnd` | `number` | 数字范围终点 |
| `numStep` | `number` | 数字范围步长 |

`CalcOperator` 取值：

| 取值 | 说明 |
|---|---|
| `""` | 无计算 |
| `Unique values` | 唯一值 |
| `Count all` | 统计全部 |
| `Count values` | 统计有值 |
| `Count unique values` | 统计唯一值数量 |
| `Count empty` / `Count not empty` | 统计空值 / 非空 |
| `Percent empty` / `Percent not empty` / `Percent unique values` | 百分比统计 |
| `Sum` / `Average` / `Median` | 求和 / 平均 / 中位数 |
| `Min` / `Max` / `Range` | 最小 / 最大 / 范围 |
| `Earliest` / `Latest` | 最早 / 最晚 |
| `Checked` / `Unchecked` | 勾选 / 未勾选数量 |
| `Percent checked` / `Percent unchecked` | 勾选 / 未勾选百分比 |
| `Template` | 自定义模板统计 |

`FieldCalc` / `GroupCalc`：

| 结构 | 字段 | 类型 / 取值 | 作用 |
|---|---|---|---|
| `FieldCalc` | `operator` | `CalcOperator` | 计算操作 |
| `FieldCalc` | `result` | `Value?` | 计算结果 |
| `FieldCalc` | `template` | `string?` | 自定义模板统计内容 |
| `GroupCalc` | `field` | `string` | 分组计算字段 ID |
| `GroupCalc` | `calc` | `FieldCalc` | 分组计算规则和结果 |

常用 `Value` 片段：

`type` 可以显式写入，但必须和目标 `keyID` 对应字段类型一致。更新已有单元格时，内核已经知道字段类型，下面示例保留最小可读结构。

```json
{"text":{"content":"hello"}}
```

```json
{"number":{"content":1,"isNotEmpty":true}}
```

```json
{"mSelect":[{"content":"A","color":"1"}]}
```

```json
{"date":{"content":1700000000000,"isNotEmpty":true,"isNotTime":true}}
```

```json
{"checkbox":{"checked":true}}
```

```json
{"relation":{"blockIDs":["20240101000000-abcdefg"]}}
```

```json
{"mAsset":[{"type":"file","name":"a.png","content":"assets/a.png"}]}
```

## 新增行模板

`NewItemTemplate` 用于新增条目时预填数据。

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `id` | `string` | 模板 ID |
| `name` | `string` | 模板名 |
| `icon` | `string?` | 模板图标；非文档目标会被清空 |
| `targetType` | `detached` / `document` | 创建独立行或文档行 |
| `primaryKeyTemplate` | `string?` | 主键模板 |
| `fieldValues` | `map<keyID, NewItemFieldValue>?` | 字段默认值 |
| `saveLocation` | `NewItemSaveLocation?` | 文档型模板的保存位置 |
| `contentTemplatePath` | `string?` | 内容模板路径 |

`fieldValues` 里的每个字段值包含：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `mode` | `static` / `currentTime` | 静态默认值或当前时间 |
| `value` | `Value?` | 静态默认值；`currentTime` 模式通常不写 |

`NewItemSaveLocation`：

| 字段 | 类型 / 取值 | 作用 |
|---|---|---|
| `boxID` | `string?` | 笔记本 ID；为空且对象存在时表示当前数据库实例所在笔记本 |
| `pathTemplate` | `string` | 文档保存路径模板 |

## 常见写入约束

- `item add` 的绑定行和独立行走的是不同路径；`--detached` 会生成独立行。
- `item update` 的 `--value` 需要传能反序列化成 `av.Value` 的 JSON 片段，而不是随意结构。
- `rowID` 只是旧名，当前文档统一写 `itemID`。
- 单选/多选更新时，内核会校正候选值并去重。
- 关联字段更新时，双向关联可能联动目标数据库。
- `render -f json` 输出里看到的分组、排序、分页字段，很多是视图状态，不是行本身的数据。
