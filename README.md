# 🚚 Multi-Agent Logistics Exception Handler

An AI-powered multi-agent system that autonomously detects, triages, and resolves last-mile delivery exceptions — built with LangGraph and OpenAI.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/jimmy-chihungchong/multi-agent-logistics-exception-handler/blob/main/logistics_agents.ipynb)
![Python](https://img.shields.io/badge/python-3.10+-blue)
![LangGraph](https://img.shields.io/badge/LangGraph-framework-orange)
![License](https://img.shields.io/badge/license-MIT-green)

> 🎓 Capstone project for the **Post Graduate Program in AI Agents for Business Applications** — McCombs School of Business, The University of Texas at Austin (in collaboration with Great Learning).

---

## 📌 The Problem

Last-mile delivery is the most expensive and failure-prone leg of the logistics chain. When something goes wrong — a customer isn't home, an address is invalid, a package is damaged, weather blocks a route — human dispatchers must manually investigate each "exception," contact stakeholders, and decide on a resolution. This is slow, costly, and doesn't scale.

## 💡 The Solution

This project replaces that manual triage loop with a **team of specialized AI agents** that collaborate to handle delivery exceptions end-to-end:

1. An exception is detected and classified
2. The right specialist agent investigates and gathers context
3. A resolution is proposed (reschedule, reroute, refund, escalate, etc.)
4. Stakeholders are notified — with human escalation only when needed

The result: routine exceptions get resolved in seconds without human intervention, and humans only see the cases that genuinely need judgment.

---

## 🏗️ Architecture

<!-- TODO: Draw your agent graph at https://excalidraw.com, export as PNG, upload to a /docs folder in the repo, then uncomment the line below -->
<!-- ![Architecture Diagram](docs/architecture.png) -->

### The Agents

<!-- TODO: Edit this table to match your actual agents — names, roles, and tools -->

| Agent | Role | Tools / Capabilities |
|---|---|---|
| 🛠️ **Resolution Agent** | It reads the situation, consults the playbook, and decides what to do — and if it cannot produce a valid answer after 3 tries, it escalates to a human rather than making a potentially wrong decision. | It receives pre-processed information through its **`ResolutionAgentView`** which includes: `consolidated_event`, `customer_profile`, `locker_availability`, `playbook_context`, `escalation_signals`, and `critic_feedback` |
| 📣 **Communication Agent** | Reads the resolution decision and the customer's profile, then drafts a personalized message in the right tone for that customer — and if it cannot produce a proper message after 3 tries, it sends a generic fallback and flags the case for human review. | It operates on a restricted view of the state called **`CommunicationAgentView`**. This view is populated by the `preprocessor_node` with the results of previous tool calls: `customer_profile_full` (contains customer's full name), and `locker_availability`|
| 🔍 **Critic Resolution Agent** | It reviews the **Resolution Agent's** decision against the playbook and context, and either approves it, sends it back for correction, or escalates it to a human supervisor if the situation is too complex or risky to handle automatically. | It receives a data view called **`CriticResolutionView`**, which contains: `consolidated_event`, `customer_profile`, `locker_availability`, `playbook_context`, `escalation_signals`, and `resolution_output` |
| 🔍 **Critic Communication Agent** | It checks that the customer message has the right tone, accurately reflects the resolution, and contains no sensitive internal details before it is sent. Unlike the **Critic Resolution Agent**, it cannot request a revision — it can only approve or escalate. | It is a validation node that operates on a specific subset of the system state called the **`CriticCommunicationView`**, which includes: `consolidated_event`, `customer_profile`, `resolution_output`, and `communication_output` |

### Non-LLM Nodes

| Node | Role | Tools / Capabilities |
|---|---|---|
| 🧭 **Preprocessor** | It cleans the data, blocks malicious inputs, filters out routine noise, and fetches everything the agents need, all before a single LLM call is made. | The following tools are available to and invoked by the preprocessor: `lookup_customer_profile`, `check_locker_availability`, and `search_playbook` |
| 🪄 **Orchestrator** | It looks at the current state of the whiteboard **UnifiedAgentState** and decides who should act next, ensuring the pipeline always follows the correct sequence, enforces escalation rules, and never skips a critical validation step. | It does not directly invoke external tools like the preprocessor does. Instead, it serves as the **deterministic central router** of the system. Its _"tools"_ are actually the state variables it evaluates to decide the next path: **Guardrail State**, **Noise Flags**, **Completion Logic**, **Loop Management**, and **Escalation Enforcement**. While the preprocessor handles data retrieval, the orchestrator handles **policy enforcement and flow control.** |
| 📦 **Finalize** | It collects all the work done by every agent, packages it into one clean, auditable record, stamps the total processing time, and closes the case. | This node does not invoke any external tools or internal agent nodes. Its function is purely administrative within the graph: it reads the state (using the `RouterView` projection), packages the final results into a structured dictionary for the user, calculates the total end-to-end latency, and appends a final log entry to the `trajectory_log`. Once it completes, it transitions the workflow to the `END` state. |

### Why LangGraph?

- **Stateful graph workflow** — exception handling is a branching process, not a linear chain; LangGraph's state machine model fits naturally
- **Conditional routing** — different exception types take different paths through the agent team
- **Human-in-the-loop support** — low-confidence or high-value cases can pause for human approval
- **Observability** — each node's inputs/outputs are inspectable, which matters for debugging multi-agent behavior

---

## 🎬 Example Run

Here's the system handling a real exception end-to-end:

<!-- TODO: Replace the block below with a real (lightly trimmed) execution trace copied from logistics_agents.ipynb — this is the section reviewers read most -->

```
📥 EXCEPTION RECEIVED: Delivery attempt failed — customer not home (Order #48291)

[Triage Agent]        → Classified: FAILED_DELIVERY / severity: LOW
[Investigation Agent] → Customer has delivery window preference: evenings.
                        Package: non-perishable. 2 attempts remaining.
[Resolution Agent]    → Decision: Reschedule to next evening window (no human needed)
[Communication Agent] → Drafted SMS: "Hi! We missed you today. Your package
                        is rescheduled for tomorrow between 6–8 PM..."

✅ RESOLVED — no human escalation required
```

Full scenarios with complete saved outputs are demonstrated in [the notebook](logistics_agents.ipynb).

---

## 🚀 Getting Started

### Option 1 — Run in Google Colab (recommended)

No setup required:

1. Click the **Open in Colab** badge at the top of this README
2. Add your OpenAI API key when prompted (or set it in Colab Secrets as `OPENAI_API_KEY`)
3. Run the cells top to bottom

### Option 2 — Run locally

```bash
git clone https://github.com/jimmy-chihungchong/multi-agent-logistics-exception-handler.git
cd multi-agent-logistics-exception-handler
pip install -r requirements.txt
export OPENAI_API_KEY="sk-..."
jupyter notebook logistics_agents.ipynb
```

---

## 🧰 Tech Stack

- **Python 3.10+**
- **LangGraph** — multi-agent orchestration and state management
- **LangChain** — LLM integration, tools, and prompts
- **OpenAI API** — agent reasoning
- **Jupyter / Google Colab** — development and demo environment

---

## 📂 Repository Structure

```
.
├── README.md
├── LICENSE
├── requirements.txt            # Pinned dependencies
└── logistics_agents.ipynb      # Main notebook — runnable demo with saved outputs
```

---

## ⚠️ Limitations & Future Work

Being honest about scope is a feature, not a bug:

- Order/customer data is **mocked** — production use would integrate real OMS/TMS/carrier APIs
- No persistent memory across sessions; each exception is handled statelessly
- Resolution policies are prompt-encoded; a production system would externalize them as configurable business rules
- **Planned:** evaluation harness to measure resolution accuracy across a labeled exception dataset; cost/latency benchmarking per exception type

---

## 👤 About

Built by **Jimmy Chong** — software engineer with 10+ years of IT experience, specializing in AI agent development for business automation.

- 💼 LinkedIn: <!-- TODO: paste your LinkedIn profile URL -->
- 🐙 GitHub: [@jimmy-chihungchong](https://github.com/jimmy-chihungchong)

## 📄 License

MIT — see [LICENSE](LICENSE) for details.
