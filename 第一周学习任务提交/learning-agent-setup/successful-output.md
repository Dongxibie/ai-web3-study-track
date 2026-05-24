# 成功输出记录｜让 Agent 生成《第二周学习计划表》

## 输入 Prompt

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

## Agent 在生成前做了什么

它没有直接答 —— 而是先做了三件事:

1. 通过 GitHub MCP 读了 `第一周学习任务提交/README.md`,搞清楚 week 1 实际覆盖的范围(EIP-1559 demo、cards、quiz)
2. 读了 `resources.md`,看到我已经收藏了 Solidity Docs / Cryptozombies / Patrick Collins / Foundry,所以方向可以直接对准 Solidity 实操
3. 尝试 fetch `https://aiweb3.school/zh/handbook/` 失败(403,WebFetch 被 Cloudflare 拦),所以在输出末尾**显式标注此版为草稿、需对照 Handbook 校准**

这是这次输出最让我觉得"agent 没在乱编"的地方 —— 它意识到自己缺少 handbook 数据时,选择**承认不确定 + 让我去补**,而不是直接编一个看起来合理但实际偏题的计划。

---

## Agent 输出全文

# 第二周学习计划表

> **主题**: 从读交易 → 写部署第一个合约 → 让 AI 当审计员
> **日期**: 2026-05-25(周一) — 2026-05-31(周日)
> ⚠ **本版为 agent 草稿**,需对照 [Handbook 第二周章节](https://aiweb3.school/zh/handbook/) 做最终校准。Cloudflare 临时拦了 WebFetch,本计划基于 week 1 实际进度 + `resources.md` 推断生成。

## 本周核心目标

1. 把 week 1 学到的「钱包 / 签名 / Gas」从理论变成肌肉记忆 —— 在 Sepolia **真的发出去一笔交易**、**真的部署一份合约**。
2. **Solidity 入门**:能读懂 < 50 行的简单合约,能自己写 < 30 行的第一份合约。
3. **AI Agent 协作升级**:从「让 AI 写 markdown 总结」升级到「让 AI 审合约 + 解释 tx」。
4. 至少 1 个新 demo 进仓库,与 week 1 形成连续叙事。

## 每日安排

| 日期 | 主题 | 目标产出 | AI 协作方式 |
| --- | --- | --- | --- |
| **05-25 周一** | Solidity 语法入门 + Remix 跑通 | `demos/week2-day1/simple-storage.sol`<br>+ daily-note 解释 `pragma` / `contract` / `function visibility` / `state variable` | 让 Claude 解释每个关键字的含义,但合约**自己手敲**(不要复制粘贴) |
| **05-26 周二** | 部署到 Sepolia 测试网 | MetaMask 装好、领到 Sepolia 测试币、把昨天的合约部署上去<br>+ daily-note 贴 Etherscan tx 链接 + 截图 | 让 Claude 帮你**解读 deployment tx** 的字段(to=null、input=bytecode、status=1 含义) |
| **05-27 周三** | viem 读链上数据 | `demos/week2-day3/read-storage/`(简单前端)<br>用 viem 调 SimpleStorage.get() 显示在网页上 | 让 Claude 写 viem `readContract` 的初稿,你**对照官方文档**核对参数(client / abi / functionName) |
| **05-28 周四** | **让 AI 审第一个合约** | `notes/week2-ai-review.md`:故意写一个有 owner 漏检查的合约,让 Claude 做安全 review,记录它**找到**和**没找到**的 bug | 这一天的重点就是用 agent —— 但要对照 OpenZeppelin 同类合约校验它的判断 |
| **05-29 周五** | ERC20 基础 | 用 OpenZeppelin Wizard 生成 ERC20、Remix 部署到 Sepolia、向小号转 100 个币<br>+ daily-note 贴转账 tx 链接 | 让 Claude 解释 `_mint` / `transferFrom` / `allowance` / **`approve` 攻击向量**(无限授权风险) |
| **05-30 周六** | 整合 demo:Counter App | `demos/week2-counter-app/`:Counter 合约 + viem 前端(读 + 写)<br>用户连钱包后能 `increment()` | 让 Claude 把前后端串起来,你**手动测**一次 happy path + 一次错误路径(用户拒签) |
| **05-31 周日** | 总结 + week2 提交 | `第二周学习任务提交/README.md`,沿用 week1 的「问题 / 交互 / 输入输出 / AI vs 人工 / 限制」5 段格式 | 让 Claude 起草 README,你**手动重写**「不可靠点」与「下一步改进」两节 |

## AI 协作边界(本周特别注意)

✅ **让 Agent 做的事**:
- 解释 Solidity 语法 / EIP 规范 / RPC 调用
- 生成合约脚手架(你再改、不要直接用)
- 帮你做 code review 找 bug
- 写 README 初稿、整理 daily-note
- 把 etherscan 上的复杂 tx 翻译成人话

❌ **不让 Agent 做的事**:
- 自动发交易 / 自动签名
- 接触助记词 / 私钥 / API Key
- 自动 `git push`(commit 可以,push 要本地确认)
- 创建 / 销毁链上资产(就算是 testnet 也要人工点确认)
- 把"草稿"假装成"定论" —— 它生成的合约必须经过你手动测过 + 对照官方实现

## 风险与备份

| 风险 | 备份方案 |
| --- | --- |
| Sepolia 测试币领不到 | 切换到 Holesky / Base Sepolia,水龙头多一些 |
| Remix 网络抽风 | 本地装 Anvil(Foundry 自带)或 Hardhat node |
| MetaMask 装不上 / 浏览器不支持 | 换 Rabby(同样支持 EIP-1193) |
| AI 审合约漏了关键 bug | 至少手动对照 OpenZeppelin Contracts 同类合约;或交叉用 Slither 静态分析 |
| 本周时间不够 7 天 | 优先保 D1 / D2 / D4 / D7(语法、部署、AI 审计、提交),D3/D5/D6 可顺延 |

## 与 Week 1 的衔接

| Week 1 学到 | Week 2 怎么用上 |
| --- | --- |
| EIP-1559 baseFee / tip / maxFee | D2 部署时观察 Etherscan 上实际的 baseFee / 你出的 tip / burn 金额 |
| 签名 ≠ 暴露私钥(ecrecover) | D4 review 时检查合约里有没有"重复 nonce"风险 |
| LLM / Agent / Workflow | D4 整周都在体会:**Agent vs LLM** 的区别就是 "能不能动文件、能不能调外部工具" |
| Guardrails | 本周的「不让 agent 做的事」清单,就是我自己给 agent 加的 guardrails |

---

## 后续校验动作

- [ ] 我去 Handbook 第二周章节核对官方主题,与本计划逐项对齐
- [ ] 周一开始执行,如有偏差在 daily-note 里记录修正
- [ ] 周日提交时,在 `第二周学习任务提交/README.md` 里回顾这份草稿的命中率

## 提交元数据

| 项 | 值 |
| --- | --- |
| 生成时间 | 2026-05-24 |
| Agent | Claude Code · Opus 4.7 |
| Commit | 本文件所在 commit(见 git log) |
| 上游约束 | 未涉及 push / 签名 / 私钥 / API Key |
