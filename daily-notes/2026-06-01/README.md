# 🚀 AgentFi X — AI × Web3 Autonomous Execution System

---

# 0. 项目定位（Team Shared Understanding）

AgentFi X 是一个 **AI Agent 驱动的 Web3 自动执行系统**，允许用户通过自然语言输入资产管理目标，并在用户授权后执行链上交易（Swap / Deposit / Stake）。

核心目标不是"建议"，而是：

> 🧠 AI 生成策略 + 🧾 用户确认 + 🔗 链上真实执行

---

# 1. 系统整体流程（必须统一理解）

所有团队成员必须遵守以下数据流：

```text
User Input
→ AI Agent（策略生成）
→ Structured JSON Plan
→ Frontend Render
→ User Approval
→ Execution Engine
→ Blockchain Interaction
→ Transaction Result Display
```

---

# 2. 功能拆解（MVP Scope）

## 2.1 AI Agent Layer

### 输入：

```text
我想用100 USDC做低风险收益
```

### 输出（严格 JSON）：

```json
{
  "goal": "yield",
  "risk": "low",
  "budget": 100,
  "steps": [
    {
      "action": "swap",
      "from": "USDC",
      "to": "ETH",
      "ratio": 0.4
    },
    {
      "action": "deposit",
      "protocol": "Aave",
      "asset": "USDC",
      "ratio": 0.6
    }
  ]
}
```

---

## 2.2 Execution Engine

支持：

- Uniswap Swap
- Aave Deposit
- Lido Stake（optional）

---

## 2.3 User Approval Layer（必须）

流程：

```text
AI生成策略
→ UI展示
→ 用户确认
→ 执行链上交易
```

---

## 2.4 Blockchain Layer

- Sepolia / Base testnet
- ethers.js interaction
- tx tracking

---

## 2.5 Result System

必须返回：

- tx hash
- gas used
- block number
- status

---

# 3. 技术架构（统一标准）

```text
Frontend (Next.js)
        ↓
AI Agent (LLM)
        ↓
Planner (JSON Generator)
        ↓
Execution Engine
        ↓
Web3 Layer (ethers.js)
        ↓
Smart Contracts (Hardhat)
        ↓
Testnet Blockchain
```

---

# 4. GitHub Repo Structure（强制统一）

```text
AgentFi-X/
│
├── frontend/        # UI + Chat + Wallet
├── agent/           # AI strategy generator
├── execution/       # swap / deposit logic
├── contracts/       # Solidity contracts
├── backend/         # API + orchestration
├── scripts/         # deploy scripts
│
├── docs/
│   ├── architecture.md
│   ├── agent.md
│   ├── execution.md
│   └── demo.md
│
├── README.md
└── package.json
```

---

# 5. 团队分工（必须严格执行）

---

## 5.1 AI Agent Engineer

### 负责模块：

- agent/
- prompt engineering
- JSON schema design
- strategy generation logic

### 交付物：

- strategy generator
- structured output validator
- prompt templates

### 不负责：

- UI
- blockchain execution

---

## 5.2 Web3 Engineer

### 负责模块：

- contracts/
- execution/
- blockchain integration

### 任务：

- Uniswap swap
- Aave deposit
- Wallet signing
- Hardhat deployment

### 交付物：

- smart contracts
- execution SDK
- tx handling

---

## 5.3 Frontend Engineer

### 负责模块：

- frontend/

### 任务：

- Chat UI
- Wallet connect
- Strategy visualization
- Approval UI
- Transaction display

### 交付物：

- full UI flow
- user interaction system

---

## 5.4 Backend / Integration Engineer

### 负责模块：

- backend/

### 任务：

- API orchestration
- Agent ↔ Execution bridge
- data persistence
- tx history storage

### 交付物：

- unified API layer
- system integration
- logging system

---

# 6. 协作规则（非常重要）

## 6.1 数据契约（必须统一）

所有模块必须使用 `StrategyPlan`，结构必须一致，否则不允许合并代码。

---

## 6.2 Git Workflow

```text
main
 ├── dev
 │    ├── feature/agent
 │    ├── feature/web3
 │    ├── feature/frontend
 │    ├── feature/backend
```

---

## 6.3 Commit 规范

```text
feat: add agent strategy generator
fix: correct swap execution bug
docs: update architecture
```

---

## 6.4 API 协议统一

```text
POST /generate-strategy
POST /execute-transaction
GET  /tx-status
```

---

# 7. Claude Code / AI 协作方式（核心）

## 每个任务必须遵循：

### 输入：

- 当前模块代码
- 目标功能

### 输出：

- 可运行代码
- 不解释

---

## 示例任务：

```text
实现 Uniswap swap execution module
要求：
- ethers.js
- 可调用
- 返回 tx hash
```

---

# 8. Demo 流程（评审标准）

必须完整演示：

```text
1. 输入自然语言
2. AI生成JSON策略
3. UI展示策略
4. 用户点击确认
5. 执行链上交易
6. 返回tx hash
7. Etherscan验证
```

---

# 9. 非功能要求

必须满足：

- 可运行（not mock）
- 至少1笔testnet交易
- 有真实tx proof
- 可复现
- 有日志记录

---

# 10. 项目边界

禁止：

- AI直接执行交易
- 无结构输出
- 无用户确认流程

必须：

- JSON schema
- user approval
- on-chain proof

---

# 11. 里程碑（团队执行节奏）

## Day 1–2

- frontend setup
- agent prompt

## Day 3–5

- swap / deposit execution

## Day 6–7

- contract + integration

## Day 8–10

- demo + debugging

---

# 12. 项目一句话定义（统一对外）

AgentFi X 是一个：

> AI 驱动的 Web3 资产执行系统，将自然语言资产目标转换为可验证的链上交易执行流程

---

# 13. 团队沟通机制（必须执行）

## 每日同步：

```text
- 今日完成
- 当前阻塞
- 下一步计划
```

---

## 问题处理原则：

- AI问题 → Agent engineer
- 合约问题 → Web3 engineer
- UI问题 → Frontend engineer
- 集成问题 → Backend engineer

---

## 冲突原则：

最终以"是否影响 Demo 流程"作为优先级判断
