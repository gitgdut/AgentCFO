# AgentCFO — DAO AI 财务官

> **Give every DAO an AI CFO with a controlled wallet.**
>
> AgentCFO 是面向 Web3 小团队 / DAO 的 AI 财务官。它可以读取贡献记录、预算规则和付款需求，自动生成付款计划，并通过 Cobo Agentic Wallet 在预算、白名单、单笔限额和人工确认边界内执行真实链上付款，最后输出可审计的结算报告。

---

## 🏁 Track

**Cobo 赛道 — Agentic Commerce**

核心命题：**AI Agent 可以参与真实经济活动，且资金操作必须通过 Cobo Agentic Wallet 完成。**

| 方向 | 说明 |
|------|------|
| **Agent-Native Payments** | Agent 根据贡献记录和预算规则，自动生成付款计划并发起付款 |
| **Agent Resource Procurement** | Agent 判断 DAO 本月需支付哪些工具订阅、服务费用或贡献者报酬 |
| **A2A Economy / Treasury** | 未来扩展到多 Agent 管理不同部门预算，统一向 DAO Treasury 汇报 |

---

## ❓ Problem

Web3 小团队和 DAO 的财务操作全卡在人工环节：

- 贡献者结算靠人工表格，容易漏发、错发、重复发
- 小额支出频繁但多签流程太重
- DAO 支出不透明，事后很难追踪
- 订阅费、工具费、赏金散落在各处
- 完全自动化有资金风险，纯人工又太低效

**AgentCFO 的解法：让 Agent 负责整理、判断、生成付款计划，让 CAW 负责受控执行资金，让人类保留关键确认权。**

---

## 🎬 MVP Demo Story

一个 Web3 小团队本月有 3 个贡献者和 1 个工具订阅费用：

| 对象 | 类型 | 说明 | 金额 |
|------|------|------|------|
| Alice | 贡献者 | 写了一篇活动复盘 | 20 USDC |
| Bob | 贡献者 | 设计了活动海报 | 15 USDC |
| Charlie | 贡献者 | 维护社群并整理数据 | 10 USDC |
| Data API | 工具订阅 | 本月数据服务订阅费 | 5 USDC |

**AgentCFO 的工作流：**

1. 自动识别收款人、金额、地址和付款原因
2. 检查是否超过总预算
3. 检查是否超过单笔限额
4. 检查钱包地址是否在白名单内
5. 发现异常时暂停付款（如地址不在白名单、重复付款）
6. 生成人类可读的付款计划
7. 人类确认后，通过 Cobo Agentic Wallet **执行测试网付款**
8. 展示 Transaction Hash、Agent Wallet 地址、付款状态和审计报告

---

## 🔄 Product Flow

```
贡献记录 / 订阅账单输入
        ↓
AgentCFO 解析任务
        ↓
生成付款计划
        ↓
风险检查：预算、白名单、限额、重复付款
        ↓
人类确认
        ↓
Cobo Agentic Wallet 执行付款
        ↓
链上交易完成
        ↓
生成审计报告
```

---

## 📋 MVP Scope

### ✅ Must Complete

| 模块 | 内容 |
|------|------|
| 任务输入 | 支持输入 / 上传 mock 贡献记录（姓名、任务、金额、钱包地址） |
| AI 付款计划 | Agent 根据贡献记录生成付款计划和付款原因 |
| 风险检查 | 预算、白名单、单笔限额、重复付款 |
| 人工确认 | 付款前确认按钮，不能直接自动转账 |
| CAW 付款 | 通过 Cobo Agentic Wallet 完成测试网付款 |
| 链上记录 | 展示 tx hash、Agent Wallet 地址、测试网地址 |
| 审计报告 | 每笔付款原因、状态、风险检查结果和剩余预算 |
| Demo 视频 | 3–5 分钟完整演示 |
| README | 项目目标、架构、运行方式、CAW 使用位置、关键代码说明 |

### 🟡 Nice to Have

- Request Network 发票/账单记录
- Sablier Flow 长期 payroll
- Safe Module 权限控制参考
- Notion / GitHub 作为数据源
- 多 Agent 财务系统

### ❌ Won't Do (MVP)

| 不做 | 原因 |
|------|------|
| 真实主网资金 | Demo 风险高，用测试网 |
| 复杂 DAO 治理投票 | 偏离 Agent 资金执行主线 |
| 完整会计系统 | 容易变成普通财务后台 |
| 自动无限制付款 | 不符合安全边界 |
| 多链复杂适配 | 时间有限，先跑通一条测试网 |

---

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────┐
│                   Frontend                       │
│  Dashboard → 任务输入 → 付款计划 → 风险检查       │
│  → 人工确认 → 付款状态 → 审计报告                 │
└────────────────────┬────────────────────────────┘
                     │ REST API
┌────────────────────▼────────────────────────────┐
│                 Backend / Agent                  │
│  接收贡献记录 → LLM 生成付款计划 → 规则引擎检查    │
│  → 结构化 payment plan → 调用 CAW → 审计日志     │
└────────────────────┬────────────────────────────┘
                     │ SDK / API
┌────────────────────▼────────────────────────────┐
│           Cobo Agentic Wallet (CAW)              │
│  Agent Wallet 管理 → 预算上限 → 白名单            │
│  → 单笔限额 → 执行测试网付款 → 返回 tx hash       │
└────────────────────┬────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────┐
│              Testnet Blockchain                  │
│          可验证链上交易 + tx hash                  │
└─────────────────────────────────────────────────┘
```

### Frontend Pages

1. Landing / 项目介绍
2. DAO 任务输入页
3. Agent 付款计划生成页
4. 风险检查页
5. 人工确认页
6. CAW 付款执行状态页
7. 审计报告页

### Backend APIs

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/payment-plan` | POST | 输入贡献记录 + 预算规则 → Agent 生成付款计划 |
| `/api/risk-check` | POST | 输入付款计划 → 风险检查结果 |
| `/api/execute-payment` | POST | 输入确认后付款计划 → 调用 CAW 执行 → tx hash |
| `/api/audit-report/:id` | GET | 输出最终审计报告 |

---

## 📊 Data Structures

### 贡献记录输入

```json
[
  {
    "name": "Alice",
    "role": "Content Contributor",
    "task": "Wrote event recap article",
    "wallet": "0xAlice...",
    "amount": 20,
    "token": "USDC"
  },
  {
    "name": "Bob",
    "role": "Designer",
    "task": "Designed event poster",
    "wallet": "0xBob...",
    "amount": 15,
    "token": "USDC"
  }
]
```

### 预算规则

```json
{
  "monthlyBudget": 50,
  "singlePaymentLimit": 25,
  "allowedToken": "USDC",
  "whitelist": ["0xAlice...", "0xCharlie...", "0xDataAPI..."],
  "requiresHumanApproval": true
}
```

### Agent 输出

```json
{
  "summary": "AgentCFO generated a payment plan for 3 contributors and 1 subscription.",
  "totalAmount": 50,
  "riskLevel": "Medium",
  "payments": [
    {
      "recipient": "Alice",
      "amount": 20,
      "reason": "Completed event recap article",
      "status": "Ready"
    },
    {
      "recipient": "Bob",
      "amount": 15,
      "reason": "Designed event poster",
      "status": "Blocked",
      "risk": "Recipient address is not in whitelist"
    }
  ]
}
```

---

## 🛠️ Tech Stack

| 层 | 技术 | 用途 |
|----|------|------|
| **AI / Agent** | LLM (OpenAI / DeepSeek) + LangChain | 贡献理解、付款理由生成、异常识别 |
| **钱包** | [Cobo Agentic Wallet](https://www.cobo.com/agentic-wallet) + Pact | 受控链上付款 + 四重护栏 |
| **区块链** | EVM 测试网 (Base Sepolia / Arbitrum Sepolia) | 付款结算层 |
| **后端** | Python (FastAPI) / TypeScript | API、规则引擎、数据管道 |
| **前端** | Next.js / Streamlit (Demo) | 仪表盘、审批界面 |
| **数据源** | CSV / JSON（MVP）；后续 GitHub / Discord / Notion API | 贡献数据 |
| **存储** | SQLite / PostgreSQL | 付款记录、审计日志 |

---

## 👥 Team (5 人)

| 角色 | 核心职责 | 交付物 |
|------|----------|--------|
| **交付 / 总控** | 路演投屏、项目串联、GitHub 管理、进度同步 | Repo、README 终版、提交清单、路演流程 |
| **物料 / 设计** | PPT、海报、Logo、视频、演讲稿、视觉统一 | PPT、16:9 头图、Demo 视频、截图素材包 |
| **前端** | Demo 页面开发，Agent 工作流展示 | 前端页面、API 对接、mock 模式 |
| **后端 / Agent** | Agent workflow、付款计划、风险检查、审计日志 | API、payment plan 逻辑、risk check |
| **合约 / CAW** | Cobo Agentic Wallet 集成、测试网付款 | CAW 配置、tx hash、Agent Wallet 地址 |

---

## ⚠️ Risk Boundaries

1. 本次 Demo 使用**测试网资金**
2. Agent **不能无限制转账**
3. 所有付款受**预算限制**
4. 所有收款地址必须在**白名单**内
5. 单笔付款有**金额上限**
6. 付款前需要**人类确认**
7. 异常付款会被**暂停**
8. 所有付款结果有 **tx hash** 可验证
9. Agent 只生成计划和执行授权范围内付款，**不接触用户私钥**
10. 未来可扩展多签审批、Sablier 流式付款、Request Network 发票记录

---

## 📅 Timeline

| 阶段 | 目标 | 时间 |
|------|------|------|
| **阶段 1 — 立项冻结** | 统一认知、建 Repo、确认 Demo 剧本、CAW 路径验证 | Day 1 |
| **阶段 2 — 核心开发** | 跑通闭环：输入 → 计划 → 检查 → 确认 → CAW 付款 → 报告 | Day 2–4 |
| **阶段 3 — 集成打磨** | UI 打磨、视频录制、PPT 完成、至少 2 次彩排 | Day 5–6 |
| **阶段 4 — 最终冻结** | 提交材料全部就绪，不再新增功能 | Day 7 |
| **阶段 5 — 提交前** | 只修 bug、优化演讲、准备备用方案 | 提交截止前 |

---

## 📁 Repo Structure

```
AgentCFO/
├── README.md                   # ← 你在这里
├── docs/
│   ├── architecture.md         # 架构设计
│   ├── caw-integration.md      # CAW 集成说明
│   ├── demo-script.md          # Demo 剧本
│   └── risk-boundary.md        # 风险边界
├── frontend/                   # 前端 Demo
├── backend/                    # 后端 / Agent 逻辑
├── contracts/                  # CAW 配置和合约交互
├── demo/
│   ├── screenshots/            # 截图
│   └── video-link.md           # 视频链接
└── assets/
    ├── poster.png              # 16:9 项目海报
    └── logo.png                # Logo
```

---

## 🏁 Submission Checklist

| 提交物 | 状态 |
|--------|------|
| GitHub Repo | ✅ |
| README | ✅ |
| Demo 视频 3–5 分钟 | ⬜ |
| 项目演示链接 | ⬜ |
| CAW 关键代码 / 配置说明 | ⬜ |
| Agent Wallet 地址 | ⬜ |
| 测试网 Transaction Hash | ⬜ |
| 流程截图 | ⬜ |
| PPT | ⬜ |
| README 头图 16:9 | ⬜ |
| 风险边界说明 | ✅ |

---

## 🚀 Quick Start

```bash
git clone https://github.com/gitgdut/AgentCFO.git
cd AgentCFO

# Backend
cd backend
pip install -r requirements.txt
cp .env.example .env
# Fill in: COBO_API_KEY, OPENAI_API_KEY, etc.
python src/main.py

# Frontend
cd frontend
npm install
npm run dev
```

---

## 🔑 Keywords

```
Agentic Commerce  •  DAO Treasury  •  AI CFO  •  Controlled Wallet
Cobo Agentic Wallet  •  Payment Plan  •  Risk Check  •  Audit Report
```

---

<p align="center">
  <b>AgentCFO</b> — Built for Cobo Agentic Commerce Hackathon<br>
  <sub>让 AI 财务官上岗。Read → Plan → Guard → Execute → Report. 💼</sub>
</p>
