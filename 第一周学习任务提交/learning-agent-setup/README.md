# Week 1｜AI 向任务｜Learning Agent Setup

## 选用的 Agent / AI 工具

**Claude Code · Opus 4.7**(本作业全程在 PowerShell 终端里运行)。

选这个的理由:
- 它本身就是一个 Agent(LLM + Tool Use + Guardrails),正好可以用「它自己」来理解 week 1 卡片里讲过的概念。
- 通过 MCP 直接连了 GitHub,可以让它**帮我做仓库操作的草稿**,我最终人工确认 commit / push,符合作业要求里「敏感与外部操作必须人工把关」的约束。
- 已经在 week 1 的 demo 里用过(cards / gas-calculator / quiz 都是 Claude Code 协助写的),所以延续同一个工作流不会浪费 context。

## 我让 Agent 帮我做的学习任务

| 任务 | Agent 输出 | 人工把关 |
| --- | --- | --- |
| 检查 GitHub 仓库状态、列出 daily-notes 进度 | 读取 commit history、列出文件 | 由我确认仓库 owner 与目标路径 |
| 把手写整理的《AI 基础概念卡片》落到 GitHub 上 | 生成 markdown + commit | 我先选定目录、再让它写 commit |
| 起草今日 daily-note(2026-05-24) | 按 2026-05-22 的风格生成中文段落式 note | 我审阅后保留,可继续追加学习实操 |
| 删除卡片末尾「AI 辅助说明」一节 | 重写文件、保留 sha 防止冲突 | 由我下达明确指令 |
| **生成第二周学习计划表** | 7 天计划 + AI 协作边界清单 | 见 [successful-output.md](./successful-output.md),由我对照 Handbook 进一步校准 |

## 本作业的 4 个文件

| 文件 | 内容 |
| --- | --- |
| [README.md](./README.md) | 本说明(选型 + 做了什么 + 文件导航) |
| [key-prompts.md](./key-prompts.md) | 关键 prompt 与 Claude Code 的配置说明 |
| [successful-output.md](./successful-output.md) | 一次成功输出记录:让 Agent 生成第二周学习计划表 |
| [human-review-log.md](./human-review-log.md) | 一次人工复核 / 修正 Agent 建议的记录 |

## 安全声明

本目录不含 API Key、token、私钥、助记词、`.env` 文件或其他任何敏感信息。

涉及外部副作用的操作(创建文件、commit、push、删除文件)在执行前都先由我确认目录与措辞;涉及钱包签名 / 转账 / 授权 / 合约写入 / API Key / 助记词的操作,Claude Code 从未代为执行,也未接触过这类素材。

## Agent 的边界(本任务实践到的)

✅ 它可以做的:
- 读取公开仓库内容、解释项目结构
- 起草 markdown、生成 commit message 草稿
- 在我确认目录后写文件、commit(无 push 权限到上游需要的额外凭证)
- 把复杂操作拆成小任务清单,在每步前等我确认

❌ 它**没有**做、也**不应该**做的:
- 自动 push 到上游(目前由我在本地手动 push,或通过 GitHub UI 确认)
- 接触 `.env` / API Key / 助记词 / 任何私钥
- 自动签名链上交易、自动发起转账或合约调用
- 在没问我之前替我决定文件该放在哪个目录、写在哪一天的 daily-note
