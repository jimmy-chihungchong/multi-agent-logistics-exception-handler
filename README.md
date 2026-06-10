# 🚚 Multi-Agent Logistics Exception Handler

An AI-powered multi-agent system that autonomously detects, triages, and resolves last-mile delivery exceptions — built with LangGraph and OpenAI.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/YOUR_USERNAME/YOUR_REPO/blob/main/YOUR_NOTEBOOK.ipynb)
![Python](https://img.shields.io/badge/python-3.10+-blue)
![LangGraph](https://img.shields.io/badge/LangGraph-framework-orange)
![License](https://img.shields.io/badge/license-MIT-green)

> 🎓 Capstone project for the **Post Graduate Program in AI Agents for Business Applications** — McCombs School of Business, The University of Texas at Austin (in collaboration with Great Learning).

---

## 📌 The Problem

Last-mile delivery is the most expensive and failure-prone leg of the logistics chain. When something goes wrong — a customer isn't home, an address is invalid, a package is damaged, weather blocks a route — human dispatchers must manually investigate each "exception," contact stakeholders, and decide on a resolution. This is slow, costly, and doesn't scale.

<!-- TODO: Add 1–2 sentences with any stats or framing you used in your capstone, e.g. "Failed first-attempt deliveries cost carriers an estimated $X per package." -->

## 💡 The Solution

This project replaces that manual triage loop with a **team of specialized AI agents** that collaborate to handle delivery exceptions end-to-end:

1. An exception is detected and classified
2. The right specialist agent investigates and gathers context
3. A resolution is proposed (reschedule, reroute, refund, escalate, etc.)
4. Stakeholders are notified — with human escalation only when needed

The result: routine exceptions get resolved in seconds without human intervention, and humans only see the cases that genuinely need judgment.

---

## 🏗️ Architecture

<!-- TODO: Replace with your actual diagram. Draw it free in the browser at https://excalidraw.com or https://app.diagrams.net, export as PNG, and upload to the repo (e.g. /docs/architecture.png) -->
![Architecture Diagram](docs/architecture.png)

### The Agents

<!-- TODO: Edit this table to match your actual agents — names, roles, and tools -->

| Agent | Role | Tools / Capabilities |
|---|---|---|
| 🧭 **Supervisor / Orchestrator** | Routes each exception to the right specialist and manages the overall workflow | LangGraph state graph, conditional routing |
| 🔍 **Triage Agent** | Classifies the exception type and severity from raw event data | Classification prompt, structured output |
| 📦 **Investigation Agent** | Pulls order, customer, and shipment context to understand what happened | <!-- e.g. mock order DB lookup, tracking API --> |
| 🛠️ **Resolution Agent** | Decides and executes the fix: reschedule, reroute, refund, redeliver | <!-- e.g. policy rules, action tools --> |
| 📣 **Communication Agent** | Drafts customer/driver notifications about the outcome | <!-- e.g. email/SMS template generation --> |

### Why LangGraph?

<!-- TODO: Keep/edit the reasons that actually drove your choice -->
- **Stateful graph workflow** — exception handling is a branching process, not a linear chain; LangGraph's state machine model fits naturally
- **Conditional routing** — different exception types take different paths through the agent team
- **Human-in-the-loop support** — low-confidence or high-value cases can pause for human approval
- **Observability** — each node's inputs/outputs are inspectable, which matters for debugging multi-agent behavior

---

## 🎬 Example Run

Here's the system handling a real exception end-to-end:

<!-- TODO: Paste a (lightly trimmed) actual execution trace from your notebook. This section is what reviewers read most — a real trace is far more convincing than a description. Example shape below: -->

```
📥 EXCEPTION RECEIVED: Delivery attempt failed — customer not home (Order #48291)

[Triage Agent]        → Classified: FAILED_DELIVERY / severity: LOW
[Investigation Agent] → Customer has delivery window preference: evenings.
                        Package: non-perishable. 2 attempts remaining.
[Resolution Agent]    → Decision: Reschedule to next evening window (no human needed)
[Communication Agent] → Drafted SMS: "Hi! We missed you today. Your package
                        is rescheduled for tomorrow between 6–8 PM..."

✅ RESOLVED in 14.2s — no human escalation required
```

More scenarios (damaged package, invalid address, weather delay) are demonstrated in the notebook with full saved outputs.

---

## 🚀 Getting Started

### Option 1 — Run in Google Colab (recommended)

No setup required:

1. Click the **Open in Colab** badge at the top of this README
2. Add your OpenAI API key when prompted (or set it in Colab Secrets as `OPENAI_API_KEY`)
3. Run the cells top to bottom

### Option 2 — Run locally

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPO.git
cd YOUR_REPO
pip install -r requirements.txt
export OPENAI_API_KEY="sk-..."
jupyter notebook YOUR_NOTEBOOK.ipynb
```

<!-- TODO: Create requirements.txt — in Colab, run `!pip freeze | grep -iE "langchain|langgraph|openai"` to capture the key pinned versions -->

---

## 🧰 Tech Stack

<!-- TODO: Adjust versions/components to match your project -->
- **Python 3.10+**
- **LangGraph** — multi-agent orchestration and state management
- **LangChain** — LLM integration, tools, and prompts
- **OpenAI API** — agent reasoning (GPT-4o / GPT-4o-mini)
- **Jupyter / Google Colab** — development and demo environment

---

## 📂 Repository Structure

<!-- TODO: Match this to your actual files -->
```
.
├── README.md
├── requirements.txt
├── logistics_agents.ipynb      # Main notebook — runnable demo with saved outputs
├── docs/
│   └── architecture.png        # System architecture diagram
└── data/
    └── sample_exceptions.json  # Mock exception events used in demos
```

---

## ⚠️ Limitations & Future Work

Being honest about scope is a feature, not a bug:

<!-- TODO: Edit to reflect reality — this section signals engineering maturity -->
- Order/customer data is **mocked** — production use would integrate real OMS/TMS/carrier APIs
- No persistent memory across sessions; each exception is handled statelessly
- Resolution policies are prompt-encoded; a production system would externalize them as configurable business rules
- **Planned:** evaluation harness to measure resolution accuracy across a labeled exception dataset; cost/latency benchmarking per exception type

---

## 👤 About

Built by **Jimmy [LAST NAME]** — software engineer with 10+ years of IT experience, specializing in AI agent development for business automation.

- 💼 LinkedIn: [your-linkedin-url]
- 🐙 GitHub: [@YOUR_USERNAME](https://github.com/YOUR_USERNAME)
<!-- Optional: 📧 email, 🌐 portfolio site -->

## 📄 License

MIT — see [LICENSE](LICENSE) for details.
