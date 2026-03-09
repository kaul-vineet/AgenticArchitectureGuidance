---
layout: default
title: "🏗️ 1. Representative Architecture"
parent: Enterprise Multi-Agent Architecture
nav_order: 2
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

**Four properties that make hierarchical orchestration reliable** (Microsoft Azure Architecture Center):

| Property | What It Means in Practice |
|---|---|
| **Specialization** | Each domain agent has a focused prompt and bounded tool set — no competing priorities, no routing ambiguity |
| **Scalability** | Agents can be added or modified without redesigning the entire system — new domain = one new domain agent |
| **Maintainability** | Testing and debugging focused on individual agents — failures isolated, not system-wide |
| **Optimization** | Each domain agent can use distinct models, tools, and compute appropriate to its workload |

**Key performance evidence:**

| Design Requirement | Finding | Data | Source | What it means | Alignment to Design Requirement |
|---|---|---|---|---|---|
| 🎯 Simplest effective approach wins · 📈 Scale over custom dev · 🔵 Reduced complexity | **Agent loop vs. zero-shot single pass** | GPT-3.5 with agent loop: **95.1%** vs GPT-4 zero-shot: **67%** on HumanEval | DeepLearning.AI | A weaker, cheaper model using an agentic loop (plan → act → observe error → retry) beat GPT-4 in a single pass by 28pp. The loop gave it the ability to observe failure and correct — something no single-pass model can do regardless of capability. | Directly supports **Simplest effective approach** — architecture pattern (reflection loop) outperforms brute-force model upgrade. Supports **Scale** — right-sized models per domain reduce cost as agent count grows. Supports **Reduced complexity** — no need to upgrade every agent to the most capable model; simpler looping agents scale more predictably. |
| 🔵 Reduced complexity · 🏢 Rapid LOB integration · 🏭 100+ agents in 6–12 months | **Tool accuracy is the #1 differentiator** | Top agents: **94%** tool accuracy vs low performers: **61%** — 33pp gap | AssetOpsBench (IBM) | IBM tested agents on industrial asset operations tasks. The 33pp accuracy gap between top and bottom agents was larger than any difference in model capability, prompt quality, or memory design. Root causes: poorly defined tool descriptions, missing argument examples, ambiguous parameter names, no structured error responses. | Directly supports **Rapid LOB integration** — well-defined connectors with explicit argument formats are the single biggest quality lever for LOB system calls. Supports **Reduced complexity** — fewer, well-defined tools per agent outperform large tool libraries. Supports **100+ agents in 6–12 months** — tool quality must be a gate before any agent is promoted; a 33pp gap means a failed pilot, not a scaling problem. |
| 🔀 Orchestration across 100+ agents · 🏭 100+ agents in 6–12 months · 🧩 No-code over custom | **Ambiguity causes accuracy collapse** | Explicit intent: **90%** success → ambiguous intent: **40%** success (−50pp) | OpenEnv/Turing (HuggingFace) | The same agents were tested on identical tasks — one set worded explicitly, one set worded vaguely. A single variable (clarity of user intent) caused a 50pp collapse. When intent is unclear the agent guesses, proceeds confidently, and produces a wrong result with no error signal to the user. | Directly supports **Orchestration across 100+ agents** — at scale, a 50pp accuracy collapse from ambiguity propagates across every domain; clarification topics must be a platform-wide standard. Supports **No-code over custom** — CS Topics provide a native, no-code mechanism to build disambiguation steps; no custom code needed. Supports **100+ agents in 6–12 months** — clarification must be built into agent templates from day one, not retrofitted after failed pilots. |
| 🔀 Orchestration across 100+ agents · 📈 Scale over custom dev · 📋 Logging & observability | **Context history loss at scale** | Overloaded agents: **24%** of traces lose conversation history mid-session vs **0%** for focused agents | MAST/ITBench — FM-1.4 (IBM) | When too many domains and tools are forced into one context, the model's attention deprioritises older turns. The user says "cancel the request I just made" and the agent has already lost track of what that request was. This is not a model limitation — it is a context window management problem caused by overloading. | Directly supports **Orchestration across 100+ agents** — context loss at 24% of traces is a structural consequence of star-topology overloading; domain hierarchy eliminates it by bounding each agent's context. Supports **Scale** — focused agents maintain 0% context loss regardless of total agent count. Supports **Logging & observability** — context loss is undetectable without per-session tracing; distributed tracing across agent boundaries is required to surface this failure mode. |
| 🔀 Orchestration across 100+ agents · 🛡️ Enterprise governance · 🔵 Reduced complexity | **Reasoning disconnect in overloaded agents** | **94%** of failed traces show reasoning-action decoupling in overloaded systems | MAST/ITBench — FM-3.3 (IBM) | The agent writes a correct reasoning chain ("check leave balance → check policy → respond") and then executes different actions that don't follow it. Reasoning and action have decoupled because too much intervening context separates the plan from the execution step. | Directly supports **Enterprise governance** — an agent that reasons correctly but acts incorrectly produces confident, auditable-looking wrong outputs; governance controls must enforce action constraints in code, not rely on LLM reasoning. Supports **Orchestration across 100+ agents** — reasoning-action decoupling is a direct consequence of overloaded orchestrators; bounded domain agents with narrow context eliminate the root cause. Supports **Reduced complexity** — the fix is a simpler, narrower agent — not a larger model or more complex prompt. |
| 📋 Logging & observability · 🛡️ Enterprise governance · 🔀 Orchestration across 100+ agents | **Failure mode density** | Clean agents: **2.6** failure modes/trace · Collapsed systems: **5.3** failure modes/trace | MAST/ITBench (IBM) | In overloaded systems, failures cascade: context loss triggers wrong routing → wrong tool arguments → unhandled error → hallucinated response. By the time a user reports the failure, five things are wrong with no clear root cause. In focused agents, failures are bounded and traceable. | Directly supports **Logging & observability** — 5.3 cascading failure modes per trace are only diagnosable with end-to-end tracing across every agent hop and tool call; transcript-only logging is insufficient. Supports **Enterprise governance** — a bounded, domain-isolated agent limits blast radius to one domain, making incident ownership and accountability clear. Supports **Orchestration across 100+ agents** — the domain hierarchy pattern is the direct architectural response: each domain agent's failures are isolated, identifiable, and fixable without touching other domains. |

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
{: .ref-grid}

**🏆 Recommendation**

> **Never use a single orchestrator for all agents. Research confirms that overloaded single orchestrators accumulate cascading failures (5.3 failure modes/trace), lose conversation history (24% of traces), and suffer reasoning-action disconnect (94% of traces). One domain agent per business function. Gateway routes intent only — owns zero tools. Each domain agent manages ≤ 8–10 specialist agents with a bounded tool set and dynamic task ledger. This architecture scales from 10 to 200 agents without redesign.**
{: .recommendation}

---
