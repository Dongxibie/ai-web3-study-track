# Week 1 Interactive

Week 1 的可交互产物。一个零依赖的单文件 HTML 小页面，覆盖三个核心概念：

1. **钱包 / 签名 / 交易**
2. **Gas / 合约执行**
3. **LLM / Agent / Workflow**

形式：3 张可翻转的概念卡片 + 1 个 **EIP-1559 Gas 计算器**（重点 demo）+ 9 题 quiz。

## 怎么打开

直接双击 `index.html`，或者：

```bash
# 任选其一
python -m http.server 8000     # 然后访问 http://localhost:8000
# 或
npx serve .
```

只用了原生 HTML/CSS/JS，没有任何依赖、没有连接钱包、没有发出任何真实交易。`to` 字段只是用于展示 tx 结构，私钥不会出现在任何地方。

## 我让 agent 做了什么

让 Claude Code（Opus 4.7）一次性生成这个单文件页面，要求：
- 三张卡片 click-to-flip
- EIP-1559 计算器要展示 `min(maxFee, baseFee + tip)` 的语义
- 要有 base fee 滑块模拟拥堵
- 9 题 quiz 自动判分

Agent 一次给出了完整的 HTML/CSS/JS，没有依赖。

## 我人工修改/检查了什么

- ❗ Quiz 第 3 题（最危险的操作）原本 agent 把 testnet 选项写得有歧义，我让它改成"在 testnet 上随便发交易"，让正确选项"截图存进相册"对比更明确。
- ❗ EIP-1559 计算公式 `min(maxFee, baseFee + tip)` 和 `burn = min(eff, baseFee)`、`tip_actual = eff - burn` 我对着 [EIP-1559 spec](https://eips.ethereum.org/EIPS/eip-1559) 复核过，边界情况（baseFee > maxFee 时不打包；baseFee + tip > maxFee 时被截断）也实际拖滑块测过。
- 默认值改成现实里更常见的范围（baseFee 20 gwei、tip 1.5 gwei、maxFee 40 gwei），而不是 agent 给的 100/2/150 这种偏激进数值。
- 概念卡片背面的文字按我自己的理解重写了 1~2 句，agent 原文偏教科书味儿。

## 哪些输出不可靠 / 我不敢断言

- **Quiz 解析文本**是 agent 写的，我做了快速 review 但没有逐题对照权威资料。如果用作他人学习，建议交叉验证。
- **Gas 计算的细节**：当前实现假设 `gasUsed === gasLimit`，对普通转账（21000 固定）成立，但合约调用实际 `gasUsed` 通常 < `gasLimit`，退款的部分这里没建模。所以这个计算器适合教学，不适合估真实合约调用费用。
- **EIP-1559 模型**简化忽略了 access list 的 gas 折扣、blob fee（EIP-4844）、L2 sequencer fee 等。仅适用 L1 主网普通交易心智模型。
- **"为什么不需要把私钥发出去"卡片**的措辞——"v 让验证方反推公钥"这句话技术上对（恢复 ID + ECDSA recover），但口语化解释可能给某些读者错觉。严谨表述应当指向 `ecrecover`。

## 下一步准备如何改进

- [ ] 把计算器接上 `viem`/`ethers` 的 `estimateFeesPerGas`，从公链拉真实 base fee 而不是手填
- [ ] 增加 Legacy（type 0）vs EIP-1559（type 2）的费用对比模式
- [ ] Quiz 加错题本（localStorage 记录答错的题，下次进入优先复习）
- [ ] 卡片改为 i18n（中/英对照），方便 README 链接给英文受众
- [ ] 把 demo 拆出来部署到 GitHub Pages，README 里给个直链
- [ ] 加上 **签名演示**：浏览器内用 WebCrypto 演示 ECDSA 签名/验签的最小例子（**只用临时密钥对，绝不接 MetaMask**）

## 多工具对比

见 `../../notes/multi-tool-comparison.md`：里面整理好了同一份 prompt，准备分别交给 Codex 和 Hermes 跑同一任务，比较 5 个维度。我先用 Claude Code 完成了基线版本，对比表格留空待填。
