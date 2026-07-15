# workspace

`workspace` 是已注册工作区列表和当前工作区信息查询入口。

## 子命令一览

- 工作区查询：`list`、`info`

## 子命令详述

| 子命令 | 用途 | 语法 | 关键参数 | 输出/副作用 | 备注 |
|---|---|---|---|---|---|
| `list` | 列出已注册的 workspace 路径 | `siyuan workspace list` | 无 | 只读；默认逐行输出去重后的 workspace 路径，`-f json` 时返回 `{path,name}` 数组 | `workspace` 子命令会跳过普通 workspace 校验，因此该命令不需要 `-w <workspace>` |
| `info` | 显示当前或默认 workspace 信息 | `siyuan workspace info -w <workspace>` | `-w, --workspace` | 只读；默认输出路径、版本和有效性，`-f json` 时返回同等字段对象 | 未传 `-w` 时依次使用 `SIYUAN_WORKSPACE_PATH`、已注册列表最后一项、用户主目录下 `SiYuan` 作为候选路径 |

## 备注

- `workspace` 下的命令不会初始化数据库，也不会执行普通 CLI 结束时的 SQL 队列刷新。
- `list` 会按路径小写形式去重；显示值保留原始路径。
