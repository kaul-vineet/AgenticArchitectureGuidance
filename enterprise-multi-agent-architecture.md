# 🤖 Enterprise Multi-Agent Architecture
> ✍️ **Author**: Vineet Kaul · Principal PM Architect · vineetkaul@microsoft.com

### Design Requirements/Priorities

- 🧩 No-code over custom features
- 📈 Scale over custom development
- 🏭 100+ agents in 6–12 months
- 🔵 Reduced complexity at every layer
- ☁️ Managed services over custom infrastructure
- 🎯 Simplest effective approach wins

> *Informed by: Microsoft CAF · Azure Architecture Center · DeepLearning.AI · HuggingFace Research · Gartner · MAST · smolagents*

---

## 🧠 Key Architectural Principles

*Consolidated from DeepLearning.AI, HuggingFace / MAST, smolagents, Microsoft CAF, Azure Architecture Center, and Azure Well-Architected Framework:*

| # | Principle | Source | Applied To This Architecture |
|---|---|---|---|
| 1️⃣ | **Minimise LLM calls** | HuggingFace / smolagents | Default to deterministic CS Topics. Every added LLM call is a new error vector. |
| 2️⃣ | **Default to Flows, escalate to Crews** | DeepLearning.AI | CS Topics + PA flows for 80%. Foundry with planning for 20% open-ended tasks. |
| 3️⃣ | **Clarification before action** | MAST (FM-2.2) | Ambiguity causes 50-point accuracy drop. Build disambiguation topics before every agentic branch. |
| 4️⃣ | **External verification — never self-check** | MAST (FM-3.3) | Every write action followed by deterministic read-back. Never ask the generating agent to confirm its own output. |
| 5️⃣ | **Externalize termination logic** | MAST (FM-1.5, FM-3.1) | Stopping conditions in Foundry orchestration code — max step count, loop detector. Never in LLM prompt. |
| 6️⃣ | **Tool argument quality is the #1 failure cause** | HuggingFace (>50% of failures) | Every connector needs explicit argument formats with examples, structured error responses, RFC3339 datetime formats. |
| 7️⃣ | **Consolidate tools** | HuggingFace / smolagents | Merge tools where possible — one call that does two things is strictly better than two sequential calls. |
| 8️⃣ | **Multi-agent is a reliability tax** | HuggingFace / AssetOpsBench | Every agent boundary costs accuracy. Add agent boundaries only at genuine compliance/security seams. |
| 9️⃣ | **Context hygiene at scale** | HuggingFace / Forge RL | Compaction step every N turns. Scope context passed between agents to what is strictly necessary. |
| 🔟 | **Sensitive state in side channel** | HuggingFace / Neuro SAN | Credentials, PII, session IDs never in LLM prompt. Use Key Vault references resolved at tool execution time. |
| 1️⃣1️⃣ | **Tool accuracy > 90% as deployment gate** | HuggingFace / AssetOpsBench | Below 90% tool accuracy, do not promote to production. |
| 1️⃣2️⃣ | **Human-in-the-loop for irreversible actions** | DeepLearning.AI · Microsoft CAF | PA approval flows as deterministic gates before write-backs to financial systems, CRM, or HR records. Two-phase: propose → dry-run → execute. |
| 1️⃣3️⃣ | **Use lowest complexity that reliably works** | Azure Architecture Center | Evaluate direct model call → single agent → multi-agent in that order. Do not add agent layers without a demonstrated failure reason. |
| 1️⃣4️⃣ | **Justify every agent boundary explicitly** | Azure Architecture Center | Only split work across agents when single-agent fails due to prompt complexity, tool overload, or security boundary. |
| 1️⃣5️⃣ | **Single responsibility per agent** | Azure Architecture Center · Microsoft CAF | Each agent has a narrowly defined domain. Monitor and prevent agent overlap — redundant agents degrade routing accuracy. |
| 1️⃣6️⃣ | **Right-size the model per agent role** | Azure Architecture Center | Not every agent needs the most capable model. Classification, extraction, and formatting agents can use smaller, cheaper models. |
| 1️⃣7️⃣ | **Validate output before passing downstream** | Azure Architecture Center | Low-confidence or malformed outputs must not propagate. Orchestrator checks quality and retries, requests clarification, or halts. |
| 1️⃣8️⃣ | **Surface errors explicitly — never swallow failures** | Azure Architecture Center | Include structured error responses on every tool. Downstream agents and orchestrators must be able to respond to failures. |
| 1️⃣9️⃣ | **Idempotency for all writes** | Microsoft Tech Community | Every write operation must be safe to repeat. Retries must not duplicate side effects. Assign an idempotency key to every state-changing tool call. |
| 2️⃣0️⃣ | **Kill switch — disable tools without redeployment** | Microsoft CAF | Define in advance how to stop tool execution, downgrade to read-only, isolate a capability, and communicate incidents fleet-wide. |
| 2️⃣1️⃣ | **Security trimming in every agent, not just the edge** | Azure Architecture Center | Agents have broad knowledge access but must not return data inaccessible to the user. Trimming must be enforced at each agent, not only at the gateway. |
| 2️⃣2️⃣ | **Content safety at every layer** | Azure Architecture Center | Apply guardrails at user input, tool calls, tool responses, and final output. Intermediate agents can introduce or propagate harmful content. |
| 2️⃣3️⃣ | **Scope agent authority to operations, not tools** | Microsoft Tech Community | Not "allow Jira" but "create ticket only." Apply rate limits and blast radius caps per agent per run. |
| 2️⃣4️⃣ | **Instrument all handoffs, not just final output** | Azure Architecture Center · Microsoft Azure Blog | Distributed agent orchestrations require tracing at every agent boundary and tool call, not only the terminal response. |
| 2️⃣5️⃣ | **Automated evaluation in CI/CD** | Microsoft Azure Blog | Every code or prompt change triggers automated quality and safety evaluation before release. Use LLM-as-Judge — exact-match assertions do not work for nondeterministic outputs. |
| 2️⃣6️⃣ | **Evaluate continuously in production** | Microsoft Azure Blog | Evaluate intent resolution, tool selection, task adherence, and response completeness throughout the agent lifecycle — not only at release. |
| 2️⃣7️⃣ | **Maintain agent inventory — shadow agents are a risk** | Microsoft CAF | Untracked deployments pose security and cost risks. Every agent must be registered, owned, and versioned. |
| 2️⃣8️⃣ | **Externalize and version-control prompts** | Azure Well-Architected Framework | Treat prompts as versioned configuration, not inline code. Implement controlled rollout for prompt changes. |

### 📚 References
- DeepLearning.AI — Agentic Design Patterns: https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/
- MAST Failure Taxonomy: https://huggingface.co/blog/ibm-research/itbenchandmast
- AssetOpsBench: https://huggingface.co/blog/ibm-research/assetopsbench-playground-on-hugging-face
- HuggingFace — Design Patterns for Agentic Workflows: https://huggingface.co/blog/dcarpintero/design-patterns-for-building-agentic-workflows
- smolagents: https://huggingface.co/blog/smolagents
- OpenEnv — Tool Argument Failures: https://huggingface.co/blog/openenv-turing
- Neuro SAN — Side Channel Pattern: https://huggingface.co/blog/danyoung/neuro-san-is-all-you-need
- Agent Leaderboard — Tool Quality Benchmarks: https://huggingface.co/blog/pratikbhavsar/agent-leaderboard
- Azure Architecture Center — AI Agent Orchestration Patterns: https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns
- Microsoft CAF — Governance and Security for AI Agents: https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/governance-security-across-organization
- Azure Well-Architected Framework — Application Design for AI Workloads: https://learn.microsoft.com/en-us/azure/well-architected/ai/application-design
- Microsoft Copilot Studio — Multi-Agent Patterns: https://learn.microsoft.com/en-us/microsoft-copilot-studio/guidance/architecture/multi-agent-patterns
- Microsoft Azure Blog — Agent Observability Best Practices: https://azure.microsoft.com/en-us/blog/agent-factory-top-5-agent-observability-best-practices-for-reliable-ai/
- Microsoft Tech Community — AI Production Architecture: https://techcommunity.microsoft.com/blog/educatordeveloperblog/ai-didn%E2%80%99t-break-your-production-%E2%80%94-your-architecture-did/4482848
- Microsoft for Developers — Designing Multi-Agent Intelligence: https://developer.microsoft.com/blog/designing-multi-agent-intelligence

---

## 📐 Recommendation Criteria

Every recommendation is evaluated against five criteria:

| | Criterion | Meaning |
|---|---|---|
| ⚡ | **High Speed of Development** | First agent ships in days, not weeks |
| 🧩 | **No-Code Over Custom Features** | Platform-native features even at 80% coverage, rather than custom code for 100% |
| 📈 | **Scale Over Custom Dev** | Works for agent #1 and agent #100 without re-architecting |
| 🏭 | **100+ Agents in 6–12 Months** | Agent factory — templates, ALM pipelines, domain reuse |
| 🔵 | **Reduced Complexity** | Fewest moving parts, fewest technologies, fewest custom servers |

---

### ⚙️ Flows vs. Crews

| | 🔵 Flow (Deterministic) | 🟠 Crew (Autonomous) |
|---|---|---|
| **Nature** | Sequential, explicit steps | Exploratory, emergent |
| **Reliability** | High | Lower — each step adds failure risk |
| **Best for** | Compliance, known workflows | Research, genuinely open-ended tasks |
| **Microsoft mapping** | CS Topics · PA Flows · Foundry Workflows | Foundry Agent Service with planning |
| **Use ratio** | **~80% of tasks** | **~20% of tasks** |

> 💡 **HuggingFace / smolagents principle**: "Regularize towards not using any agentic behaviour." Reduce LLM calls to the minimum. Every added LLM call is a new error vector.

---

### 🧠 Agentic Pattern Reliability Hierarchy

*Based on Andrew Ng (DeepLearning.AI) and MAST empirical research:*

```
RELIABILITY (most → least mature)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔧 Tool Use / Deterministic Flow   ████████████████████
   CS Topics, PA flows, known workflows
   → Default for ~80% of all tasks

🔁 Reflection / External Verification  ████████████████
   Separate critic agent or deterministic check
   → Add to Foundry agents for quality-sensitive outputs
   → NEVER let an agent verify its own output

📋 Planning                         ████████
   Dynamic task decomposition
   → Only for genuinely open-ended exploratory tasks
   → Never for known workflows

🤝 Multi-Agent Collaboration        ██████
   Each agent handoff costs ~10–15 accuracy points
   (68% single-agent → 47% multi-agent)
   → Use only where domain isolation is genuinely required
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## 1. 🏗️ Representative Architecture

### Platform Stack

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  👤 END USERS
  Teams · M365 Copilot · SharePoint · Web · Mobile
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  🚪 GATEWAY / MASTER AGENT (Copilot Studio)
  ┌─────────────────────────────────────────────────────────────────────────┐
  │  Routes intent to domain agents · Entra Agent ID                       │
  │  Can also call Specialist Connected Agents directly via A2A             │
  └──────────────┬──────────────────────────────────────────┬───────────────┘
                 │ primary                                  │ direct A2A
━━━━━━━━━━━━━━━━━┿━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┿━━━━━━━━━━━━━━━━
  📦 DOMAIN AGENTS  (one per business domain · MSFT platforms only)        │
  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────────────┐ │
  │  🟦 HR Domain    │  │  🟦 Finance      │  │  🟦 IT / Ops             │ │
  │  Copilot Studio  │  │  Copilot Studio  │  │  Copilot Studio          │ │
  │                  │  │                  │  │  + 🟧 Foundry            │ │
  └────────┬─────────┘  └────────┬─────────┘  └──────────────┬───────────┘ │
  ┌────────┴──────────────────────┴────────────────────────────┴───────────┐│
  │  🟦 CS domain agents      ──► child agents + connected agents          ││
  │  🟧 Foundry domain agents ──► Foundry / non-MSFT agents (A2A/REST)     ││
  └──────────────────────────────────┬───────────────────────────────────────┘│
                                    │                                         │
                                    ▼                                         │
━━━━━━━━━━━━━━━━━━━┯━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┯━━━━━━━━━━━━━━━━━━━━━━━━━┿
 🔧 SPECIALIST     │  🔗 SPECIALIST CONNECTED        │  🟧🟥 FOUNDRY /
 CHILD AGENTS      │  AGENTS                         │  NON-MSFT AGENTS
 (CS only)         │  (CS · Foundry · non-MSFT · A2A)│  (A2A / REST)
 CS domain         │  CS domain agents only          │  Foundry domain
 agents only       │                                 │  agents only
 ┌───────────────┐ │  ┌───────────────────────────┐  │  ┌───────────────────┐
 │ 🟦 Leave Mgmt │ │  │ 🟦 Benefits Bot            │  │  │ 🟧 Code Interp.   │
 │ 🟦 HR Policy  │ │  │ 🟧 Analytics Agent         │  │  │ 🟥 Agentforce     │
 │ 🟦 Expenses   │ │  │ 🟥 Agentforce              │  │  │ 🟥 ServiceNow     │
 └───────────────┘ │  └───────────────────────────┘  │  └───────────────────┘
━━━━━━━━━━━━━━━━━━━┷━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┷━━━━━━━━━━━━━━━━━━━━━━━━━━
  🔌 TOOL LAYER
  ┌────────────┬──────────────┬─────────────┬─────────────┐
  │⚡ Power     │🌐 APIs via   │🔗 MCP       │🛠️ Built-in  │
  │  Automate  │  Azure APIM  │  Servers    │  Tools      │
  │🟧 Foundry  │              │             │  Code Interp│
  │  Flows     │              │             │  File Search│
  └────────────┴──────────────┴─────────────┴─────────────┘
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  💾 DATA LAYER
  ┌──────────┬───────────┬──────────┬────────┬────────────┐
  │📊        │⚡ Redis   │🟧 Cosmos │🔍 Azure│📁 Data     │
  │Dataverse │(session   │  DB      │  AI    │  Lake      │
  │(CS +     │ state,    │(Foundry  │  Search│(compliance │
  │ admin)   │ hot cache)│ threads) │(RAG)   │ archive)   │
  └──────────┴───────────┴──────────┴────────┴────────────┘
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  🛡️ GOVERNANCE LAYER
  ┌──────────────────────────────┐
  │🏢 Agent 365                          │
  │  + Purview + Defender + App Insights │
  └──────────────────────────────┘
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  LEGEND
  🟦 Copilot Studio   🟧 Azure AI Foundry   🟥 Non-Microsoft (Agentforce, ServiceNow…)
```

### 📚 References
- Microsoft CAF — AI Agents: https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/
- Azure Architecture Center — AI Agent Orchestration Patterns: https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns
- DeepLearning.AI — Agentic Design Patterns: https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/
- MAST — Why Enterprise Agents Fail: https://huggingface.co/blog/ibm-research/itbenchandmast
- smolagents — Building Good Agents: https://huggingface.co/docs/smolagents/tutorials/building_good_agents
- Gartner — Multiagent Systems: https://www.gartner.com/en/articles/multiagent-systems
- Microsoft Agent Factory Blog: https://azure.microsoft.com/en-us/blog/agent-factory-the-new-era-of-agentic-ai-common-use-cases-and-design-patterns/

### 🏆 Recommendation
> ⚡🧩📈🏭🔵 **Use Copilot Studio as gateway and domain agents with Dataverse as the operational data layer. Add Foundry only for specialist agents. This requires no custom infrastructure, deploys through Power Platform Pipelines, and lets domain teams own their agents using no-code tooling.**

---

## 2. 🔄 How a User Instruction is Processed

```
👤 User: "Apply for leave and update my Salesforce records"
    │
    ▼
🚪 Gateway Agent (Copilot Studio)
    │  FLOW: Classifies intent → multi-domain
    │  Disambiguation check: if intent is ambiguous → enter
    │  clarification topic BEFORE routing (HuggingFace finding:
    │  ambiguity causes 50-point accuracy drop)
    │  correlationId generated and propagated downstream
    │
    ▼
📦 HR Domain Agent (Copilot Studio)
    │  FLOW: Routes to Leave Management child agent
    │  Passes typed parameters (userId, leaveType)
    │
    ▼
🔧 Leave Management Agent (CS connected child)
    │  TOOL USE: PA flow → reads HRIS (leave balance)
    │  TOOL USE: PA flow → submits leave request
    │  VERIFICATION GATE: reads back confirmation state
    │    deterministically — does NOT ask agent to confirm
    │    its own output (FM-3.3: hallucinated success)
    │  Returns: typed output (confirmationRef, managerEmail)
    │
    ▼
📦 HR Domain Agent aggregates → passes result to Gateway
    │
    ▼
🚪 Gateway Agent
    │  Routes CRM update to Agentforce (A2A + correlationId)
    │
    ▼
🟥 Agentforce Agent
    │  Receives: A2A payload with full chat history + contextId
    │  TOOL USE: Updates Salesforce record
    │  Returns: CRM confirmation
    │
    ▼
🚪 Gateway aggregates both results → composes final response
    │
    ▼
👤 "Leave approved (Ref: L2026-441). Salesforce updated."
```

### 📋 Key Safeguards in the Flow

| Risk | Safeguard Applied |
|---|---|
| Agent verifies its own output (FM-3.3) | Deterministic read-back after every write action |
| Ambiguous user input causes wrong routing | Explicit disambiguation topic before any agentic branch |
| Agent loops without progress (FM-1.3) | Max step count + loop detector in Foundry orchestration code |
| Agent does not know when to stop (FM-1.5) | Termination conditions in code — never in LLM prompt |
| Tool argument malformed (>50% of failures per HuggingFace) | Structured error responses with format examples on every connector |

### 📚 References
- Copilot Studio — Multi-Agent Patterns: https://learn.microsoft.com/en-us/microsoft-copilot-studio/guidance/multi-agent-patterns
- AssetOpsBench — Enterprise Agent Benchmarking: https://huggingface.co/blog/ibm-research/assetopsbench-playground-on-hugging-face
- OpenEnv — Tool-Using Agents Evaluation: https://huggingface.co/blog/openenv-turing
- DeepLearning.AI — Tool Use Pattern: https://www.deeplearning.ai/the-batch/agentic-design-patterns-part-3-tool-use/
- A2A Protocol: https://a2a-protocol.org/latest/

### 🏆 Recommendation
> ⚡🔵 **This flow requires zero custom code. CS Topics handle routing, Power Automate handles tool calls, A2A/REST handles Agentforce. Build this in 1–2 sprints per domain. Do not add reflection or planning until the basic deterministic flow is stable and evaluated.**

---

## 3. ⚖️ Copilot Studio vs. Foundry — As Parent / Master Agent

### 🟦 Copilot Studio as Parent

| ✅ Pros | ❌ Cons |
|---|---|
| · No-code authoring — teams own independently | · Sequential orchestration — child agents called one-at-a-time |
| · Native M365 channels — Teams, SharePoint, M365 Copilot zero-setup | · Session limits — 30 min idle, 60 min total, 100 turns |
| · Shared container for CS children — variables, auth, Dataverse natively shared | · Instructions size limit — hard cap of 8,000 characters |
| · 1,400+ connectors out of the box | · Model selection limited — locked to Microsoft-managed models |
| · Agent templates + Component Collections — repeatable factory model | |
| · Power Platform Pipelines — built-in Dev→Test→Prod ALM | |
| · Fastest time-to-first-agent across all platforms | |
| · Agent 365 + CoE Toolkit governed natively in M365 Admin Center | |

### 🟧 Foundry Agent Service as Parent

| ✅ Pros | ❌ Cons |
|---|---|
| · Parallel agent invocation via Foundry Workflows — fan-out/fan-in | · Slower time-to-agent vs CS even with low-code tools |
| · Foundry Flows (low-code) — visual drag-and-drop workflow designer | · Foundry Connected Agents: max depth = 2 (hard limit) |
| · OOB Foundry connectors to Azure, M365, and partner services | · Session management — threadId registry pattern required |
| · Deterministic Workflows — state machines for regulated processes | · PaaS licensing cost on top of token costs |
| · Native Memory (preview) — automatic cross-session user profiles | · Foundry → CS child integration still in preview |
| · BYO Cosmos DB for data sovereignty (Standard setup only) | |
| · Full OpenTelemetry distributed tracing | |
| · Code Interpreter + File Search built in | |

> 💡 **Foundry is low-code too.** Foundry Agent Service includes a visual Flows designer, pre-built connectors, and a portal UI for agent creation. The distinction from Copilot Studio is target audience and depth of control, not code vs. no-code.

### 📚 References
- Microsoft — Copilot Studio vs Foundry: https://techcommunity.microsoft.com/blog/microsoft-security-blog/microsoft-copilot-studio-vs-microsoft-foundry-building-ai-agents-and-apps/4483160
- Microsoft CAF — Platform Selection: https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/technology-solutions-plan-strategy
- Foundry — Flows and Workflows: https://learn.microsoft.com/en-us/azure/ai-foundry/agents/how-to/
- Microsoft Inside Track — CS or Foundry: https://www.microsoft.com/insidetrack/blog/customer-questions-answered-should-i-use-copilot-studio-or-azure-ai-foundry-to-build-my-agent/

### 🏆 Recommendation

| Criterion | Verdict |
|---|---|
| ⚡ Speed | **Copilot Studio wins** — hours to first agent vs. days |
| 🧩 No-code | **Copilot Studio wins** — no-code native |
| 📈 Scale | **Copilot Studio wins** — agent templates, Component Collections, PP Pipelines |
| 🏭 100+ agents | **Copilot Studio wins** — factory model built for this scale |
| 🔵 Reduced complexity | **Copilot Studio wins** — single platform, Dataverse only, no extra infra |

> **Use Copilot Studio as the parent/master agent. Introduce Foundry as parent only for domains requiring parallel execution, deterministic state-machine workflows, or compliance-grade BYO storage. Target fewer than 3 Foundry-parent domains out of your total agent estate.**

---

## 4. ⚖️ Copilot Studio vs. Foundry — As Child / Connected Agent

### 🟦 Copilot Studio as Child

| ✅ Pros | ❌ Cons |
|---|---|
| · Shared container with CS parent — variables, auth, Dataverse, PA connectors natively shared *(inline child agents only)* | · Separate transcript — must correlate by session ID *(connected agents only; inline child agents share parent transcript)* |
| · Typed I/O contracts (YAML) — structured, parseable return values | · Citation propagation — known limitation with knowledge sources |
| · Independent ALM lifecycle — version and deploy without touching parent *(connected agents only)* | · One extra orchestration hop — marginal latency cost |
| · Reusable across multiple parent agents | |
| · Team ownership — domain experts own end to end *(connected agents only)* | |
| · Inline option — zero-latency, zero-boundary for tightly scoped sub-tasks *(child agents only)* | |

### 🟧 Foundry as Child

| ✅ Pros | ❌ Cons |
|---|---|
| · Complex computation — Code Interpreter, large RAG, multi-step chains | · Opaque to parent — text-only return, no typed variable contract |
| · Foundry Flows (low-code) — visual builder for agent sub-workflows | · Max depth 2 — cannot chain further Foundry sub-agents |
| · Native Memory (preview) — cross-session without custom code | · CS → Foundry integration still in preview |
| · BYO Cosmos DB for PII-regulated outputs | · Infrastructure awareness — Standard setup requires Cosmos DB config |

### 📚 References
- Copilot Studio — Add Other Agents: https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-add-other-agents
- Copilot Studio — Child/Connected Agent I/O: https://microsoft.github.io/mcscatblog/posts/copilot-studio-child-connected-agents-inputs-outputs/
- Foundry — Multi-Agent Workflows: https://techcommunity.microsoft.com/blog/azure-ai-foundry-blog/building-a-digital-workforce-with-multi-agents-in-azure-ai-foundry-agent-service/4414671
- Salesforce — Agentforce A2A: https://www.salesforce.com/blog/agent-interoperability/

### 🏆 Recommendation

| Criterion | Inline Child Agent | Connected Agent |
|---|---|---|
| ⚡ Speed | Fastest — no boundary, no hop | Fast — team builds independently in hours |
| 🧩 No-code | ✅ CS Topics — fully no-code | ✅ CS — fully no-code |
| 📈 Scale | Tightly scoped sub-tasks only | ✅ Reusable across multiple parents, independent ALM |
| 🏭 100+ agents | Not a factory unit — embedded in parent | ✅ Factory model — one per domain function, template-based |
| 🔵 Reduced complexity | Shared transcript, shared container — zero extra infra | Own transcript (correlationId required), own ALM |
| 🔗 Agent connectivity | CS domain agents only | CS domain agents (A2A) · Foundry domain agents cannot call CS connected |

> **Use CS inline topics for tightly scoped sub-tasks within the same domain where shared context and zero-latency matter. Use CS connected agents when the sub-domain needs independent governance, team ownership, or reuse across multiple parents. Foundry agents are always in the connected tier (A2A) — never inline. Non-MSFT agents (Agentforce, ServiceNow) are connected tier only. Target ratio: 60% CS inline child, 25% CS connected, 10% Foundry connected, 5% non-MSFT connected.**

---

## 5. 🎯 Agent Orchestration — Domain Agents vs. Star Topology Anti-Pattern

### ✅ Recommended: Domain-Level Agents (Hierarchical)

```
👤 User
    │
    ▼
🚪 Gateway (1 agent — routes only, owns zero tools)
    │
    ├──► 📦 HR Domain Agent      → manages ≤8 child/connected agents + tools
    ├──► 📦 Finance Domain Agent  → manages ≤8 child/connected agents + tools
    ├──► 📦 IT Domain Agent      → manages ≤8 child/connected agents + tools
    └──► 📦 Sales Domain Agent   → manages ≤8 child/connected agents + tools

Rules:
  Each domain agent manages ≤ 8–10 specialist agents (child or connected)
  Each domain agent has a bounded tool set — keep scope focused
  Each domain agent has its own Entra identity + team owner
  Each domain agent maintains a dynamic task ledger (auditable record
    of subtask state — not just LLM mental model)
```

### ❌ The Star Topology Anti-Pattern (Many Agents to One Orchestrator)

```
                    ANTI-PATTERN
    HR Agent ─────────┐
    Finance Agent ────┤
    IT Agent ─────────┤──► ⚠️ SINGLE SHARED ORCHESTRATOR
    Sales Agent ──────┤         (handles all 100 agents)
    ... 95 more ───────┘
```

| ⚠️ Disadvantage | What Happens in Practice |
|---|---|
| 🔴 **Tool overload collapses quality** | LLM routing accuracy drops sharply as tool count grows. A single orchestrator for 100 agents accumulates hundreds of tools — routing becomes unreliable. |
| 🔴 **Context window floods** | All agent interactions accumulate in one shared thread. Context fills with unrelated domain history. Research confirms this causes FM-1.4 (Loss of Conversation History). |
| 🔴 **One rate limit ceiling for all** | All 100 agents share one model endpoint's TPM quota. A Finance spike throttles HR users. |
| 🔴 **No domain security boundary** | HR and Finance queries share context. Per-domain security trimming cannot be enforced. |
| 🔴 **One failure = full outage** | A bug takes all 100 agents offline simultaneously. |
| 🔴 **ALM becomes impossible** | Updating any agent requires testing the shared orchestrator against all 100 agents. |
| 🔴 **No team ownership** | Ownership ambiguity leads to governance failure at scale. |
| 🟡 **Unpredictable cost** | Simple FAQ queries cost the same as complex multi-tool tasks. |

### 📚 References
- Azure Architecture Center — Agent Design Patterns: https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns
- AssetOpsBench — Multi-Agent Accuracy Penalty: https://huggingface.co/blog/ibm-research/assetopsbench-playground-on-hugging-face
- DeepLearning.AI — Multi-Agent Collaboration: https://www.deeplearning.ai/the-batch/agentic-design-patterns-part-5-multi-agent-collaboration/
- Neuro SAN — Decentralized Routing: https://huggingface.co/blog/danyoung/neuro-san-is-all-you-need

### 🏆 Recommendation

| Criterion | Verdict |
|---|---|
| ⚡ Speed | Domain agents — each team owns and deploys independently |
| 🧩 No-code | CS Topics handle routing — no code needed |
| 📈 Scale | Domain model scales linearly — new domain = one new domain agent |
| 🏭 100+ agents | 8–12 domain agents × 8–10 specialist agents = 64–120 agents; fits target |
| 🔵 Reduced complexity | Each domain agent is simple; complexity stays local to its domain |

> **Never use a single orchestrator for all agents. One domain agent per business function. Gateway routes intent only — owns zero tools. Each domain agent manages ≤ 8–10 specialist agents (child or connected) with a dynamic task ledger. This architecture scales from 10 to 200 agents without redesign.**

---

## 6. 💾 Session Management, Transcript Storage & Persistent Storage

### 🔄 Session Management

| Platform | How Session is Natively Managed | Mitigation |
|---|---|---|
| **Copilot Studio** | Automatic — session variables maintained per conversation; 30 min idle · 60 min total · 100 turns hard limits | Inactivity reset topic · Store key state to session variables · Async pattern for tasks >5 min |
| **Foundry** | Automatic — thread-based; each session is a thread (up to 100k messages); no time limit; threadId maps to userId | **Redis** for userId→threadId registry (sub-ms lookup) · Set own retention policy · Use Memory feature for cross-session continuity |
| **Agentforce** | Salesforce-managed — session state owned by Salesforce platform | Pass correlationId in every A2A call to maintain continuity across platforms |

### 🧠 Context Architecture

*HuggingFace / Forge RL research finding: attention dilution degrades performance as context grows. Implement explicit compaction:*

| Tier | Name | What It Holds | CS Implementation | Foundry Implementation | Trigger |
|---|---|---|---|---|---|
| **Tier 1** | Active Context | Current task · recent tool results · active variables | Managed automatically | Managed automatically | — |
| ↓ | *Compaction step* | — | — | — | N turns or token threshold reached |
| **Tier 2** | Session Store | Intermediate results · extracted entities · task state | Dataverse table written via PA flow | Redis (hot session state, userId→threadId) + Memory feature (preview) | Compaction step fires |
| ↓ | *Archive step* | — | — | — | Session close |
| **Tier 3** | Long-Term Storage | User preferences · past conversation summaries · learned facts | Dataverse custom table (read via PA flow on session start) | **CS-heavy:** Dataverse via PA flow · **Foundry-heavy:** CosmosDB · **Preview acceptable:** Foundry Memory Feature | Session close |

### 📝 Conversation Transcript Storage

| Platform | Where Stored | Default Retention | For Compliance >30 Days |
|---|---|---|---|
| **Copilot Studio** | Dataverse `ConversationTranscript` (auto) | **30 days** | Synapse Link → Azure Data Lake |
| **Foundry** | Cosmos DB (Standard) or Microsoft-managed (Basic) | **No auto-expiry — you set it** | TTL policy on Cosmos DB containers |
| **Agentforce** | Salesforce Event Monitor | Salesforce-managed | Export via MuleSoft → Azure Data Lake |

**Cross-agent transcript correlation:**
- CS inline child → single parent transcript (no correlation needed)
- CS connected child → separate transcript → join by `correlationId`
- Foundry child → Foundry thread → join by `correlationId`
- Agentforce → Salesforce Event Monitor → export and join by `correlationId`

### Decision Matrix — Which Technology Serves Each Requirement

*Products in scope: Redis (cache) · CosmosDB (session) · SQL Server (profile) · Dataverse · Foundry Memory Feature*

---

#### 🟦 Copilot Studio

| Requirement | Storage Recommendation | Why — cannot be met by any other product |
|---|---|---|
| 📝 **Full conversation transcript** | **Dataverse** | • Auto-written by CS runtime to `ConversationTranscript` table — zero setup, no custom code<br>• Native 30-day retention; extend via Synapse Link → Azure Data Lake for compliance<br>• Redis: volatile by default — not durable for compliance transcripts<br>• CosmosDB: not in CS native stack; requires custom integration<br>• SQL Server: not in CS data path; requires custom connector<br>• Foundry Memory Feature: unavailable in CS context |
| 👤 **User profile across sessions** | **Dataverse** | • Custom Dataverse table read via PA flow on session start — native, no extra infra<br>• Fully integrated with CS security model and Entra ID<br>• Redis: no native CS connector; volatile without persistence config<br>• SQL Server: possible but adds custom connector complexity; Dataverse already provisioned<br>• CosmosDB: not natively reachable from CS without custom code<br>• Foundry Memory Feature: not available in CS context |
| 🔍 **Agent step recording** | **Dataverse** | • Custom table written via PA flow captures decision points and tool call outcomes<br>• Same managed identity and ALM lifecycle as the agent — no extra infrastructure<br>• Redis: not a durable audit log; evicts data without persistence config<br>• SQL Server: not natively accessible from CS Power Automate flows<br>• CosmosDB: no native CS integration path<br>• Foundry Memory Feature: not applicable to CS |
| ⚡ **Low latency (< 50ms)** | **CS Native Session Variables** | • Within-session state is held in CS runtime memory — no external store needed, zero network hop<br>• None of the 5 products applies: all are external stores with network overhead<br>• Dataverse (200–500ms REST) is the closest listed option but exceeds the 50ms target<br>• Redis, CosmosDB, SQL Server, Foundry Memory Feature: not in the CS native data path |

---

#### 🟧 Foundry AI

| Requirement | Storage Recommendation | Why — cannot be met by any other product |
|---|---|---|
| 📝 **Full conversation transcript** | **CosmosDB** | • `thread-message-store` stores every message verbatim<br>• `agent-entity-store` captures every LLM input/output with full payload and timestamp<br>• Redis: volatile — data lost on eviction without persistence config; not a durable transcript store<br>• SQL Server: no native schema for threaded conversations; concurrent writes degrade performance at scale<br>• Dataverse: 200–500ms REST overhead + service protection throttling — unsuitable for high-volume real-time writes<br>• Foundry Memory Feature: stores extracted summaries only; raw transcript is permanently lost |
| 👤 **User profile across sessions** | **Option 1: Foundry Memory Feature** ⚠️ Preview<br>**Option 2: CosmosDB** — Foundry-heavy architecture<br>**Option 3: Dataverse via PA flow** — CS-heavy architecture | **Option 1 — Foundry Memory Feature** *(when preview is acceptable)*<br>• Purpose-built for cross-session knowledge — AI-driven auto-extraction, zero custom code<br>• Hybrid search retrieval (semantic + keyword) for context-aware profile reads<br>• Zero infrastructure — portal toggle only<br>• ⚠️ Preview — not GA; do not use as sole production dependency<br><br>**Option 2 — CosmosDB** *(when most agents in the architecture are Foundry-based)*<br>• CosmosDB is already in the stack for transcript + step recording — no new infrastructure<br>• Durable, single-digit ms point reads by userId — direct SDK, no intermediary<br>• Structured profile schema — deterministic, no AI extraction needed for known enterprise fields<br>• GA — no preview risk<br><br>**Option 3 — Dataverse via PA flow** *(when most agents in the architecture are Copilot Studio-based)*<br>• Dataverse is already the dominant store — no new infrastructure<br>• Profile read once at session start — 200–500ms acceptable for session init, not mid-turn<br>• Requires PA flow intermediary (Foundry → PA → Dataverse); total ~1–2s at session start<br>• ⚠️ Validate Dataverse service protection limits at concurrent session scale |
| 🔍 **Agent step recording** | **CosmosDB** | • `agent-entity-store` natively captures every reasoning step, tool call, and model decision with full payload and timestamp<br>• No custom schema required — Foundry writes this automatically in Standard setup<br>• Redis: not a durable audit log; data lost on eviction without persistence config<br>• SQL Server: high-volume concurrent agent writes strain relational engines at scale<br>• Dataverse: service protection limits and 200–500ms write latency unsuitable for high-frequency step logging<br>• Foundry Memory Feature: not designed for execution step capture; no equivalent audit structure |
| ⚡ **Low latency (< 50ms)** | **Redis** | • Sub-millisecond key-value lookups — userId→threadId mapping resolved per request<br>• Hot user profile cache for prompt injection within a turn<br>• Scales horizontally with Azure Cache for Redis<br>• CosmosDB: single-digit ms — adequate for reads but not sub-ms for high-frequency per-turn lookups<br>• SQL Server: 10–100ms, degrades significantly under concurrent agent load<br>• Dataverse: 200–500ms REST overhead — fails the 50ms requirement<br>• Foundry Memory Feature: async extraction — not designed for real-time per-turn lookups |

---

#### 🟥 Agentforce

> ⚠️ Agentforce runs on Salesforce's own infrastructure. None of the five products (Redis, CosmosDB, SQL Server, Dataverse, Foundry Memory Feature) apply natively within Agentforce. Recommendations below follow Salesforce best practice. Integration with the Microsoft stack is via A2A/REST with `correlationId` propagation.

| Requirement | Storage Recommendation | Why — cannot be met by any other product |
|---|---|---|
| 📝 **Full conversation transcript** | **Salesforce Event Monitoring** | • Platform-native audit trail for all agent interactions — no custom setup required<br>• Per Salesforce best practice: Event Monitoring is the authoritative source for Agentforce conversation logs<br>• For long-term retention: export via MuleSoft → Azure Data Lake; join Microsoft-side traces by `correlationId`<br>• None of the 5 Microsoft products are in the Agentforce runtime data path |
| 👤 **User profile across sessions** | **Salesforce Data Cloud** | • Unified customer profile across all Salesforce products — purpose-built for cross-session identity<br>• Per Salesforce best practice: Data Cloud consolidates CRM, Marketing Cloud, and agent interaction data into a single profile<br>• Einstein AI features provide semantic retrieval over unified profiles natively<br>• Agentforce reads Data Cloud profiles without custom integration<br>• None of the 5 Microsoft products are accessible within Agentforce without an external API call |
| 🔍 **Agent step recording** | **Salesforce Event Monitoring + Agentforce Action Logs** | • Event Monitoring captures all agent actions, tool invocations, and decisions with timestamps<br>• Per Salesforce best practice: combine Event Monitoring with Agentforce-native Action Logs for full execution audit<br>• For Microsoft-side correlation: export logs and join by `correlationId` in Azure Data Lake<br>• None of the 5 Microsoft products are in the Agentforce execution path |
| ⚡ **Low latency (< 50ms)** | **Salesforce Platform Cache** | • Salesforce's native cache layer (org cache + session cache) — sub-100ms reads within the Salesforce runtime<br>• Per Salesforce best practice: Platform Cache is the recommended store for hot session state within Agentforce flows<br>• None of the 5 Microsoft products are accessible within the Salesforce runtime without a round-trip API call, which exceeds the 50ms target |

---

### Can Dataverse Replace Any of These?

With low latency (< 50ms) as a hard requirement, Dataverse qualifies only where it fully meets the requirement:

| Technology | Replaceable by Dataverse? | Reason |
|---|---|---|
| 🗄️ **SQL Server** | ❌ No | Dataverse REST API latency exceeds 50ms target for agent-speed concurrent reads and writes. SQL Server outperforms Dataverse for concurrent agent workloads. |
| 🧠 **Memory Feature** | ❌ No | Dataverse can store user profiles but cannot replicate AI-driven auto-extraction, semantic consolidation, or hybrid search retrieval. |
| ⚡ **Redis** | ❌ No | Dataverse REST API (200–500ms) cannot match Redis sub-millisecond latency. |
| 🟧 **Cosmos DB** | ❌ No | Dataverse cannot handle Foundry raw thread message volume, `agent-entity-store` LLM audit structure, or the latency requirements for real-time thread reads/writes. |

> ⚠️ **With low latency as a hard requirement, Dataverse does not fully qualify as a replacement for any of the four storage technologies in a Foundry agent scenario.** Dataverse remains correct for non-latency-sensitive operations: agent registry metadata, governance configuration, and CS transcript storage (auto-written, not in the real-time agent response path).

---

### Recommended Storage Assignment

```
📝 Full conversation transcript
    Copilot Studio  →  Dataverse (auto, zero setup — CS only)
    Foundry agents  →  Cosmos DB (Standard setup)
                       (no low-latency alternative for full
                        raw thread + LLM audit trail)

👤 User profile across sessions
    Architecture lean drives the choice — provision one store, not one per agent type:
    CS-heavy        →  Dataverse (via PA flow on session start)
                       Already the dominant store · zero new infra
    Foundry-heavy   →  CosmosDB (already in stack for transcript + steps)
                       Direct SDK read · single-digit ms · GA
    Preview OK      →  Foundry Memory Feature
                       AI-driven extraction · semantic retrieval · zero infra

🔍 Agent step recording
    All Foundry     →  Cosmos DB agent-entity-store
                       + App Insights (latency, failure metrics)

⚡ Low latency session state
    userId→threadId →  Redis (sub-ms lookups during active sessions)
    User profile    →  Memory Feature retrieval OR Redis cache
```

### Updated Infrastructure Matrix

```
CS-heavy architecture (most agents on Copilot Studio):
    ✅ Dataverse          → Transcripts (auto) · user profiles · agent metadata
    ✅ Redis              → NOT REQUIRED for CS-only
    ❌ CosmosDB           → NOT REQUIRED
    ❌ Foundry Memory     → NOT REQUIRED

Foundry-heavy architecture (most agents on Foundry):
    ✅ CosmosDB           → Transcripts · agent step recording · user profiles
    ✅ Redis              → Low latency session state (userId→threadId)
    ✅ App Insights       → Observability and metrics
    ❌ Foundry Memory     → NOT REQUIRED (CosmosDB covers profiles)

Mixed / Preview acceptable:
    ✅ Foundry Memory     → User profiles — AI-driven · zero infra · ⚠️ Preview
    ✅ CosmosDB           → Transcripts + agent steps (if Foundry Standard)
    ✅ Redis              → Low latency session state
    ✅ Dataverse          → CS transcripts (auto) · governance metadata
```

### 📚 References
- Copilot Studio — Transcript Controls: https://learn.microsoft.com/en-us/microsoft-copilot-studio/admin-transcript-controls
- Foundry — Threads, Runs, Messages: https://learn.microsoft.com/en-us/azure/ai-foundry/agents/concepts/threads-runs-messages
- Foundry — Memory: https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/what-is-memory
- Foundry Standard Agent Setup: https://learn.microsoft.com/en-us/azure/ai-foundry/agents/how-to/standard-agent-setup
- Cosmos DB Foundry Integration: https://learn.microsoft.com/en-us/azure/cosmos-db/gen-ai/azure-agent-service
- Introducing Memory in Foundry: https://devblogs.microsoft.com/foundry/introducing-memory-in-foundry-agent-service/
- Forge RL — Context Compaction: https://huggingface.co/blog/MiniMax-AI/forge-scalable-agent-rl-framework-and-algorithm
- DeepLearning.AI — Agent Memory: https://www.deeplearning.ai/short-courses/llms-as-operating-systems-agent-memory/
- mem-agent Persistent Memory Research: https://huggingface.co/blog/driaforall/mem-agent
- Dataverse Capacity for Agents: https://www.microsoft.com/en-us/power-platform/blog/2025/12/04/dataverse-capacity/

### 🏆 Recommendation
> **User profile store follows architecture lean — provision one store, not one per agent type. CS-heavy: Dataverse is the only store needed (transcripts auto-written, profiles via PA flow, session state in variables). Foundry-heavy: CosmosDB covers transcripts, agent steps, and profiles in one store; add Redis for low-latency session state. Preview acceptable: Foundry Memory Feature replaces CosmosDB for profiles with zero infrastructure. Do not mix profile stores across agent types — pick based on where your architecture leans and stay consistent.**

---

## 7. 🔌 Infrastructure Requirements — Definitive Answer

### ✅ Required (Managed — Minimal Setup)

| Infrastructure | Required? | When | Setup Effort |
|---|---|---|---|
| **Dataverse** | ✅ Always | CS transcripts (auto) · agent metadata · governance · admin config · user profiles when CS-heavy | Zero — auto-provisioned |
| **Redis** | ✅ For Foundry agents | Low latency session state: userId→threadId lookups during active sessions | Medium — Azure Cache for Redis |
| **Azure AI Search** | ✅ If RAG needed | Knowledge base > 500 docs or semantic similarity retrieval | Low — add as knowledge source |
| **Azure APIM** | ✅ For production | External REST API governance — rate limiting, auth, logging | Low — managed service |
| **Azure Data Lake** | ✅ If compliance | Transcript retention > 30 days | Low — Synapse Link connector |

### 🟡 Conditional — Driven by Architecture Lean

| Infrastructure | When Needed | When NOT Needed |
|---|---|---|
| **Cosmos DB** | **Foundry-heavy architecture** — transcripts + agent step recording + user profiles in one store · OR compliance mandate (CMK, data residency, full LLM audit trail) | CS-heavy architecture — Dataverse covers all storage needs. Never needed for CS. |
| **Foundry Memory Feature** | **Preview acceptable** — replaces Cosmos DB for user profiles with zero infrastructure; AI-driven extraction and semantic retrieval | CS-heavy (Dataverse covers profiles) · Foundry-heavy (Cosmos DB covers profiles) · any architecture where preview dependency is a production risk |

### ❌ Not Required — Explicitly Ruled Out

| Infrastructure | Why Not Needed | What Replaces It |
|---|---|---|
| **SQL Server / Azure SQL** | Dataverse handles admin/governance data; Cosmos DB handles Foundry agent data; no role for a separate SQL tier | Dataverse (admin) · Cosmos DB (Foundry) |
| **Custom vector DB** | Azure AI Search is the managed vector store with no custom infra | Azure AI Search |
| **Custom audit database** | Agent 365 + Purview + App Insights + Cosmos DB agent-entity-store cover all audit needs | Agent 365 + Purview + App Insights |

### 📊 Infrastructure Matrix

```
CS-heavy architecture (most agents on Copilot Studio):
    ✅ Dataverse          → REQUIRED (transcripts auto · user profiles · metadata)
    ✅ Azure AI Search    → IF knowledge base > 500 docs
    ✅ Azure APIM         → FOR external API governance
    ✅ Azure Data Lake    → IF compliance requires >30 day retention
    ❌ Redis              → NOT REQUIRED
    ❌ Cosmos DB          → NOT REQUIRED
    ❌ Foundry Memory     → NOT REQUIRED

Foundry-heavy architecture (most agents on Foundry):
    ✅ Dataverse          → Governance metadata · CS agent transcripts if any
    ✅ Cosmos DB          → REQUIRED (transcripts + agent steps + user profiles)
    ✅ Redis              → REQUIRED (low latency userId→threadId session state)
    ✅ Azure AI Search    → IF knowledge base > 500 docs
    ✅ Azure APIM         → FOR external API governance
    ✅ Azure Data Lake    → IF compliance requires >30 day retention
    ❌ Foundry Memory     → NOT REQUIRED (Cosmos DB covers profiles)

Preview acceptable (either lean):
    ✅ Foundry Memory     → Replaces Cosmos DB for user profiles · zero infra · ⚠️ Preview
    ✅ Cosmos DB          → Still REQUIRED for transcripts + agent steps (Foundry Standard)
                           NOT REQUIRED for profiles if Foundry Memory is used
    ✅ Redis              → REQUIRED for Foundry agents (session state)
```

### 📚 References
- Foundry Agent Setup Options: https://learn.microsoft.com/en-us/azure/ai-foundry/agents/how-to/standard-agent-setup
- Azure Cache for Redis: https://learn.microsoft.com/en-us/azure/azure-cache-for-redis/
- Azure AI Search: https://learn.microsoft.com/en-us/azure/search/semantic-search-overview
- Cosmos DB Foundry: https://learn.microsoft.com/en-us/azure/cosmos-db/gen-ai/azure-agent-service

### 🏆 Recommendation
> **Infrastructure follows architecture lean — provision for your dominant platform, not per agent type. CS-heavy: Dataverse only — no Redis, no Cosmos DB, no Foundry Memory Feature. Foundry-heavy: Cosmos DB (transcripts + agent steps + user profiles) + Redis (session state) — no Foundry Memory Feature needed. Preview acceptable: Foundry Memory Feature replaces Cosmos DB for profiles only; Cosmos DB is still required for transcripts and agent step recording. Every component must be justified by the architecture — not assumed as a default.**

---

## 8. 🔧 Agentic Framework and Agent 365 — Where They Fit

### 🤖 Microsoft Agent Framework (Semantic Kernel + AutoGen)

*Merged into a single open-source SDK October 2025.*

| Use It When | Skip It When |
|---|---|
| · ✅ Foundry agents need deterministic routing (not LLM-driven) | · ❌ Pure Copilot Studio no-code agents |
| · ✅ Embedding agent logic into existing .NET / Python apps | · ❌ Standard workflow automations (Power Automate is correct) |
| · ✅ Building Reflection loops or custom Planning pipelines in Foundry | · ❌ Team-authored agents |
| · ✅ LLM-as-a-Judge evaluation harnesses | · ❌ Agents with standard connector tool use |

### 🏢 Microsoft Agent 365

| Capability | Value at 100-Agent Scale |
|---|---|
| 🗂️ **Agent Registry** | All 100 agents in one view including shadow agents |
| 🔐 **Access Governance** | Blueprint-level policies applied fleet-wide |
| 📊 **Role-Based Dashboards** | IT, Security, Business each see relevant metrics |
| 🛡️ **Security Integration** | Defender detects anomalous tool call patterns |
| 🔗 **Interoperability Governance** | Controls which agents can call which other agents |

**What Agent 365 does NOT cover:**
- Agentforce logs → Salesforce Event Monitor → export, join by `correlationId`
- Foundry message-level traces → Azure Monitor + App Insights
- Power Platform maker-level inventory → CoE Toolkit

### 📋 CoE Toolkit vs. Agent 365

| | Agent 365 | CoE Toolkit |
|---|---|---|
| **Layer** | M365 consumption / governance | Power Platform maker / builder |
| **Covers** | Who uses agents, performance, security | Who built agents, which environment, ALM health |
| **Availability** | Frontier program (limited) | GA, free, open source |

### 📚 References
- Microsoft Agent Framework: https://azure.microsoft.com/en-us/blog/introducing-microsoft-agent-framework/
- Microsoft Agent 365: https://www.microsoft.com/en-us/microsoft-agent-365
- Agent 365 Announcement: https://www.microsoft.com/en-us/microsoft-365/blog/2025/11/18/microsoft-agent-365-the-control-plane-for-ai-agents/
- Power Platform CoE Starter Kit: https://learn.microsoft.com/en-us/power-platform/guidance/coe/starter-kit

### 🏆 Recommendation
> **Deploy CoE Toolkit immediately — free, GA, provides full inventory foundation for 100 agents. Enroll in Agent 365 Frontier now. Skip Agent Framework for CS agents — it adds developer complexity where no-code suffices. Use it only for Foundry agents requiring deterministic routing or custom evaluation.**



---

## 9. 📌 Final Summary

### ✅ What to Build With

| Layer | Technology | Notes |
|---|---|---|
| 🚪 Gateway + Domain Agents | **Copilot Studio** | No-code, fastest time to agent |
| 🔧 Specialist child agents | **Copilot Studio** (inline or connected) | CS domain agents only · shared container, typed I/O |
| 🔗 Specialist connected agents | **Foundry Agent Service** (A2A) | Complex reasoning, code execution, large RAG — connected tier only |
| 🔗 Non-MSFT connected agents | **Agentforce / ServiceNow** (A2A/REST) | Connected tier only — never child agents |
| ⚡ Workflows | **Power Automate** + **Foundry Flows** | No-code, 1,400+ connectors |
| 🔌 Shared tools | **MCP servers** + **Azure APIM** | One integration per enterprise system |
| 💾 CS agent data | **Dataverse** | Transcripts (auto), metadata, admin config |
| ⚡ Foundry session state | **Redis** | Low latency userId→threadId, hot cache |
| 👤 Foundry cross-session memory | **Memory Feature** (preview) | User profiles, session summaries |
| 🗄️ Foundry full transcript + audit | **Cosmos DB** (compliance only) | CMK / data residency / LLM audit trail |
| 🔍 Large-scale RAG | **Azure AI Search** | Knowledge base > 500 docs |
| 📁 Long-term archive | **Azure Data Lake** (Synapse Link) | Compliance retention > 30 days |
| 🛡️ Governance | **Agent 365 + CoE Toolkit + Entra Agent ID** | No custom audit infra needed |

### ❌ What to Avoid

| ❌ Avoid | Why |
|---|---|
| Single orchestrator for all agents (star topology) | Unusable at 30+ agents, catastrophic at 100 |
| Cosmos DB for CS agents | Dataverse is the correct store; Cosmos DB is Foundry Standard only |
| SQL Server | No role — Dataverse covers admin data; Cosmos DB covers Foundry agent data |
| Redis for CS-only architecture | Not needed when Dataverse covers session state adequately |
| Agent Framework for CS agents | Dev complexity where no-code suffices |
| Free multi-agent peer-to-peer chaining | Least reliable pattern; orchestrator-controlled delegation only |
| Planning for known deterministic workflows | Use CS Topics / Foundry Workflows instead |
| Agent self-verification of its own output | FM-3.3: agents hallucinate success states — always use external verification |
| Building before defining termination conditions | FM-1.5: agents that do not know when to stop are the #1 failure predictor |
| Promoting tools below 90% accuracy | HuggingFace: 33-point accuracy gap; tool quality is the biggest performance lever |

### 🗓️ 100-Agent Delivery Timeline

```
Month 1–2:   Platform foundation
             ├── Dev / Test / Prod environments (per domain)
             ├── ALM pipelines (Power Platform Pipelines)
             ├── 3–5 agent templates
             │     Gateway · Domain Agent · FAQ Specialist
             │     Task Specialist · Integration Specialist
             ├── CoE Toolkit deployment
             ├── Entra Agent ID strategy
             └── Agent 365 Frontier enrollment

Month 3–4:   Pilot wave (10–15 agents)
             ├── Gateway + 2–3 domain agents
             ├── Validate architecture, verification gates,
             │   correlationId pattern, termination logic
             ├── Establish LLM-as-a-Judge evaluation baseline
             └── Validate Agent 365 telemetry

Month 5–12:  Factory mode (8–12 agents/month)
             ├── Domain teams clone templates and configure
             ├── CoE reviews each agent before Test promotion
             ├── ALM pipeline promotes Test → Prod automatically
             └── Agent 365 registry grows with each release
```

---

*Last updated: March 2026*
*Sources: Microsoft Cloud Adoption Framework · Azure Architecture Center · DeepLearning.AI Agentic Design Patterns · HuggingFace / MAST / smolagents / AssetOpsBench / mem-agent · Gartner Multiagent Systems Research · Microsoft Agent Factory Blog Series*

---

## 10. 📋 Agent Design Template

> Complete one card per agent before building. This is the factory input document.

---

### 🪪 1. Identity

| Field | Value |
|---|---|
| **Agent Name** | *e.g., HR Leave Management Agent* |
| **Agent ID** | *Unique ID — used as correlationId prefix* |
| **Version** | *e.g., 1.0.0* |
| **Owner / Sponsor** | *Named individual accountable for this agent* |
| **Domain** | *e.g., Human Resources* |
| **Business Justification** | *What problem does this agent solve? What workflow does it replace?* |

---

### 🧩 2. Agent Classification

| Field | Options | Selected |
|---|---|---|
| **Agent Type** | Gateway · Domain Agent · Specialist Child · Specialist Connected · Inline Topic | |
| **Platform** | 🟦 Copilot Studio · 🟧 Foundry Agent Service | |
| **Agentic Pattern** | 🔵 Flow (deterministic) · 🟠 Crew (autonomous) | |
| **Deployment Tier** | Inline (shared with parent) · Connected (independent) · External (A2A) | |

> ⚠️ Start with Flow. Choose Crew only if the task is genuinely open-ended, dynamic adaptation is required, and errors are recoverable.

---

### 🎯 3. Scope and Responsibilities

```
Single Responsibility Statement:
"This agent is responsible for _________________________________
 and nothing else."

In Scope:
  •
  •

Out of Scope (explicitly):
  •
  •

Orchestrator routing description (used by parent for routing):
"Call this agent when the user wants to ________________________"
(Write this carefully — it IS the routing signal)

Ambiguity handling:
  What inputs require clarification before the agent acts?
  •
```

---

### 📥 4. Input / Output Contract

#### Inputs

| Parameter | Type | Required? | Format / Example |
|---|---|---|---|
| *e.g., userId* | String | ✅ Yes | AAD object ID |
| *e.g., leaveType* | Enum | ✅ Yes | Annual \| Sick \| Unpaid |
| *e.g., startDate* | Date | ✅ Yes | RFC3339: 2026-03-10 |

#### Outputs

| Parameter | Type | Always Returned? | Description |
|---|---|---|---|
| *e.g., confirmationRef* | String | ✅ Yes | Leave request reference |
| *e.g., status* | Enum | ✅ Yes | Approved \| Pending \| Rejected |
| *e.g., errorCode* | String | On failure only | Structured error for parent to handle |

---

### 🔌 5. Tools and Integrations

| Tool Name | Type | System | Operation | Auth | Structured Error Response? |
|---|---|---|---|---|---|
| *e.g., GetLeaveBalance* | Power Automate | HRIS | Read | Managed Identity | ✅ Yes |
| *e.g., SubmitLeaveRequest* | Power Automate | HRIS | Write | Managed Identity | ✅ Yes |

**Tool quality checklist (HuggingFace: >50% of agent failures from malformed tool args):**
- [ ] Explicit argument formats with concrete examples in tool description
- [ ] Datetime fields specify RFC3339 format with timezone offset
- [ ] All error responses are structured and actionable (not silent failures or raw HTTP codes)
- [ ] Tool tested independently before wiring into agent
- [ ] Tool accuracy validated > 90% in test harness before production promotion

**Verification gates (MAST FM-3.3 — never self-verify):**
- For every write operation: define the deterministic read-back step that confirms state change
- Human-in-the-loop PA approval flow required before: ______________________

**Side-channel state (Neuro SAN pattern):**
- Sensitive values that must NOT appear in LLM prompt: ______________________
- Resolved at tool execution time via Key Vault reference: ____________________

---

### 📚 6. Knowledge Sources

| Source | Type | Access | Size | If >500 docs → Azure AI Search |
|---|---|---|---|---|
| *e.g., HR Policy SharePoint site* | SharePoint | Read | ~50 docs | No |

---

### 💾 7. Session and Memory

| Concern | CS Approach | Foundry Approach |
|---|---|---|
| **Within-session state** | Topic/global variables | Thread messages (managed) |
| **Context compaction** | Summarisation PA flow at N turns | Memory feature + compaction step |
| **Cross-session user profile** | Dataverse custom table (PA flow reads on start) | Memory feature (preview) |
| **Inactivity handling** | Inactivity topic at __ minutes → clear variables | Thread retained; new thread on next session |
| **Long-running tasks** | Async: trigger PA → notify via Teams card | Async: Durable Function → push notification |
| **Max session turns** | Design to complete in < 80 turns | No limit but implement compaction at N turns |

---

### 🔁 8. Agentic Behaviour

| Pattern | Enabled? | Configuration |
|---|---|---|
| **Tool Use** | ✅ Always | List tools in section 5; merge tools where possible |
| **Clarification** | ✅ Always | Define ambiguous input conditions; enter clarification topic before any agentic branch |
| **Termination Logic** | ✅ Always | Max steps: __ · Loop detector: same tool + same args = break · Success state: deterministic (not LLM-reasoned) |
| **External Verification** | ✅ For writes | Read-back step after every state-changing tool call |
| **Reflection** | ☐ Optional | Trigger: quality-sensitive outputs (reports, code, plans). Critic prompt: "Review for accuracy, completeness, and policy compliance." |
| **Planning** | ☐ Crew only | Enable only if task is genuinely open-ended |
| **Multi-agent delegation** | ☐ Domain agents only | List connected specialist agents with routing descriptions |

---

### 🛡️ 9. Security and Governance

| Field | Value |
|---|---|
| **Entra Agent Identity** | Agent ID registered in Entra: __________________ |
| **Credential Type** | Managed Identity (preferred) · Federated · OAuth2 |
| **Permitted Tool Scopes** | List APIs/connectors this agent is allowed to call |
| **Prohibited Scopes** | What this agent must NOT have access to |
| **Data Classification** | Public · Internal · Confidential · Restricted |
| **PII Handled?** | Yes / No → If Yes: Foundry Standard + CMK required |
| **Sensitive state in side channel?** | Yes / No → If Yes: Key Vault references, never in prompt |
| **Content Safety** | Input filter: ✅ / Output filter: ✅ |

---

### 🔗 10. Connected Agents (Domain Agents Only)

| Specialist Agent | Platform | Routing Description | Calls Per Session |
|---|---|---|---|
| *e.g., Leave Management Agent* | CS | "Call when user requests leave application or balance" | 1 |
| *e.g., HR Policy Agent* | CS | "Call when user asks about HR policies or entitlements" | 3 |

> ⚠️ Do not exceed 8–10 specialist agents per domain agent. Keep total tools + agents bounded — routing accuracy degrades as scope grows.

---

### 📊 11. Observability and Evaluation

| Metric | Target | Measurement |
|---|---|---|
| **Task completion rate** | > 90% | CS Analytics / Foundry traces |
| **Tool call accuracy** | > 90% | Test harness pre-production (HuggingFace deployment gate) |
| **Routing accuracy** | > 95% | LLM-as-a-Judge on sample sessions |
| **Average turns to completion** | < 5 | CS Analytics |
| **Session escalation rate** | < 10% | CS Analytics |
| **Loop detection events** | 0 in production | Foundry orchestration logs |
| **FM-3.3 (self-verification) events** | 0 | Verification gate audit log |

**Evaluation approach:**
- [ ] Deterministic checks for structured outputs
- [ ] LLM-as-a-Judge for response quality
- [ ] Human annotation sample (monthly calibration)

**correlationId pattern:** `{domainCode}-{agentId}-{sessionTimestamp}`

---

### 🏗️ 12. ALM and Deployment

| Field | Value |
|---|---|
| **Solution Name** | *e.g., HR.LeaveManagement.Agent* |
| **Publisher Prefix** | *e.g., contoso* |
| **Base Template Used** | *e.g., Specialist-Task-Agent-Template-v1* |
| **Dev Environment** | |
| **Test Environment** | |
| **Prod Environment** | |
| **ALM Pipeline** | Power Platform Pipelines · GitHub Actions · Azure DevOps |
| **Environment Variables** | All config values externalised (endpoints, IDs, feature flags) |
| **Dependencies** | Parent agents, shared PA flows, MCP servers, Dataverse tables |

---

### ✅ 13. Definition of Done

- [ ] Single responsibility statement is clear and agreed with business owner
- [ ] All ambiguous input conditions have a clarification topic
- [ ] Input/output contract defined and tested
- [ ] All tool calls tested independently at > 90% accuracy
- [ ] Structured error responses on every tool connector
- [ ] External verification gate built for every write operation
- [ ] Termination conditions defined in code (max steps, loop detector)
- [ ] Sensitive state moved to side channel (Key Vault) — not in prompt
- [ ] Inactivity/compaction topic built (CS agents)
- [ ] Entra Agent Identity created, scoped with least privilege, sponsor assigned
- [ ] correlationId propagation tested end-to-end
- [ ] Deployed through ALM pipeline (never manual publish)
- [ ] Registered in Agent 365 / CoE Toolkit inventory

### 📚 References
- Copilot Studio — ALM Strategy: https://learn.microsoft.com/en-us/microsoft-copilot-studio/guidance/alm
- Copilot Studio — Agent Templates: https://learn.microsoft.com/en-us/microsoft-copilot-studio/faqs-templates
- Microsoft Entra Agent ID: https://learn.microsoft.com/en-us/entra/agent-id/identity-professional/microsoft-entra-agent-identities-for-ai-agents
- DeepLearning.AI — Evaluating AI Agents: https://www.deeplearning.ai/short-courses/evaluating-ai-agents/
- HuggingFace — Agent Leaderboard: https://huggingface.co/blog/pratikbhavsar/agent-leaderboard
- Power Platform — Component Collections: https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-export-import-copilot-components

---

## 11. ⚠️ Design Considerations

> Open items identified during architecture review. To be resolved before production scale-out.

---

### DC-1 · Gateway Routing Capacity at Scale

**Gap:** The CS instruction limit is 8,000 characters. At 8–12 domain agents, each requiring a precise routing description plus system safety instructions, the gateway prompt approaches this ceiling. The star topology anti-pattern section correctly warns that routing accuracy collapses as scope grows — but the gateway itself faces this exact problem.

**Options to evaluate:**
- Route by intent taxonomy using a structured CS topic with a bounded decision tree — not open-ended LLM prompt matching
- Externalise routing descriptions to a Dataverse lookup table; inject only the matched description at runtime
- Measure token consumption of the gateway prompt at 6, 9, and 12 domain agents before committing to a pattern

---

### DC-2 · Dependent Cross-Domain Workflows

**Gap:** The architecture handles independent multi-domain calls correctly (leave + Salesforce: gateway calls HR domain, calls Agentforce, composes — no issue). The gap is specifically *dependent* cross-domain workflows: where steps have ordering constraints, shared state, compensation requirements, or duration exceeding the CS session limit (60 min / 100 turns).

**Example:** Employee offboarding — IT access revocation must complete before HR records close; Finance final payroll depends on HR confirmation; if Finance fails, HR records must be reopened across an already-closed CS session.

**Options to evaluate:**
- Classify intents at the gateway as "independent multi-domain" (current pattern) vs. "dependent multi-domain" (needs orchestration)
- Power Automate workflow as orchestrator for dependent processes — no session limits, native compensation branches, PA approval flows for human gates
- Foundry Workflow (state machine) for compliance-grade processes requiring CMK or full audit trail
- Define a Workflow Registry in Dataverse (parallel to Agent Registry): named workflows with ordered steps, compensating actions, timeout policies, and correlationId prefix per workflow

---

### DC-3 · Indirect Prompt Injection

**Gap:** Content safety is applied at user input and agent output, but not specifically at tool *response* ingestion. At 100 agents making tool calls against enterprise systems (SharePoint, HRIS, CRM, Jira), a malicious document or record can embed adversarial instructions that hijack the agent's next action — without triggering output-layer content safety.

**Options to evaluate:**
- Add a response sanitisation step between tool response and LLM context re-entry — distinct from output content safety
- Add to the Agent Design Template tool checklist: "Is the tool response from a user-writable system? If yes, sanitisation required."
- Evaluate Azure Content Safety prompt shield for indirect injection detection on tool responses

---

### DC-4 · Preview Dependency on Foundry Memory Feature

**Gap:** Cross-session user profile storage for Foundry agents is built on Memory Feature (preview). A 100-agent factory deployment targeting 6–12 months ships before GA is confirmed. API or behaviour changes in preview would require rebuilding the user profile layer across all Foundry agents.

**Options to evaluate:**
- Build with Redis + Cosmos DB as the durable profile store now; add Memory Feature as a retrieval layer when GA
- Gate Foundry agents on Memory Feature only after it reaches GA or a committed preview SLA is confirmed
- Define a profile store abstraction so the underlying store can be swapped without rewriting agent logic

---

### DC-5 · Model Selection Locked in Copilot Studio

**Gap:** Architectural Principle 16 recommends right-sizing the model per agent role (smaller models for classification, extraction, formatting). CS — recommended for 80–90% of agents — locks to Microsoft-managed models with no selection control. The principle is correct; the platform does not support it for the majority of agents.

**Options to evaluate:**
- Explicitly document this as a CS tradeoff in the platform selection section
- Route classification and extraction agents that would benefit from smaller models to Foundry instead of CS
- Monitor whether CS adds model selection capability; treat Principle 16 as Foundry-only for now

---

### DC-6 · LLM-as-Judge Calibration

**Gap:** LLM-as-Judge is recommended as the evaluation gate for routing accuracy and response quality. Known biases (positional, verbosity, self-enhancement) mean the judge can produce systematically wrong quality signals — giving false confidence before production promotion — unless calibrated against human annotations first.

**Options to evaluate:**
- Establish human annotation agreement threshold (e.g., >80% agreement) as a prerequisite before LLM-as-Judge is used as an automated gate
- Calibrate the judge during the Month 3–4 pilot wave, not after Month 5 scale-out begins
- Use ensemble judging (multiple model judges + human spot-check) for high-stakes routing decisions

---

### DC-7 · No Circuit Breaker or Tool Degradation Pattern

**Gap:** A 90% tool accuracy threshold exists as a deployment gate but there is no defined behaviour for tools that degrade below threshold after they are in production. A failing tool will continue to be called, accumulating errors and potentially corrupting downstream state across multiple agents.

**Options to evaluate:**
- Define a degradation mode per agent: fallback to read-only / FAQ-only if agentic tools fail above a configurable error rate threshold
- Add error rate monitoring in App Insights per tool per agent with an alert threshold and automatic tool disablement trigger
- Leverage Principle 20 (kill switch) explicitly: document the per-tool disablement procedure as part of every Agent Design Template entry

---

### DC-8 · Error Propagation Through the Agent Hierarchy

**Gap:** In a 4-hop chain (Gateway → Domain Agent → Specialist Agent → Tool), there is no defined contract for how a tool failure propagates back to the user with enough context to be meaningful. Intermediate agents typically either swallow the error (wrong) or re-interpret it (introduces hallucination risk).

**Options to evaluate:**
- Define a typed error envelope passed upstream untouched: `{ errorCode, errorMessage, retryable, agentId, stepId, correlationId }`
- Only the gateway translates the error envelope into user-facing language — intermediate agents forward, never interpret
- Add error envelope to the Agent Design Template I/O contract section as a mandatory output field

---

### DC-9 · correlationId Enforcement

**Gap:** The correlationId pattern (`{domainCode}-{agentId}-{sessionTimestamp}`) is a naming convention, not an enforced contract. In heterogeneous systems (CS + Foundry + Agentforce), propagation breaks silently — there is no validation step that rejects responses missing the expected correlationId.

**Options to evaluate:**
- Add gateway-level validation: any agent response without a matching correlationId is treated as an error, not passed to the user
- Define correlationId as a required field in the A2A payload schema, not an optional header
- Add correlationId propagation test to the Agent Design Template Definition of Done checklist (already partially there — make it a gate, not a checkbox)

---

*Last reviewed: March 2026 · Owner: Vineet Kaul*
