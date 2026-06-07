# AgentCFO — AI CFO for Web3 Teams & DAOs

> *把你的财务官装进口袋。Read contributions. Plan payments. Execute on-chain. Settle auditable reports.*

---

## 1. Problem

**Web3 小团队和 DAO 没有专职 CFO，财务操作全卡在人工环节。**

- 贡献者多、付款频率高（开发者、设计师、社区管理员、服务商……）
- 算贡献靠手动扒 GitHub / Discord / Notion，费时且容易遗漏
- 预算规则写在 Google Sheet 或脑子里，执行靠人盯
- 链上打款走多签钱包，流程慢、审批链长
- 事后对账痛苦——钱花哪了、谁批的、为什么批，散落在各处

**结果：每月光是"付款"这件事就吃掉 10~20% 的核心团队时间，且容易出错。**

---

## 2. Track

**Hackathon Track: AI × Web3 / DeFi & Payments**

本项目参加 AI + Web3 主题黑客松，聚焦 **Agent + 链上钱包 + 自动化财务** 的交叉领域。

---

## 3. MVP Flow

```
  ┌──────────────┐     ┌──────────────┐     ┌─────────────────────┐     ┌──────────────┐
  │ 贡献数据源     │     │ 预算规则引擎   │     │ Cobo Agentic Wallet  │     │ 结算报告      │
  │              │     │              │     │                     │     │              │
  │ • GitHub     │────▶│ • 总预算上限   │────▶│ 🔒 预算上限校验       │────▶│ • 付款明细    │
  │ • Discord    │     │ • 白名单地址   │     │ 🔒 白名单校验         │     │ • 审批记录    │
  │ • Notion     │     │ • 单笔限额     │     │ 🔒 单笔限额           │     │ • 链上 TX     │
  │ • 自定义 API  │     │ • 付款计划生成  │     │ 🔒 人工确认           │     │ • 可审计导出   │
  └──────────────┘     └──────────────┘     └─────────────────────┘     └──────────────┘
```

### Step-by-step

| Step | 做什么 | 输入 | 输出 |
|------|--------|------|------|
| 1️⃣ **Read** | AI 读取各平台贡献记录，理解谁做了什么、该不该付 | GitHub commits / PRs, Discord 角色, Notion tasks | 结构化贡献摘要（贡献者、贡献类型、建议金额） |
| 2️⃣ **Plan** | 将贡献数据套入预算规则（总预算、分配比例、白名单），生成付款计划 | 贡献摘要 + 预算规则 | 付款计划 JSON（地址、金额、理由） |
| 3️⃣ **Execute** | 通过 Cobo Agentic Wallet 的 Pact 机制，在四重护栏内执行真实链上付款 | 付款计划 | 链上 TX hash |
| 4️⃣ **Settle** | 汇总所有付款 + 审批记录 + 链上 TX，输出可审计结算报告 | TX records + 计划 | Markdown / PDF 结算报告 |

### Four Guardrails (四重护栏)

| 护栏 | 说明 |
|------|------|
| 💰 **预算上限** | 本期总付款不超过预设预算 |
| 📋 **白名单** | 只向已审批的地址付款 |
| 🔢 **单笔限额** | 单笔不超过阈值（如 0.5 ETH） |
| ✋ **人工确认** | 高风险付款需人类管理员二次确认 |

---

## 4. Tech Stack

| 层 | 技术选型 | 用途 |
|----|----------|------|
| **AI / Agent** | LLM (OpenAI / DeepSeek) + LangChain | 读取贡献数据、生成付款理由、自然语言交互 |
| **钱包** | [Cobo Agentic Wallet](https://www.cobo.com/agentic-wallet) + Pact 机制 | 链上付款执行 + 四重护栏 |
| **区块链** | EVM 兼容链 (Base / Arbitrum / OP) | 付款结算层 |
| **后端** | Python (FastAPI) / TypeScript | API 服务、规则引擎、数据管道 |
| **数据源** | GitHub API, Discord API, Notion API | 贡献数据采集 |
| **前端** | Next.js / Streamlit (Demo) | 仪表盘、审批界面 |
| **存储** | SQLite / PostgreSQL | 付款记录、审计日志 |

---

## 5. Risks & Mitigations

| 风险 | 等级 | 缓解措施 |
|------|------|----------|
| **AI 幻觉导致付款金额错误** | 🔴 高 | 金额计算走规则引擎（确定性），AI 只负责"理解贡献并建议"，不直接输出金额；配合人工确认护栏 |
| **智能合约 / 钱包安全** | 🔴 高 | 使用 Cobo 经过审计的 Agentic Wallet；金额上限 + 白名单双重兜底；私钥不暴露给 Agent |
| **Cobo API 限流或不可用** | 🟡 中 | 本地缓存付款计划，API 恢复后重试；设计 fallback 到手动多签流程 |
| **人工确认环节成为瓶颈** | 🟡 中 | 低风险付款（小额 + 白名单内）自动执行；只在高风险付款触发人工确认 |
| **DAO 法律地位模糊 / 合规风险** | 🟡 中 | 本项目聚焦技术实现；合规建议标注为"需咨询法律顾问" |
| **多数据源接入复杂度** | 🟢 低 | Week 1-2 只接 1~2 个数据源做 MVP，后续扩展 |

---

## 6. Validation Plan (4-Week Sprint)

```
Week 1 ──── Week 2 ──── Week 3 ──── Week 4
[搭建]      [数据+规则]   [钱包集成]    [端到端 Demo]
```

| 周 | 目标 | 验证标准 |
|----|------|----------|
| **Week 1** | 项目脚手架搭建 | • Repo 初始化<br>• 技术栈确定<br>• Cobo Sandbox 环境申请并通过<br>• 基础 API 跑通 |
| **Week 2** | 贡献读取 + 预算规则引擎 | • 接入 ≥1 个数据源（如 GitHub）获取贡献记录<br>• AI 能输出结构化贡献摘要<br>• 预算规则引擎能根据摘要生成付款计划 JSON |
| **Week 3** | Cobo Agentic Wallet 集成 | • Pact 护栏配置（预算上限、白名单、单笔限额）<br>• Sandbox 环境完成付款执行<br>• 人工确认流程跑通 |
| **Week 4** | 端到端 Demo | • **真实链上付款**（测试网或低金额主网）<br>• 四重护栏全部生效<br>• 输出可审计结算报告<br>• Demo 视频录制 |

---

## 7. Why AgentCFO?

| 对比维度 | 传统多签钱包 | 人工财务 | AgentCFO |
|----------|:-----------:|:------:|:--------:|
| 付款速度 | 慢（需多人签名） | 中 | 快（自动执行 + 少量人工确认） |
| 规则执行 | 靠人记忆 | 靠人记忆 | 代码强制执行 |
| 对账 | 手动查浏览器 | Excel | 自动生成可审计报告 |
| 可扩展性 | 固定签名人 | 无法扩展 | 规则引擎可编程扩展 |
| 成本 | Gas 费 | 人力成本 | Gas 费 + API 费 |

---

## 8. Repository Structure

```
AgentCFO/
├── README.md               # ← 你在这里
├── docs/                   # 设计文档
│   ├── architecture.md     # 架构设计
│   └── pact-rules.md       # Pact 护栏规则设计
├── src/
│   ├── agent/              # AI Agent 核心
│   │   ├── contribution_reader.py
│   │   └── payment_planner.py
│   ├── wallet/             # Cobo Wallet 集成
│   │   └── cobo_client.py
│   ├── rules/              # 预算规则引擎
│   │   └── budget_engine.py
│   └── reports/            # 结算报告生成
│       └── settlement.py
├── tests/
├── demo/                   # Demo 脚本和视频
└── .github/                # CI/CD
```

---

## 9. Quick Start

```bash
# Clone
git clone https://github.com/gitgdut/AgentCFO.git
cd AgentCFO

# Install dependencies (TBD)
pip install -r requirements.txt

# Set environment variables
cp .env.example .env
# Fill in: COBO_API_KEY, OPENAI_API_KEY, GITHUB_TOKEN, etc.

# Run the agent
python src/main.py
```

---

## License

MIT

---

<p align="center">
  <b>AgentCFO</b> — Built for AI × Web3 Hackathon<br>
  <sub>让你的 AI 财务官上岗 💼</sub>
</p>
