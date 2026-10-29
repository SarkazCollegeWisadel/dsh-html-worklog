# 产物契约 · 单文件 HTML 的硬性结构

这份文件规定「什么样才算合格产物」。写之前先看一遍，写完按这里逐条验收。

## 1. 文件级

- 单文件（`.html`），首行 `<!DOCTYPE html>`，UTF-8。
- `<html lang="zh-CN">`；`<head>` 内至少四件：`<meta charset="utf-8">`、`<meta name="viewport" content="width=device-width, initial-scale=1">`、`<meta name="color-scheme" content="dark">`、`<title>`。
- 全部 CSS 在 `<style>` 内，全部 JS 在 `<script>` 内（**无 `src`**）。
- 体积建议 ≤ 200 KB。超了先砍图片，再砍内容。

## 2. 零外链（硬性）

以下任一出现即不合格：

- `http://` / `https://` / `//cdn…` 形式的资源引用
- `<link rel="stylesheet">`、`<script src>`、`@import`、`url(远端)`
- 网络字体（Google Fonts、字体子集下载）
- `<iframe>` 嵌入

允许：`data:image/svg+xml;utf8,...` 的小图标（≤ 2 KB）。
不允许：base64 位图。

## 3. 数据层

```html
<script id="worklog-data" type="application/json">
{ "meta": {...}, "breakpoints": [...], "ledger": [...], "todos": [...], "timeline": [...] }
</script>
```

- `id="worklog-data"` **必须存在且唯一**。
- 内容必须是**合法 JSON 对象**；顶层五个键 `meta` / `breakpoints` / `ledger` / `todos` / `timeline` 全部必填（空数组可以）。
- JSON 内**不得**出现 `</script>`、`<!--`（会提前截断脚本块）。
- `meta` 必填：`title`、`project`、`owner`、`workspace`、`updated_at`、`timezone`、`round.done`、`round.not_done`。
- 解析失败时页面必须显示**可见的错误提示**，不是白屏。

## 4. 渲染层

- 内联 JS，读到数据后立即渲染；不得 `fetch`、不得依赖网络。
- `localStorage` 一律 `try/catch`：被禁用时降级为默认展开，页面仍完整可读。
- 不引入任何框架与构建产物。

## 5. 顶部固定区

`.hero` 用 `position: sticky; top: 0`，含三块：

1. **当前断点**：≤ 3 条，一条一行，超出就截断（多的放「断点」分区）。
2. **最近更新时间**：ISO + 时区，同时写进 `<time datetime="...">`。
3. **这一轮做完 / 没做完**：两列，各自成列表。

窄屏（375px）下 hero 高度不超过视口 40%，超出则 `max-height` + 内部滚动。

## 6. 四个分区

固定 id 与标题，不得改名或合并：

| 分区 | id | 说明 |
|---|---|---|
| 断点 | `sec-breakpoints` | 能直接接手的下一步 |
| 台账 | `sec-ledger` | 已发生事实 + 证据 |
| 待办 | `sec-todos` | 未做项 + owner + 阻塞 |
| 时间线 | `sec-timeline` | 状态变化点，正序 |

每个分区：`<details class="sec" id="sec-*">` + `<summary>`（标题 + 条数）+ `<div class="body">`。
禁止把某区做成默认折叠的死区——首屏至少「断点」展开。

## 7. 折叠持久化

- key 规则：`worklog.collapsed.<meta.project>.<section-id>`，值 `"0"` = 展开、`"1"` = 折叠。
- 页面加载时读取一次，`toggle` 事件写回。
- 另外写一个 `worklog.lastOpened` 存 `meta.updated_at`，便于人工核对有没有读到旧版。

## 8. 时间

- 存储与显示同一套：ISO 8601 带偏移量，`2026-03-02T21:40:00+08:00`；显示时把 `T` 换成空格。
- **禁止**：刚刚、刚才、昨天、上周、前天、明天、Today、Yesterday、3 天前。
- 时间线固定**正序**（最早的在上）。

## 9. 视觉

- 深色底：`#10`–`#20` 亮度区间（如 `#111319`），面板略亮一档。
- **单一强调色**，全文件只用它一个着色（状态差异靠文字 + 边框 + 字重，不靠第二种颜色）。
- 正文最小 14px，行高 1.6–1.75。
- 文本容器 `max-width: 78ch`；长路径用 `overflow-wrap: anywhere`，不许把页面撑出横向滚动。
- `:focus-visible` 给可见焦点环。

## 10. 骨架

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<meta name="color-scheme" content="dark">
<title>项目名 · 工作笔记</title>
<style>/* 内联，深色 + 单强调色 */</style>
</head>
<body>
<div class="box" id="boot-error" hidden></div>
<header class="hero"><!-- 断点 ≤3 / 更新时间 / 做完·没做完 --></header>
<main>
  <details class="sec" id="sec-breakpoints" open><summary><span>断点</span><span class="count"></span></summary><div class="body"></div></details>
  <details class="sec" id="sec-ledger"><summary><span>台账</span><span class="count"></span></summary><div class="body"></div></details>
  <details class="sec" id="sec-todos"><summary><span>待办</span><span class="count"></span></summary><div class="body"></div></details>
  <details class="sec" id="sec-timeline"><summary><span>时间线</span><span class="count"></span></summary><div class="body"></div></details>
</main>
<script id="worklog-data" type="application/json">{}</script>
<script>/* 解析 → 渲染 → 记忆折叠 */</script>
</body>
</html>
```
