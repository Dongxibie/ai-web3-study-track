# 关键 Prompt 与配置说明

## 一、Claude Code 的配置说明

我没有写任何自定义 Agent 框架,直接用了 Anthropic 官方的 **Claude Code CLI**(claude-opus-4-7),环境如下:

| 配置项 | 取值 |
| --- | --- |
| 操作系统 | Windows 11 Pro(PowerShell) |
| Agent | Claude Code Opus 4.7,1M context |
| 工具能力 | File ops(Read/Edit/Write/Glob/Grep)、Bash、PowerShell、WebFetch/WebSearch、GitHub MCP |
| GitHub 接入 | 通过 MCP 服务器(`mcp__github__*` 系列工具),只用到读 / 写文件 / 列 commit,没有给 push 权限 |
| 持久记忆 | 文件式 memory(`~/.claude/projects/.../memory/`),只存我同意持久化的偏好 / 项目元信息 |
| 自动化范围 | 仅文件编辑 + commit;**不**做 push、**不**做 git config、**不**接触 .env |

启动方式就是终端里 `claude`,然后开始对话。GitHub MCP 在 Claude Code 启动时自动连,作业期间 *connecting → ready* 是自动完成的。

## 二、关键 Prompt 清单

下面挑出本次作业过程里**真正影响输出质量的 prompt**,按时间顺序列出。原文保留中文与口语化表达 —— 这本身也是本次想验证的一件事:**口语化 prompt + 关键约束**比"写得像 ChatGPT 模板"更有效。

### Prompt 1 · 检查仓库状态(意图驱动型)

```
检查我的 GitHub 链接
```

**为什么有效**:虽然只有 6 个字,但 Claude Code 通过 memory 知道我的仓库是 `Dongxibie/ai-web3-study-track`、daily 笔记规则是 `daily-notes/YYYY-MM-DD.md`,自动做了:
1. WebFetch 仓库主页确认可访问
2. 通过 GitHub MCP 列出 `daily-notes/` 下的所有文件
3. 列出最近的 commit
4. 主动指出"今天还没有 daily note"

**人工把关**:它在动手前没改任何东西,只是"汇报状态"。我看完后才决定下一步。

### Prompt 2 · 上传作业 + 联动 daily-note(组合型)

```
[贴入手写整理的《AI 基础概念卡片》全文,文末附:]

（这是一个单独的作业，它并不属于我们的每日作业。不过我希望你把这个链接加到每日作业中，
在 Demo 里面单独列成一个"AI 生态的初步了解"）
```

**为什么有效**:核心信息是最后那段括号里的指令 —— 它告诉 agent:
- 这份内容是**作业素材**,不是要你修改
- 你需要做**两件**事:存文件 + 在另一个文件里加链接
- 链接要叫"AI 生态的初步了解",且要放在 Demo 区

**人工把关**:Agent 先用 `AskUserQuestion` 工具问了我两个问题(文件位置 / 链接放哪一天),我选完它才动手。这场就是 [human-review-log.md](./human-review-log.md) 里记录的那次。

### Prompt 3 · 生成第二周学习计划表(本次 successful-output)

```
任务:作为我的 AI × Web3 School Learning Agent,根据我 week1 的实际进度
(已建立基础概念、做了 EIP-1559 demo、能解释 LLM/Agent/Workflow),
生成一份第二周学习计划表。

要求:
1. 7 天每日安排,日期从 2026-05-25 开始
2. 每天列出:学习主题、目标产出、AI 协作方式、Solidity/链上动手内容
3. 单列一节"AI 协作边界",写清楚哪些事让 agent 做、哪些事不让做
4. 风险与备份方案要列(如测试币领不到 / Remix 抽风等)
5. 最后说明:此版为 agent 草稿,需对照 Handbook 第二周章节做最终校准
```

**为什么有效**:
- 把**已知背景**(week1 进度)写进去,避免它从零开始猜
- 列了 5 个**结构化要求**,而不是"帮我做个计划"
- 显式标注"草稿、需对照 Handbook 校准" —— 既明确 agent 的能力边界,也提示了下一步人工动作

输出全文见 [successful-output.md](./successful-output.md)。

## 三、Prompt 设计的几条经验

实际用下来,**有效 prompt 的共同点**:

1. **给上下文锚点而不是空泛要求**:"根据我 week1 进度生成 week2 计划" 远好于 "帮我做个学习计划"
2. **结构化输出要求**:列点列表格比 "请详细" 更稳
3. **显式标注 agent 的边界**:让它自己也意识到"这是草稿、要人工对齐",避免它把推断当成定论
4. **不在 prompt 里塞敏感信息**:不贴 .env、不贴 API Key、不贴 mnemonic —— 这是绝对红线

**反过来,容易失败的 prompt**:
- "帮我总结一下" → 输出会很模糊
- "帮我把所有都做了" → agent 容易跳步、也容易越权
- 把 prompt 写得很正式但忘了说目的 → agent 会在错的方向上写很多
