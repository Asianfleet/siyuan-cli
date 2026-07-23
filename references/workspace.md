# workspace

`workspace` 是已注册工作区列表和当前工作区信息查询入口。

## 子命令一览

- 工作区查询：`list`、`info`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `list` | 列出已注册的 workspace 路径 | `siyuan workspace list` | 无 | 只读；默认按注册顺序逐行输出去重后的 workspace 路径，`-f json` 时返回 `[{path,name}]` 数组 | `workspace` 子命令跳过普通 workspace 校验，因此不需要 `-w <workspace>`；读取 `~/.config/siyuan/workspace.json` 失败或 JSON 解析失败时会直接报错；`--dry-run` 与普通执行无差异 |
| `info` | 显示当前或默认 workspace 信息 | `siyuan workspace info -w <workspace>` | `-w, --workspace`：可选；指定被检查的目标 workspace；未传时检查 CLI 解析出的默认 workspace；显式传入优先 | 只读；默认输出 `Path / Version / IsValid`，`-f json` 时返回同等字段对象 | 即使目标路径不存在或不是合法工作区，也会输出 `IsValid: false`，不会报错；`info` 只是检查路径是否像工作区，不会校验 `workspace.json` 是否已登记；`--dry-run` 与普通执行无差异 |

## 通用注意事项

- `workspace` 下的命令不会初始化数据库，也不会执行普通 CLI 结束时的 SQL 队列刷新。
- `list` 的去重规则以路径规范化后的小写值为准；非 Windows 平台按原始字符串去重；显示值保留读取到的路径写法。
