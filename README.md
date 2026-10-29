# dsh-html-worklog

把长会话的进度写成**一份单文件、断网可开**的 HTML 工作笔记。给 agent（DSH / Codex / Claude Code）用的 Skill。

## 解决什么问题

长会话结束时，最贵的不是没做完，而是**下次回来接不上**。Markdown 笔记越写越长、越读越找不着重点；在线文档要联网、要账号、发出去还得对方有权限。

这个 skill 的产物是**一个 HTML 文件**：双击就开、断网能看、手机宽度能读、能直接丢给对方。四个分区固定——断点、台账、待办、时间线——每区回答一个不同的问题。

## 结构

```
dsh-html-worklog/
  SKILL.md                  # 主入口：什么时候用、硬契约、更新方式、验收清单
  references/
    html-contract.md        # 产物契约：单文件 HTML 的硬性结构（含骨架）
    sections.md             # 分区规范：四区各写什么、颗粒度、反例
  examples/
    sample-worklog.html     # 可打开的真样例（内容全部脱敏虚构）
  README.md
  LICENSE
```

## 安装

把整个目录拷进宿主的 skills 根，**目录名必须与 `SKILL.md` 的 `name` 一致**：

- **DSH**：`$DSH_HOME/skills/dsh-html-worklog/`（本机通常是 `D:\tool\dsh-desktop-data\skills\`）
- **Codex**：`~/.codex/skills/dsh-html-worklog/` 或项目内 `.agents/skills/`
- **Claude Code**：`~/.claude/skills/dsh-html-worklog/`

`SKILL.md` 三宿主通用，差异只在「写到哪」。

## 用

说「给我一份工作笔记 / 把这轮进度写成网页 / 留个断点」，agent 会：

1. 复制 `examples/sample-worklog.html` 的骨架
2. 把数据段换成本轮真实内容（断点 ≤3 条，台账带证据，待办带 owner，时间线只记状态变化）
3. 整文件写到指定位置，`*.bak-<时间戳>` 备份上一版
4. 回读校验：JSON 合法、四区 id 齐全、无外链、无相对时间

## 验收

见 `SKILL.md` 第 5 节清单。最核心的三条：断网能开、断点 ≤3 条、没有相对时间。

## 许可

MIT，见 `LICENSE`。
