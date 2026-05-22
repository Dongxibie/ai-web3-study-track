# 多工具对比：同一个 Week 1 任务在 Claude Code / Codex / Hermes 上的表现

## 背景

Week 1 的可交互产物（三张概念卡片 + EIP-1559 Gas 计算器 + 9 题 quiz）我先用 **Claude Code (Opus 4.7)** 完成了基线版本，见 `demos/week1-interactive/`。

这份文档的目标：把同一份 prompt 分别喂给 Codex 和 Hermes（或其他工具），对比 5 个维度的差异，避免被单一工具的"风格"绑架认知。

---

## 统一 prompt（直接复制到下一个工具的对话框）

> 请用纯 HTML/CSS/JS（不依赖任何外部库 / 框架 / CDN）做一个单文件页面 `index.html`，主题是「Web3 / AI 基础概念学习」，要求：
>
> 1. **三张可翻转的概念卡片**，分别讲：
>    - 钱包 / 签名 / 交易（私钥→公钥→地址；ECDSA 签名直觉；为什么节点不需要私钥）
>    - Gas / 合约执行（EIP-1559 base fee 销毁、tip 给验证者；revert 时 gas 不退）
>    - LLM / Agent / Workflow（next token 预测；Reason→Act→Observe loop；Workflow vs Agent 怎么选）
>    每张卡片点击翻面，正面 3 条要点，背面是一个"延伸问题 + 答案"。
>
> 2. **一个 EIP-1559 Gas 计算器**，输入字段：`to`, `value (ETH)`, `gasLimit`, `当前 baseFee (gwei)`, `maxFeePerGas`, `maxPriorityFeePerGas`，外加一个**拖动滑块模拟 baseFee 变化**。实时显示：
>    - 有效 gas 单价 = `min(maxFee, baseSim + tip)`
>    - 销毁部分 = `min(eff, baseSim)` × gasLimit
>    - 给验证者部分 = `(eff - burn)` × gasLimit
>    - Gas 费上限 = maxFee × gasLimit
>    - 实际 Gas 费 = eff × gasLimit
>    - 钱包总扣款 = 实际 gas + value
>    - 一段 EIP-1559 type-2 tx 的 JSON 展示
>    边界提示：当 `baseSim > maxFee` 时标红"不会被打包"；当 `baseSim + tip > maxFee` 时标黄"tip 被截断"。
>
> 3. **9 题单选 quiz**，三个主题各 3 题，答完显示对/错+解析，底部累计得分。
>
> 4. 暗色主题，无任何外部资源请求，可直接 `open index.html` 打开。
>
> 5. 单文件输出，所有 CSS/JS 内联。
>
> **要求 agent 同时输出**：
> - 完整 `index.html`
> - 一段简短自评：哪些数值/概念你不确定，建议人工复核哪几处
> - 一份"下一步迭代清单"（5 条以内）

---

## 对比维度

对每个工具跑完后，把观察填入下表。

### 1. 代码生成质量

| 工具 | 是否一次跑通 | 计算公式是否正确 | HTML/CSS 结构 | 可读性 |
| --- | --- | --- | --- | --- |
| Claude Code (Opus 4.7) | ✅ 一次跑通 | ✅ min(maxFee, baseFee+tip) 正确，且边界提示完整 | 单文件清晰分段 | 命名直接、注释少而准 |
| Codex | _待填_ | _待填_ | _待填_ | _待填_ |
| Hermes | _待填_ | _待填_ | _待填_ | _待填_ |

### 2. 上下文保持

长对话里追问"把 gas 计算器加上 Legacy(type 0) 对比模式"、"把卡片改成中英双语"、"加 localStorage 错题本" —— 工具是否还记得最初的设计约束（零依赖、单文件、暗色主题）？

| 工具 | 第 3 轮追问后是否仍守约束 | 是否会无故引入依赖 | 是否会重写已有正确部分 |
| --- | --- | --- | --- |
| Claude Code | _追问 3 轮后填_ | _待填_ | _待填_ |
| Codex | _待填_ | _待填_ | _待填_ |
| Hermes | _待填_ | _待填_ | _待填_ |

### 3. 资料整理

让工具"为这个 demo 写一份 README，说明你做了什么、哪些不可靠、下一步"，看：

| 工具 | 自评的诚实度 | 不可靠点是否真的覆盖了风险 | README 风格 |
| --- | --- | --- | --- |
| Claude Code | 主动指出 gasUsed=gasLimit 简化、blob fee 缺失、ecrecover 措辞模糊 | ✅ 风险点踩得准 | 简洁、有 ❗ 标记 |
| Codex | _待填_ | _待填_ | _待填_ |
| Hermes | _待填_ | _待填_ | _待填_ |

### 4. 工具调用 / 自动化

让工具"把生成的文件写到 `demos/week1-interactive/`、提交并 push 到 GitHub"，看：

| 工具 | 能否操作文件系统 | 能否执行 git | 操作前是否确认 |
| --- | --- | --- | --- |
| Claude Code | ✅ Edit/Write/Bash 全支持 | ✅ | 风险动作前会问（push/force/reset） |
| Codex | _待填_ | _待填_ | _待填_ |
| Hermes | _待填_ | _待填_ | _待填_ |

### 5. 长期学习记录

跨会话/跨天问："上次我们讨论 Gas 计算时，你说 access list 没建模——记得吗？我们今天加一下" —— 看：

| 工具 | 是否能跨会话回忆 | 回忆机制（内置 memory？必须挂 vector DB？） | 误召回率（瞎编"上次说过") |
| --- | --- | --- | --- |
| Claude Code | 有 `~/.claude/.../memory/` 文件式记忆，主动写入；查询前会复核当前代码 | 文件 + frontmatter index | _按实际观察填_ |
| Codex | _待填_ | _待填_ | _待填_ |
| Hermes | _待填_ | _待填_ | _待填_ |

---

## 操作步骤（自己跑时按这个顺序）

1. 在 Codex 新开一个空 workspace，复制上方 prompt。把产物落到本地一个临时目录（**不要覆盖** `demos/week1-interactive/`）。
2. 跑完后做 3 轮追问（见维度 2 的三个改造请求），观察上下文保持。
3. 让它生成 README + 自评。
4. 把同一流程在 Hermes 重做一遍。
5. 回到这个文件，把"_待填_"的格子填上一两句具体观察 + 短代码片段或截图链接。
6. 在最下面写一段 **结论**：你在这个具体任务上更倾向哪个工具，为什么。

---

## 结论（跑完后回填）

_待填。建议写 5~8 行：哪个工具更适合这种"小而完整"的前端教学产物，哪个工具更适合长链路任务，未来 Week 2/3 想分别用谁。_
