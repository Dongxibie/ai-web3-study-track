# 5.26 Co-learning｜Cobo Agentic Wallet 学习笔记

整理类型：会议内容学习笔记
主题：AI Agent 钱包、权限控制与链上安全执行
分享嘉宾：Johnny（Cobo Agentic Wallet 产品负责人）
整理时间：2026.5.26

---

# 一、为什么 Agent 需要"可控的钱包"

随着 AI Agent 从聊天机器人逐渐演变成能够自主执行任务的智能体，一个新的问题开始出现：

> 如果 AI 可以代表人类完成链上操作，那么它应该如何安全地管理和使用资金？

过去的 AI 更多是"建议型工具"。

例如：

* ChatGPT 帮用户回答问题；
* Cursor 帮程序员写代码；
* Claude Code 帮用户生成项目结构。

但现在的 Agent 已经开始具备：

* 自动执行工作流；
* 自动调用工具；
* 自动进行链上操作；
* 自动完成支付与交易。

这意味着：

> Agent 不再只是"思考"，而是真正开始"动钱"。

而链上资金具有不可逆性，一旦交易签名并广播，就很难撤回。

因此：

* AI 的执行边界；
* 钱包权限；
* 授权范围；
* 风控与审计；

都必须被重新设计。

---

# 二、Agent 动用资金时的核心风险

Johnny 提到了 Agent 在没有约束情况下可能产生的几个典型问题。

---

## 1. Prompt Injection（提示词注入）

Agent 的行为可能受到外部输入影响。

例如：

用户原本要求：

> "只允许花费 100 USDC。"

但如果 Agent 被恶意 Prompt 干扰：

* 可能修改交易金额；
* 可能调用错误合约；
* 甚至执行用户未授权的交易。

问题在于：

自然语言并不是严格权限系统。

AI 能"理解意图"，但不一定能"严格遵守规则"。

---

## 2. Shadow Operations（影子操作）

Agent 可能在用户看不到的地方创建额外钱包或子账户。

例如：

* Agent 创建一个新的 EOA；
* 把资金转入该地址；
* 再从新地址执行交易。

这样就绕过了原本的人类控制边界。

即使用户对 MPC 钱包做了限制：

> 只要资金被转出原控制体系，就已经失控。

---

## 3. Unscoped Authority（无限权限）

如果不给 Agent 设置权限边界：

* Agent 理论上可以操作全部资产；
* 一次错误执行就可能造成巨大损失。

例如：

* 私钥泄露；
* Agent 幻觉；
* 错误调用高风险合约；

都可能导致全部资金被转出。

---

## 4. Zombie Permissions（僵尸授权）

很多 DeFi 协议都会要求用户进行授权（Approve）。

如果授权长期不撤销：

* 即使用户已经不用该协议；
* 一旦协议被攻击；
* 资产仍可能被盗取。

因此：

> Agent 的授权必须具备时效性与范围限制。

---

# 三、Cobo Agentic Wallet 的核心思路

Cobo 的目标不是简单做一个 AI 钱包。

它更想解决：

> "如何让 Agent 在可信边界内替人类使用链上资产。"

整个系统核心包括三个部分：

* MPC
* Pact
* Recipe

---

# 四、MPC：解决私钥安全问题

MPC（Multi-Party Computation）即多方计算钱包。

核心思想：

> 私钥不会被任何单独一方完整掌握。

在 Cobo 的方案中：

* Human 持有部分密钥；
* Agent 持有部分密钥；
* Cobo 持有部分密钥。

任何单方：

* 都无法独立转移资产；
* 都无法单独完成签名。

---

## 2-of-2 签名模式

这里主要有两种场景。

### 场景一：Agent + Cobo

当用户批准 Pact 后：

* Agent 可以自动执行交易；
* Cobo 会验证是否符合授权范围；
* 双方共同完成签名。

---

### 场景二：Human + Cobo

用户自己操作钱包时：

* Human 发起签名；
* Cobo 协同验证；
* 完成资产转移。

这样可以保证：

* 没有人可以单独盗取资产；
* Agent 的执行始终受到限制。

---

# 五、Pact：给 Agent 设置"规则边界"

Johnny 认为：

> MPC 只解决"谁能动钱"，但 Pact 才解决"钱怎么用"。

Pact 本质上是一份授权协议。

它会明确：

* Agent 能做什么；
* 不能做什么；
* 什么时候停止执行。

---

## Pact 的四个核心组成

---

### 1. Intent（目标）

即用户希望 Agent 完成什么事情。

例如：

> "ETH 低于 2000 美元时买入，高于 2500 美元时卖出。"

---

### 2. Execution Plan（执行计划）

AI 会把用户意图转换为具体执行步骤。

例如：

* 调用哪个合约；
* 使用哪个 Token Pair；
* 使用多少 ETH；
* 如何执行 Swap。

---

### 3. Policy（风控规则）

这是 Pact 最重要的部分。

包括：

* 金额限制；
* 白名单地址；
* 可调用合约；
* Token 限制；
* ABI 参数限制；
* 滑点限制；
* 每日次数限制。

也就是说：

> Agent 即使想"乱来"，也无法突破这些边界。

---

### 4. Completion Condition（结束条件）

Pact 不应该永久生效。

例如：

* 超过 1000 USDC 后自动停止；
* 到达指定时间后失效；
* 完成目标后自动 revoke。

这样可以减少长期授权风险。

---

# 六、Recipe：帮助 Agent 正确执行链上操作

Johnny 提到：

目前大模型其实并不真正理解复杂链上交互。

例如：

* 合约 ABI；
* Router 调用；
* 授权逻辑；
* DeFi 执行顺序；

都很容易出错。

因此 Cobo 引入了：

> Recipe Skill Layer

可以理解为：

* Agent 的链上知识胶囊；
* 已验证的执行模板；
* 标准化操作路径。

---

## Recipe 的作用

它会提前封装：

* 合约地址；
* ABI；
* 参数规则；
* 风控条件；
* 正确调用顺序。

这样 Agent 不需要"自由发挥"。

而是：

> 按照已验证的流程完成交易。

目前支持的场景包括：

* Aave
* Uniswap
* Hyperliquid
* Polymarket

等主流协议。

---

# 七、Agent Wallet 的执行流程

整个流程大致如下：

---

## Step 1：用户提出需求

例如：

> "帮我在 ETH 跌到 2000 时买入。"

---

## Step 2：Agent 生成 Pact

包括：

* 执行计划；
* 风控策略；
* 时间限制；
* 金额限制。

---

## Step 3：Human 审批

用户会在 App 中看到：

* 风险提示；
* 执行内容；
* 交易范围。

然后：

* 同意；
* 修改；
* 或拒绝。

---

## Step 4：Agent 自动执行

审批完成后：

Agent 才能在授权范围内执行链上操作。

---

# 八、Human-in-the-loop 的意义

Johnny 认为：

短期内：

> Human-in-the-loop 仍然不可缺少。

也就是说：

* 关键步骤仍需要人类审批；
* Agent 不能完全自主控制资金。

原因包括：

* AI 幻觉问题仍存在；
* 支付网络尚未成熟；
* Agent 基础设施还不完善。

但未来：

随着 Agent 能力增强：

* Agent 与 Agent 支付；
* Agent 自主交易；
* Agent 雇佣 Agent；

都可能出现。

到那时：

人类可能只负责制定规则。

---

# 九、小额免密支付的讨论

分享中还讨论了：

> Agent 自动购买 API Token 的场景。

例如：

* Claude API 余额不足；
* Agent 自动充值；
* 单次不超过 50 美元；
* 每天最多一次。

这个场景的重点在于：

* 高频；
* 小额；
* 风险较低；
* 适合自动化。

Johnny 认为：

Pact 非常适合这种模式。

因为它可以限制：

* 金额；
* 地址；
* 次数；
* 时间；
* Token 类型。

从而把风险控制在较小范围内。

---

# 十、我对这次分享的理解

这次分享让我意识到：

AI Agent 真正进入 Web3 后，最难的问题并不是模型能力，而是：

> "如何安全地让 AI 使用资产。"

过去 Web3 更多关注：

* 钱包；
* 签名；
* 合约；
* DeFi。

但 Agent 时代会新增很多问题：

* AI 是否可信；
* AI 是否越权；
* AI 是否会长期持有危险授权；
* AI 是否能正确执行复杂合约逻辑。

Cobo 的思路其实是在尝试建立：

> 一套 AI 可执行、但又受到严格约束的链上操作系统。

我觉得：

Pact 很像"AI 世界里的权限系统"，而 Recipe 更像"AI 的链上技能库"。

未来如果 Agent Economy 真正发展起来：

* Agent Wallet
* Agent Payment
* Agent Permission
* Agent Reputation

这些方向都可能变成 Web3 的重要基础设施。

---

# 十一、关键词整理

* Agent Wallet
* MPC
* Pact
* Recipe
* Human-in-the-loop
* AI Agent
* DeFi Automation
* Smart Contract
* Policy Engine
* On-chain Execution
* Wallet Security
* Permission Control
* Crypto Payment
* Self-custody
* Chain Abstraction
