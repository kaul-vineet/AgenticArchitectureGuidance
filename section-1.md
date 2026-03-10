---
layout: default
title: "🏗️ 1. Representative Architecture"
parent: Enterprise Multi-Agent Architecture
nav_order: 3
---

## 1. 🏗️ Representative Architecture

### Platform Stack

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  👤 END USERS
  Teams · M365 Copilot · SharePoint · Web · Mobile
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  🚪 GATEWAY / MASTER AGENT
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
- [Microsoft CAF — AI Agents](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/)
- [Azure Architecture Center — AI Agent Orchestration Patterns](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns)
- [DeepLearning.AI — Agentic Design Patterns](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/)
- [MAST — Why Enterprise Agents Fail](https://huggingface.co/blog/ibm-research/itbenchandmast)
- [smolagents — Building Good Agents](https://huggingface.co/docs/smolagents/tutorials/building_good_agents)
- [Gartner — Multiagent Systems](https://www.gartner.com/en/articles/multiagent-systems)
- [Microsoft Agent Factory Blog](https://azure.microsoft.com/en-us/blog/agent-factory-the-new-era-of-agentic-ai-common-use-cases-and-design-patterns/)
{: .ref-grid}

### 🏆 Recommendation
> ⚡🧩📈🏭🔵 **Use Copilot Studio as gateway and domain agents with Dataverse as the operational data layer. Add Foundry only for specialist agents. This requires no custom infrastructure, deploys through Power Platform Pipelines, and lets domain teams own their agents using no-code tooling.**
{: .recommendation}

---

### 🔗 Alignment to Key Architectural Principles

| # | Principle | Source | Applied To This Architecture |
|---|---|---|---|
| 1️⃣ | **Minimise LLM calls** | [HuggingFace / smolagents](https://huggingface.co/blog/smolagents) | **CS:** Gateway and domain routing are CS Topics — zero LLM calls. PA flows handle all tool calls (leave balance, CRM update, HRIS write) without LLM involvement.<br>**Foundry:** Workflow nodes are deterministic by default; planning enabled only for genuinely open-ended tasks. Target: <20% of interactions involve generative processing. |
| 2️⃣ | **Default to Flows, escalate to Crews** | [DeepLearning.AI](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/) | **CS:** CS Topic flows are the default for every new domain; no generative topics unless deterministic routing fails.<br>**Foundry:** Sequential and HITL Workflows for 80% of tasks; Magentic / Group Chat only for genuinely open-ended scenarios. No Foundry build is approved if CS can reliably deliver the outcome. |
| 3️⃣ | **Clarification before action** | [MAST (FM-2.2)](https://huggingface.co/blog/ibm-research/itbenchandmast) | **CS:** Gateway has a mandatory disambiguation Topic before any routing decision; domain agents apply the same check before delegating to specialists.<br>**Foundry:** A clarification Workflow node is built into every branch where input intent is ambiguous. Ambiguity causes a 50pp accuracy collapse (OpenEnv/Turing) — this gate is non-negotiable on both platforms. |
| 4️⃣ | **External verification — never self-check** | [MAST (FM-3.3)](https://huggingface.co/blog/ibm-research/itbenchandmast) | **CS:** After every PA flow write, a separate PA flow reads back confirmation from the system of record — never the same call.<br>**Foundry:** A dedicated verification tool call follows every write in the Workflow; structured JSON enables deterministic state checks. Every write tool in the Agent Design Template must have a paired read-back verification tool. |
| 5️⃣ | **Externalize termination logic** | [MAST (FM-1.5, FM-3.1)](https://huggingface.co/blog/ibm-research/itbenchandmast) | **CS:** Session limits (100 turns, 60 min) are platform-enforced; Topic conditions declare explicit completion signals.<br>**Foundry:** `max_steps`, loop detection, and escalation thresholds are in Workflow YAML and SDK code — never in the system prompt. Magentic includes built-in stall detection. Termination conditions are declared in the Agent Design Template before build on both platforms. |
| 6️⃣ | **Tool argument quality is the #1 failure cause** | [HuggingFace / OpenEnv](https://huggingface.co/blog/openenv-turing) | **CS:** Every PA connector must include: (1) an example call, (2) RFC3339 datetime formats, (3) typed enums, (4) a structured error response with field name and expected format.<br>**Foundry:** Same four requirements apply to every Azure Function and OpenAPI tool definition. These are mandatory build checklist items — no connector is promoted without them. |
| 7️⃣ | **Consolidate tools** | [HuggingFace / smolagents](https://huggingface.co/blog/smolagents) | **CS:** PA flows that always execute in sequence (e.g. check balance + submit leave) are merged into one flow.<br>**Foundry:** Azure Functions consolidate multi-step operations where no intermediate decision is needed. Rule: if two calls always happen sequentially with no branch between them, they must be merged. Exception: if they require individual retry or independent verification. |
| 8️⃣ | **Multi-agent is a reliability tax** | [HuggingFace / AssetOpsBench](https://huggingface.co/blog/ibm-research/assetopsbench-playground-on-hugging-face) | **CS:** New boundaries require one of: different data sovereignty, separate compliance scope, or connectors that cannot be shared.<br>**Foundry:** Each Workflow sub-agent hop adds LLM inference cost; sub-agents added only where parallel execution or specialist isolation is demonstrably required. Organisational reasons are not valid justification on either platform. |
| 9️⃣ | **Context hygiene at scale** | [HuggingFace / Forge RL](https://huggingface.co/blog/dcarpintero/design-patterns-for-building-agentic-workflows) | **CS:** Gateway passes only intent, userId, and correlationId — no full chat history. Inline children share parent context; connected agents receive only the typed fields they need.<br>**Foundry:** Per-thread context per sub-agent; compaction every 10 tool turns; explicit state passed at each Workflow boundary. Context scope is defined per boundary in the Agent Design Template on both platforms. |
| 🔟 | **Sensitive state in side channel** | [HuggingFace / Neuro SAN](https://huggingface.co/blog/danyoung/neuro-san-is-all-you-need) | **CS:** Credentials live in Power Platform connection references — never in Topics or system prompts. Employee IDs, balances, and salary figures are fetched at tool execution time and never stored in the conversation context.<br>**Foundry:** Managed Identity for all service calls; secrets in Azure Key Vault — never in Workflow YAML or agent instructions. |
| 1️⃣1️⃣ | **Tool accuracy > 90% as deployment gate** | [HuggingFace / AssetOpsBench](https://huggingface.co/blog/ibm-research/assetopsbench-playground-on-hugging-face) | **CS:** CoE Toolkit tracks per-connector success rate; 90% baseline is recalibrated per workflow length — a 3-hop flow targeting 98% end-to-end requires 99.3%+ per step.<br>**Foundry:** App Insights tracks the same metric per Workflow node. Required per-step accuracy is computed in the Agent Design Template for each domain before build begins on both platforms. |
| 1️⃣2️⃣ | **Human-in-the-loop for irreversible actions** | [DeepLearning.AI](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/) · [Microsoft CAF](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/governance-security-across-organization) | Every action is pre-classified as: (1) **Advisory** — agent recommends, human decides (financial approvals, HR exceptions). (2) **Supervised** — agent executes, 15-min undo window (CRM updates, standard leave). (3) **Autonomous** — no review (read-only, notifications). **CS:** PA approval flows with Adaptive Cards show action, reasoning, source data, and undo path — reviewable in <30 sec.<br>**Foundry:** HITL Workflow node pauses before irreversible writes. |
| 1️⃣3️⃣ | **Use lowest complexity that reliably works** | [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) | Decision ladder enforced at intake: (1) PA flow without LLM → (2) single CS Topic → (3) specialist CS agent → (4) Foundry. Each rung must be documented as attempted and failed before the next is approved. **CS is the default; Foundry is the exception.** No Foundry build is approved without documented evidence that CS cannot meet the requirement. |
| 1️⃣4️⃣ | **Justify every agent boundary explicitly** | [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) | Three valid reasons for a new boundary: (a) security / compliance isolation, (b) prompt complexity > 8,000 chars, (c) tool overload > ~15 tools. **CS:** Applies to domain and specialist splits.<br>**Foundry:** Applies to each Workflow sub-agent node. Organisational preference is not a valid reason. Default is no new boundary — the burden of proof is on the team requesting one. |
| 1️⃣5️⃣ | **Single responsibility per agent** | [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) · [Microsoft CAF](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/governance-security-across-organization) | Each agent has a written job description and an explicit "does NOT handle" list in the Agent Design Template. **CS:** CoE Toolkit monthly review flags intent overlap between agents.<br>**Foundry:** App Insights flags redundant invocations across sub-agents. Overlapping agents are consolidated — not left running in parallel on either platform. |
| 1️⃣6️⃣ | **Right-size the model per agent role** | [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) | **CS:** Model selection locked to Microsoft-managed — no per-agent tuning (known limitation; accept the trade-off).<br>**Foundry:** Gateway uses a mid-tier model (classification); domain agents use a capable reasoning model; specialist agents use the smallest model that passes the capability test for the specific subtask. Model selection and capability test result are recorded in the Agent Design Template. |
| 1️⃣7️⃣ | **Validate output before passing downstream** | [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) | **CS:** Connected agents return typed YAML — receiving agent checks required fields before acting; inline agents use Topic condition checks.<br>**Foundry:** JSON schema validation at each Workflow node; malformed output halts the Workflow and triggers retry or escalation. Validation is enforced in code on both platforms — never delegated to LLM judgment. |
| 1️⃣8️⃣ | **Surface errors explicitly — never swallow failures** | [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) | **CS:** Every PA connector returns `{errorCode, field, expectedFormat, retryable}`. Topics have named error branches per failure type — no generic fallback.<br>**Foundry:** SDK catches specific exception types; errors propagate upward through the Workflow. Users see either a confirmed success with reference number or a named error with next steps — never a silent failure. |
| 1️⃣9️⃣ | **Idempotency for all writes** | [Microsoft Tech Community](https://techcommunity.microsoft.com/blog/educatordeveloperblog/ai-didn%E2%80%99t-break-your-production-%E2%80%94-your-architecture-did/4482848) | **CS:** PA flows include idempotency key `{correlationId}-{actionType}-{date}`; flow checks Dataverse for existing key before executing.<br>**Foundry:** Azure Functions and OpenAPI tools include the same key pattern. Prevents duplicate leave submissions, CRM updates, and financial records on retry. Idempotency key is a required field in the Agent Design Template for every write action on both platforms. |
| 2️⃣0️⃣ | **Kill switch — disable tools without redeployment** | [Microsoft CAF](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/governance-security-across-organization) | Three levels: (1) **Agent-level** — disable CS agent or Foundry project via portal, immediate effect. (2) **Tool-level** — disable a specific PA connector or Azure Function; agent degrades to read-only. (3) **Fleet-level** — CoE Toolkit broadcasts maintenance message to all CS agents simultaneously. Kill switch runbook for each domain is written before go-live — not after an incident. |
| 2️⃣1️⃣ | **Security trimming in every agent, not just the edge** | [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) | **CS:** Agents query Dataverse using the authenticated user's Entra identity — RLS trims results at the data layer, not by the LLM.<br>**Foundry:** Managed Identity scoped to minimum required permissions; search index security-trims results before retrieval. No agent response on either platform contains data not returned by the underlying system for that user's entitlement. |
| 2️⃣2️⃣ | **Content safety at every layer** | [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) | **CS:** AI Builder content safety on user input before the Topic fires; PA flow responses pass through a content check before inclusion in the agent response.<br>**Foundry:** Azure AI Content Safety applied at Workflow input and output nodes. Safety filter level is configurable per domain — stricter for HR and Finance — on both platforms. |
| 2️⃣3️⃣ | **Scope agent authority to operations, not tools** | [Microsoft Tech Community](https://techcommunity.microsoft.com/blog/educatordeveloperblog/ai-didn%E2%80%99t-break-your-production-%E2%80%94-your-architecture-did/4482848) | **CS:** PA connectors scoped to minimum operations (HRIS: submit + read balance only; Salesforce: update designated fields only). Rate cap: 10 write operations per session per specialist agent.<br>**Foundry:** RBAC per Workflow node; blast radius caps in SDK code. Scopes are enforced at the infrastructure layer — not declared in the system prompt on either platform. |
| 2️⃣4️⃣ | **Instrument all handoffs, not just final output** | [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) · [Microsoft Azure Blog](https://azure.microsoft.com/en-us/blog/agent-factory-top-5-agent-observability-best-practices-for-reliable-ai/) | **CS:** CorrelationId generated at Gateway and passed to every downstream agent call, PA flow, and tool invocation. Dataverse logs each hop.<br>**Foundry:** Full OpenTelemetry via App Insights; every hop tagged with the same correlationId. A single correlationId must reconstruct the complete end-to-end trace across all agents and platforms — non-negotiable acceptance criterion for every domain. |
| 2️⃣5️⃣ | **Automated evaluation in CI/CD** | [Microsoft Azure Blog](https://azure.microsoft.com/en-us/blog/agent-factory-top-5-agent-observability-best-practices-for-reliable-ai/) | **CS:** PP Pipelines trigger the 50-sample eval harness per agent, connector health checks, and routing accuracy regression on every solution import.<br>**Foundry:** Azure DevOps / GitHub Actions run the same eval harness plus regression against the last 30 days of production traces on every Workflow YAML or prompt change. A prompt change has the same gate as a code change on both platforms. LLM-as-Judge is the scoring method; exact-match is not used. |
| 2️⃣6️⃣ | **Evaluate continuously in production** | [Microsoft Azure Blog](https://azure.microsoft.com/en-us/blog/agent-factory-top-5-agent-observability-best-practices-for-reliable-ai/) | **CS:** CoE Toolkit tracks weekly: intent resolution rate, connector success rate, session abandonment, and escalation rate. A >5% week-over-week drop triggers a review ticket.<br>**Foundry:** App Insights tracks the same metrics per Workflow hop. Monthly: full eval harness re-run on both platforms to catch silent prompt drift from model updates. |
| 2️⃣7️⃣ | **Maintain agent inventory — shadow agents are a risk** | [Microsoft CAF](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/governance-security-across-organization) | **CS:** Agent 365 is the system of record — every agent has a named owner, domain, production date, last-reviewed date, and decommission plan. No deployment without prior registration.<br>**Foundry:** No native registry; custom Azure Policy + Entra tracking required. Monthly CoE review flags agents unused for 30 days or unreviewed for 90 days. Shadow agents found by environment scan are treated as security incidents on both platforms. |
| 2️⃣8️⃣ | **Externalize and version-control prompts** | [Azure Well-Architected Framework](https://learn.microsoft.com/en-us/azure/well-architected/ai/application-design) | **CS:** Agent instructions stored in solution environment variables — not hardcoded in Topics — so prompt updates promote through PP Pipelines as configuration changes without Topic rebuilds.<br>**Foundry:** System prompts in Azure App Configuration or Key Vault — not in Workflow YAML. Every prompt change requires a PR with reviewer sign-off and must pass the eval harness before merge on both platforms. A prompt change is a code deployment. |

---

### 🎯 Agent Orchestration

**✅ Recommended: Gateway → Domain Agent Hierarchy**

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

**📐 Why This Architecture Works — Research Evidence**

The Gateway → Domain hierarchy is the **Orchestrator-Workers pattern**, documented and validated across multiple independent research sources:

> *"In this workflow, a central LLM dynamically decomposes tasks, delegates them to worker agents, and synthesizes their outputs into coherent results."*
> — HuggingFace, Design Patterns for Agentic Workflows

> *"By decomposing work, developers can tailor prompts for specific objectives... Many teams are getting good results with this method, and ablation studies show that multiple agents give superior performance to a single agent."*
> — Andrew Ng, DeepLearning.AI

> *"When you use multiple AI agents, you can break down complex problems into specialized units of work or knowledge. Individual agents can focus on a specific domain or capability, which reduces code and prompt complexity."*
> — Microsoft Azure Architecture Center

> *"Instead of expecting one AI to be an all-knowing expert (often unrealistic — akin to getting a high-school intern when you hoped for a PhD), multi-agent orchestrators break problems into smaller pieces and assign them to specialized agents."*
> — HuggingFace, Neuro SAN

**Real-world outcomes from hierarchical multi-agent architectures** (Microsoft Agent Factory, 2025):

| Organisation | Architecture | Outcome |
|---|---|---|
| **Fujitsu** | Specialized agents for data analysis, market research, and document creation, each invoking specific APIs | **67% reduction** in proposal production time |
| **ContraForce** | Planning + specialist agents for intake, impact assessment, playbook execution, and escalation | **80% of incident investigation automated**; processing cost under **$1 per incident** |
| **JM Family (BAQA Genie)** | Coordinated specialist agents for requirements, story writing, coding, documentation, and QA | Weeks reduced to days; **up to 60% QA time savings** |

*Source: [Agent Factory — Use Cases & Design Patterns](https://azure.microsoft.com/en-us/blog/agent-factory-the-new-era-of-agentic-ai-common-use-cases-and-design-patterns/)*

**Four properties that make hierarchical orchestration reliable** (Microsoft Azure Architecture Center):

| Property | What It Means in Practice |
|---|---|
| **Specialization** | Each domain agent has a focused prompt and bounded tool set — no competing priorities, no routing ambiguity |
| **Scalability** | Agents can be added or modified without redesigning the entire system — new domain = one new domain agent |
| **Maintainability** | Testing and debugging focused on individual agents — failures isolated, not system-wide |
| **Optimization** | Each domain agent can use distinct models, tools, and compute appropriate to its workload |

**Alignment to Key performance evidence:**

| Design Requirement | Finding | Data | Source | What it means | Alignment to Design Requirement |
|---|---|---|---|---|---|
| 🎯 Simplest effective approach wins · 📈 Scale over custom dev · 🔵 Reduced complexity | **Agent loop vs. zero-shot single pass** | GPT-3.5 with agent loop: **95.1%** vs GPT-4 zero-shot: **67%** on HumanEval | DeepLearning.AI | A weaker, cheaper model using an agentic loop (plan → act → observe error → retry) beat GPT-4 in a single pass by 28pp. The loop gave it the ability to observe failure and correct — something no single-pass model can do regardless of capability. | · **Simplest effective approach** — architecture pattern (reflection loop) outperforms brute-force model upgrade<br>· **Scale** — right-sized models per domain reduce cost as agent count grows<br>· **Reduced complexity** — no need to upgrade every agent to the most capable model; simpler looping agents scale more predictably |
| 🔵 Reduced complexity · 🏢 Rapid LOB integration · 🏭 100+ agents in 6–12 months | **Tool accuracy is the #1 differentiator** | Top agents: **94%** tool accuracy vs low performers: **61%** — 33pp gap | AssetOpsBench (IBM) | IBM tested agents on industrial asset operations tasks. The 33pp accuracy gap between top and bottom agents was larger than any difference in model capability, prompt quality, or memory design. Root causes: poorly defined tool descriptions, missing argument examples, ambiguous parameter names, no structured error responses. | · **Rapid LOB integration** — well-defined connectors with explicit argument formats are the single biggest quality lever for LOB system calls<br>· **Reduced complexity** — fewer, well-defined tools per agent outperform large tool libraries<br>· **100+ agents in 6–12 months** — tool quality must be a gate before any agent is promoted; a 33pp gap means a failed pilot, not a scaling problem |
| 🔀 Orchestration across 100+ agents · 🏭 100+ agents in 6–12 months · 🧩 No-code over custom | **Ambiguity causes accuracy collapse** | Explicit intent: **90%** success → ambiguous intent: **40%** success (−50pp) | OpenEnv/Turing (HuggingFace) | The same agents were tested on identical tasks — one set worded explicitly, one set worded vaguely. A single variable (clarity of user intent) caused a 50pp collapse. When intent is unclear the agent guesses, proceeds confidently, and produces a wrong result with no error signal to the user. | · **Orchestration across 100+ agents** — at scale, a 50pp accuracy collapse from ambiguity propagates across every domain; clarification topics must be a platform-wide standard<br>· **No-code over custom** — CS Topics provide a native, no-code mechanism to build disambiguation steps; no custom code needed<br>· **100+ agents in 6–12 months** — clarification must be built into agent templates from day one, not retrofitted after failed pilots |
| 🔀 Orchestration across 100+ agents · 📈 Scale over custom dev · 📋 Logging & observability | **Context history loss at scale** | Overloaded agents: **24%** of traces lose conversation history mid-session vs **0%** for focused agents | MAST/ITBench — FM-1.4 (IBM) | When too many domains and tools are forced into one context, the model's attention deprioritises older turns. The user says "cancel the request I just made" and the agent has already lost track of what that request was. This is not a model limitation — it is a context window management problem caused by overloading. | · **Orchestration across 100+ agents** — context loss at 24% of traces is a structural consequence of star-topology overloading; domain hierarchy eliminates it by bounding each agent's context<br>· **Scale** — focused agents maintain 0% context loss regardless of total agent count<br>· **Logging & observability** — context loss is undetectable without per-session tracing; distributed tracing across agent boundaries is required to surface this failure mode |
| 🔀 Orchestration across 100+ agents · 🛡️ Enterprise governance · 🔵 Reduced complexity | **Reasoning disconnect in overloaded agents** | **94%** of failed traces show reasoning-action decoupling in overloaded systems | MAST/ITBench — FM-3.3 (IBM) | The agent writes a correct reasoning chain ("check leave balance → check policy → respond") and then executes different actions that don't follow it. Reasoning and action have decoupled because too much intervening context separates the plan from the execution step. | · **Enterprise governance** — an agent that reasons correctly but acts incorrectly produces confident, auditable-looking wrong outputs; governance controls must enforce action constraints in code, not rely on LLM reasoning<br>· **Orchestration across 100+ agents** — reasoning-action decoupling is a direct consequence of overloaded orchestrators; bounded domain agents with narrow context eliminate the root cause<br>· **Reduced complexity** — the fix is a simpler, narrower agent — not a larger model or more complex prompt |
| 📋 Logging & observability · 🛡️ Enterprise governance · 🔀 Orchestration across 100+ agents | **Failure mode density** | Clean agents: **2.6** failure modes/trace · Collapsed systems: **5.3** failure modes/trace | MAST/ITBench (IBM) | In overloaded systems, failures cascade: context loss triggers wrong routing → wrong tool arguments → unhandled error → hallucinated response. By the time a user reports the failure, five things are wrong with no clear root cause. In focused agents, failures are bounded and traceable. | · **Logging & observability** — 5.3 cascading failure modes per trace are only diagnosable with end-to-end tracing across every agent hop and tool call; transcript-only logging is insufficient<br>· **Enterprise governance** — a bounded, domain-isolated agent limits blast radius to one domain, making incident ownership and accountability clear<br>· **Orchestration across 100+ agents** — the domain hierarchy pattern is the direct architectural response: each domain agent's failures are isolated, identifiable, and fixable without touching other domains |
| 🥇 P1 Supervisor pattern · 🥈 P2 Network/mesh · 🛡️ Enterprise governance | **Hallucination rate: orchestrated vs. peer-to-peer** | Sonnet 4 in orchestrated configurations: **0% hallucination** · Sonnet 4 in multi-agent ReAct (peer-to-peer): **36% hallucination** | AgentArch benchmark — ServiceNow (arXiv 2509.10769) | The same model (Sonnet 4) was tested in orchestrated (supervisor-led) and unstructured peer-to-peer ReAct configurations. In every orchestrated architecture, hallucination rate was 0%. In unstructured multi-agent ReAct — where agents call each other laterally without a supervisor — hallucination spiked to 36%. This is not a model quality difference; it is a structural architecture difference. | · **P1 Supervisor pattern** — the 0% vs 36% gap is the clearest empirical case for supervisor-led orchestration over peer-to-peer network patterns<br>· **P2 Network/mesh** — this finding is a direct warning against unstructured peer-to-peer agent patterns; lateral collaboration must always be orchestrator-controlled<br>· **Enterprise governance** — a 36% hallucination rate in unstructured patterns makes audit and compliance impossible at enterprise scale |
| 🔀 Orchestration across 100+ agents · 📈 Scale over custom dev · 🥇 P1 Hierarchical pattern | **Multi-agent vs. single-agent decision accuracy** | Multi-agent with GPT-4.1: **97–99% correct final decisions** · Single-agent GPT-4.1: **79–86%** — 18pp gap | AgentArch benchmark — ServiceNow (arXiv 2509.10769) | AgentArch tested 18 agentic architectures across 6 LLMs on realistic enterprise workflows. Multi-agent architectures with function calling delivered 18pp higher correct final decision rates than single-agent on the same model. Specialization — not model power — drove the gap. | · **Orchestration across 100+ agents** — at enterprise scale, 18pp higher decision accuracy across 100 agents compounds into thousands of correct decisions per day that would be wrong in a flat architecture<br>· **Scale** — the performance advantage of hierarchy holds regardless of agent count; the pattern scales with the estate<br>· **P1 Hierarchical pattern** — this benchmark directly validates the Gateway → Domain → Specialist hierarchy as the highest-accuracy enterprise architecture |
| 🎯 Simplest effective approach · 🔀 Orchestration at scale · 📋 Logging & observability | **Context degradation beyond 12 tool turns** | Beyond **12 tool-use turns**: agents invoke redundant operations, re-read unchanged files, repeat failed calls; **50% longer context → 3–5% efficiency loss** with compounding effects | LoCoBench / ReliabilityBench (arXiv 2511.13998 / 2601.06112) | Once an agent accumulates more than ~12 tool calls in a single context, redundant behavior emerges nonlinearly — re-reading files, repeating failed calls, generating verbose summaries that consume tokens without adding value. The degradation is not linear; context accumulation, compression overhead, and repetitive operations interact destructively. | · **Simplest effective approach** — keep each domain agent bounded to its own short, focused context; routing to a domain agent resets the context clock to zero<br>· **Orchestration at scale** — at 100+ agents, context degradation in a flat architecture accumulates across every domain simultaneously; hierarchy contains it per domain<br>· **Logging & observability** — redundant tool calls are invisible without per-step tracing; end-to-end observability is required to detect context degradation in production |
| 🥇 P1 Supervisor pattern · 🏭 100+ agents in 6–12 months · 📈 Scale | **AutoGen hierarchical multi-agent vs. baseline** | AutoGen hierarchical with GPT-4: **69.48% accuracy on MATH level-5** · Baseline ChatGPT: significantly lower · OptiGuide hierarchical pattern: **3–5× reduction in manual interactions**, codebase reduced to ~100 lines | AutoGen — Microsoft Research (arXiv 2308.08155) | Microsoft Research's AutoGen framework established hierarchical chat and dynamic group chat (a specialised form of hierarchical chat) as the recommended patterns for complex task decomposition. The OptiGuide supply chain application — a real enterprise deployment — used a Commander → Writer → Safeguard hierarchy and reduced codebase size by an order of magnitude vs. traditional approaches. | · **P1 Supervisor pattern** — the Commander/Writer/Safeguard hierarchy in OptiGuide is a direct implementation of the supervisor pattern; 3–5× manual interaction reduction proves production value<br>· **100+ agents in 6–12 months** — hierarchical patterns reduce codebase to ~100 lines; simpler, template-based agents are the direct enabler of factory-scale delivery<br>· **Scale** — AutoGen's "hierarchical chat" is Microsoft's own recommended pattern; the Gateway → Domain architecture is the enterprise implementation of this principle |

---

**❌ The Star Topology Anti-Pattern (One Master Agent for All Domains)**

```
                    ANTI-PATTERN
    HR Agent ─────────┐
    Finance Agent ────┤
    IT Agent ─────────┤──► ⚠️ SINGLE SHARED ORCHESTRATOR
    Sales Agent ──────┤         (handles all 100 agents)
    ... 95 more ───────┘
```

| ⚠️ Disadvantage | What Happens in Practice | Research Backing |
|---|---|---|
| 🔴 **Tool overload collapses routing quality** | A single orchestrator for 100 agents accumulates hundreds of tools. LLM routing accuracy drops sharply as tool count grows — the orchestrator cannot reliably select the right agent. | *"As the number of knowledge sources and tools increases, it becomes difficult to provide a predictable agent experience."* — Microsoft Azure Architecture Center |
| 🔴 **Context window floods with unrelated history** | All domain interactions accumulate in one shared thread. Finance queries appear in HR context. Attention dilutes across irrelevant history. Directly causes FM-1.4 (Loss of Conversation History — 24% of traces in overloaded systems). | *"Context windows can grow rapidly because each agent adds its own reasoning, tool results, and intermediate outputs."* — Microsoft Azure Architecture Center · MAST FM-1.4 |
| 🔴 **Routing classification fails under scope growth** | A single orchestrator must classify every intent across all 100 domains from one prompt. Misclassification sends the request to the wrong domain. | *"If a task is misclassified, it might be sent to the wrong agent, leading to inefficiency or incorrect results."* — HuggingFace, Design Patterns for Agentic Workflows |
| 🔴 **One rate limit ceiling for all domains** | All 100 agents share one model endpoint's TPM quota. A Finance spike throttles HR users simultaneously. | *"Evaluate how using a single MaaS endpoint can result in rate limiting when agents run concurrently."* — Microsoft Azure Architecture Center |
| 🔴 **Security trimming cannot be enforced** | HR and Finance queries share context and identity. Per-domain data access cannot be scoped. Over-privileged identities are the highest-impact incident category. | *"Security trimming must be implemented in every agent, not only at the gateway."* — Microsoft Azure Architecture Center · Microsoft Tech Community |
| 🔴 **Chain composition creates unreviewed capabilities** | Tools approved individually compose into capabilities nobody reviewed. | *"Read data, create ticket, send message — all approved individually. Put together, it becomes a capability nobody reviewed."* — Microsoft Tech Community |
| 🔴 **One failure = full outage for all domains** | A prompt change, model degradation, or bug takes all 100 agents offline simultaneously. Failure modes cascade — overloaded systems accumulate 5.3 failure modes per failed trace vs 2.6 in focused systems. | MAST/ITBench (IBM) — Cascading Collapse |
| 🔴 **ALM becomes impossible at scale** | Updating any domain requires regression testing the shared orchestrator against all 100 agents. Deployment frequency drops to near zero. | Microsoft Azure Architecture Center — Maintainability principle |
| 🔴 **No team ownership — governance fails** | Ownership ambiguity means no team is accountable for quality or incidents. Shadow agents proliferate. | Microsoft CAF — Principle 27 (Maintain agent inventory) |
| 🟡 **Unpredictable and inefficient cost** | Simple FAQ queries invoke the same full-context orchestrator as complex multi-step financial workflows. | Azure Architecture Center — Optimization principle |

**📚 References**
- [HuggingFace — Design Patterns for Agentic Workflows](https://huggingface.co/blog/dcarpintero/design-patterns-for-building-agentic-workflows)
- [HuggingFace — IBM MAST Failure Taxonomy](https://huggingface.co/blog/ibm-research/itbenchandmast)
- [HuggingFace — IBM AssetOpsBench](https://huggingface.co/blog/ibm-research/assetopsbench-playground-on-hugging-face)
- [HuggingFace — Neuro SAN Decentralized Routing](https://huggingface.co/blog/danyoung/neuro-san-is-all-you-need)
- [HuggingFace — OpenEnv/Turing Tool + Routing Evaluation](https://huggingface.co/blog/openenv-turing)
- [DeepLearning.AI — Multi-Agent Collaboration](https://www.deeplearning.ai/the-batch/agentic-design-patterns-part-5-multi-agent-collaboration/)
- [DeepLearning.AI — How Agents Improve LLM Performance](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/)
- [Azure Architecture Center — AI Agent Orchestration Patterns](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns)
- [Microsoft Tech Community — AI Didn't Break Your Production](https://techcommunity.microsoft.com/blog/educatordeveloperblog/ai-didn%E2%80%99t-break-your-production-%E2%80%94-your-architecture-did/4482848)
- [MAST Paper (arXiv)](https://arxiv.org/abs/2503.13657)
- [AgentArch Benchmark — ServiceNow (arXiv 2509.10769)](https://arxiv.org/abs/2509.10769)
- [AutoGen — Microsoft Research (arXiv 2308.08155)](https://arxiv.org/abs/2308.08155)
- [LoCoBench — Context Degradation (arXiv 2511.13998)](https://arxiv.org/abs/2511.13998)
- [ReliabilityBench (arXiv 2601.06112)](https://arxiv.org/abs/2601.06112)
- [Agent Factory — Use Cases & Design Patterns](https://azure.microsoft.com/en-us/blog/agent-factory-the-new-era-of-agentic-ai-common-use-cases-and-design-patterns/)
- [Microsoft Multi-Agent Reference Architecture](https://microsoft.github.io/multi-agent-reference-architecture/docs/reference-architecture/Reference-Architecture.html)
- [ISE — Multi-Agent Systems at Scale](https://devblogs.microsoft.com/ise/multi-agent-systems-at-scale/)
- [LangGraph — Hierarchical Agent Teams](https://langchain-ai.github.io/langgraph/tutorials/multi_agent/hierarchical_agent_teams/)
{: .ref-grid}

**🏆 Recommendation**

> **Never use a single orchestrator for all agents. Research confirms that overloaded single orchestrators accumulate cascading failures (5.3 failure modes/trace), lose conversation history (24% of traces), and suffer reasoning-action disconnect (94% of traces). One domain agent per business function. Gateway routes intent only — owns zero tools. Each domain agent manages ≤ 8–10 specialist agents with a bounded tool set and dynamic task ledger. This architecture scales from 10 to 200 agents without redesign.**
{: .recommendation}

---
