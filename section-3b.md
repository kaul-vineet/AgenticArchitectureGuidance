---
layout: default
title: "🔗 4. As Child / Connected Agent"
parent: Enterprise Multi-Agent Architecture
nav_order: 5
---

## 4. 🔗 As Child / Connected Agent

---

### 🟦 Copilot Studio as Child / Connected Agent

#### Scenario 1 — CS Child / Connected Agent where Master Agent is Copilot Studio

| ✅ Pros | ❌ Cons |
|---|---|
| · **Shared container (inline)** — variables, auth, Dataverse, PA connectors natively shared with no extra config | · **Sequential invocation only** — CS parent calls child agents one at a time; no parallel fan-out |
| · **Zero-latency inline option** — inline child agents have no orchestration hop; same session, same transcript | · **Session chain limits** — 30 min idle / 60 min total / 100 turns applies to the full parent + child chain |
| · **Typed I/O contracts (YAML)** — structured, parseable return values for connected agents | · **Separate transcript for connected agents** — requires correlationId pattern to link parent and child sessions |
| · **Native identity inheritance** — same Entra connection, same Dataverse row-level security; no cross-platform auth | · **Instructions cap (8,000 chars)** — limits child agent complexity; prompts must be concise |
| · **Unified ALM** — Power Platform Pipelines promotes parent and child together through Dev→Test→Prod | · **Citation propagation gap** — known limitation when child uses knowledge sources; citations may not surface in parent response |
| · **Agent 365 + CoE Toolkit** — full governance visibility over both parent and child in M365 Admin Center | · **Model selection locked** — both parent and child limited to Microsoft-managed models |
| · **Reusable connected agents** — one CS child can serve multiple CS parent agents independently | |
| · **Independent ALM for connected agents** — child can be versioned and deployed without touching the parent | |

#### Scenario 2 — CS Child / Connected Agent where Master Agent is Foundry

| ✅ Pros | ❌ Cons |
|---|---|
| · **No-code child authoring** — domain teams build CS child agents with no code while Foundry handles orchestration | · **Not natively supported** — Foundry → CS calling requires Direct Line REST API workaround; no first-party A2A support from Foundry to CS |
| · **1,400+ PA connectors** — CS child brings Power Automate connector library into a Foundry-orchestrated system | · **Text-only return** — CS returns plain text to Foundry; no typed variable contract, no structured JSON handoff |
| · **M365 channel capability** — CS child handles Teams / SharePoint interactions that Foundry cannot natively address | · **Auth complexity** — Foundry must authenticate to CS via Direct Line channel secret; separate credential management |
| · **Dataverse access** — CS child natively reads/writes Dataverse without custom connectors | · **Fully separate session context** — CS session and Foundry thread are independent; correlationId must be injected manually |
| · **Team separation** — domain teams own CS child agents; platform team owns Foundry orchestration | · **Distributed debugging** — traces split across App Insights (Foundry) and Dataverse/CoE (CS); no unified trace |
| · **Gradual migration path** — existing CS agents can be called from Foundry without rebuilding | · **ALM complexity** — separate pipelines (PP Pipelines for CS, Azure DevOps/GitHub Actions for Foundry) must stay in sync |
| | · **Latency overhead** — REST call across platforms adds 200–500ms per child invocation vs native A2A |
| | · **Preview / unsupported** — Direct Line integration is not an officially supported enterprise pattern; subject to breaking changes |

---

### 🟧 Foundry as Child / Connected Agent

#### Scenario 1 — Foundry as Connected Agent where Master Agent is Copilot Studio

CS connects to a Foundry agent via a native platform connector using the Foundry project endpoint URL and Agent ID. This is distinct from the open A2A protocol — it uses Microsoft's internal CS-to-Foundry integration.

> ⚠️ **Status: Public Preview.** The CS → Foundry connection is not production-ready per Microsoft. Preview features "aren't meant for production use and may have restricted functionality." Only agents created in the **new Foundry portal** are supported — legacy (classic) portal agents return a `404 - Version not found` error.

| ✅ Pros | ❌ Cons |
|---|---|
| · **No-code setup in CS** — Foundry agent added via UI wizard in the CS Agents page; no custom code required on the CS side | · **Public preview only** — not recommended for production; feature may change or be restricted |
| · **Full Foundry tool ecosystem** — Foundry child can use Code Interpreter, File Search, Azure AI Search, Azure Functions, and OpenAPI tools unavailable natively in CS | · **New Foundry portal only** — agents from the legacy classic portal return `404`; must rebuild or migrate to new portal |
| · **Model flexibility** — Foundry child can use any model in the Foundry catalog (GPT-4o, Llama, DeepSeek, Grok) with full parameter control (temperature, top-p, max tokens) | · **Latency multiplies at each hop** — CS runs its own LLM orchestration to decide when to invoke Foundry, then Foundry runs its own; double the reasoning cost before a response reaches the user |
| · **Separation of concerns** — Foundry agent developed, versioned, and deployed independently by a separate engineering team; CS treats it as an opaque capability unit | · **CS orchestration is strictly sequential** — CS calls connected agents one at a time, not in parallel; unsuitable for real-time scenarios requiring multiple concurrent agent calls |
| · **CS handles channel layer** — Teams, SharePoint, M365 Copilot channels remain managed in CS while Foundry handles complex reasoning, data processing, and tool-heavy tasks | · **Citations not reliably passed back** — Microsoft documents this as a known limitation: "Citations might not always be maintained when passing outputs back from an agent back to a calling agent" |
| · **Gradual migration** — existing Foundry agents can be onboarded as connected agents without any rebuild | · **CS strips grounding references** — SharePoint links and document references are removed by CS's generative orchestrator before passing responses between agents |
| · **CS autonomous orchestration** — CS generative orchestrator can reference the Foundry agent in its instructions for autonomous routing decisions | · **Black-box orchestration** — CS uses a generative (LLM-driven) orchestrator to decide whether to invoke Foundry; you cannot always control or predict when the Foundry agent is called |
| | · **Reuse restriction** — an agent with connected agents cannot itself be used as a connected agent to another parent; limits certain multi-tier topologies |
| | · **Responsible use burden** — when connecting to agents outside CS, the implementer is responsible for data flows, security, permissions, and output quality |

#### Scenario 2 — Foundry as Child when Master Agent is Foundry (Foundry-to-Foundry)

A parent Foundry agent invokes a child Foundry agent as a tool call using `ConnectedAgentToolDefinition`. The parent uses natural language routing to decide which child to call. Microsoft now recommends migrating to the **Workflows** feature (visual, declarative orchestration) over the classic Connected Agents API, which is deprecated and will be retired on **March 31, 2027**.

> ⚠️ **Depth limit:** Max depth = 2. Sub-agents cannot have their own sub-agents. Exceeding this returns an `Assistant Tool Call Depth Error`. This is a hard service limit.

| ✅ Pros | ❌ Cons |
|---|---|
| · **No custom orchestration code** — parent agent uses natural language routing to decide which child to invoke; no hardcoded if/else logic | · **Hard depth limit = 2** — sub-agents cannot have their own sub-agents in the classic connected agents model; prevents deeply nested hierarchies |
| · **Modular specialized agents** — each child agent has a focused role (e.g., clause summarizer, compliance validator); improves accuracy, maintainability, and traceability | · **Connected Agents (classic) is deprecated** — only in `2025-05-15-preview` API; must migrate to Workflows before March 31, 2027 |
| · **Multiple specialized children per parent** — parent supports sibling child agents enabling fan-out to different specialists | · **Multi-agent Workflows is preview** — new Workflows orchestration patterns are public preview; not production-ready per Microsoft |
| · **Full Foundry tooling per agent** — each child independently uses Code Interpreter, File Search, Azure AI Search, Azure Functions, OpenAPI tools | · **Citations not reliably propagated** — "It is currently not possible to guarantee citations will be passed from connected agents" |
| · **Parallel execution via SDK** — developers can implement fan-out/fan-in in code (Python/.NET SDK), launching multiple sub-agents simultaneously and reducing latency | · **Local function tools blocked** — connected agents cannot call local functions using the function calling tool; use OpenAPI or Azure Functions instead |
| · **Full observability** — App Insights traces all parent-child interactions; complete conversation-level tracing including agent-to-agent messages | · **Cost compounds** — each agent hop incurs its own LLM inference cost; a parent invoking 3 GPT-4o children multiplies token consumption with no shared context |
| · **Structured JSON output** — child agents can return typed JSON schemas; enables deterministic data passing between agents | · **Session management per agent** — each agent maintains its own thread context; explicit state design required across agents |
| · **Visual Workflows builder** — supports branching, variables, human-in-the-loop approvals, and Power Fx formulas without orchestration code | · **Infrastructure overhead** — requires Cosmos DB, Azure AI Search, and Azure Functions; unlike CS's managed PaaS model |
| · **Independent deployment and ALM** — parent and child agents published separately with own endpoints and Agent Identities | · **Max 128 tools per agent** — caps the total number of connected agents plus other tools a parent can have |
| · **Enterprise security per agent** — each published agent gets its own Managed Identity (Entra), RBAC, and isolated Azure resource access | · **Separate publish required** — parent and child must both be published separately before production; development identity permissions do not transfer |

---

### 🏆 Recommendation

| Criterion | CS Inline Child | CS Connected | Foundry Connected (CS parent) | Foundry Child (Foundry parent) |
|---|---|---|---|---|
| ⚡ Speed | Fastest — no hop | Fast — hours to build | Moderate — double LLM hop | Moderate — tool-call overhead |
| 🧩 No-code | ✅ CS Topics | ✅ CS | ✅ CS wizard (Foundry side requires setup) | ⚠️ Foundry Workflows (preview) |
| 🏭 Production ready | ✅ GA | ✅ GA | ❌ Preview only | ❌ Preview / deprecated path |
| 📈 Complex reasoning | ❌ CS model limits | ❌ CS model limits | ✅ Any Foundry model, Code Interpreter | ✅ Full Foundry toolset per agent |
| 🔀 Parallel execution | ❌ Sequential | ❌ Sequential | ❌ Sequential (CS orchestrator) | ✅ Via SDK code |
| 🔍 Observability | CS transcript | CS transcript (correlationId) | Split — CS + App Insights | ✅ Full App Insights tracing |
| 🧩 Citations | ✅ Reliable | ⚠️ Gap for knowledge sources | ❌ Known limitation | ❌ Known limitation |

> **Use CS inline topics for tightly scoped sub-tasks within the same domain where shared context and zero-latency matter. Use CS connected agents when the sub-domain needs independent governance, team ownership, or reuse across multiple parents. Add Foundry as a connected agent under CS only for complex reasoning, code execution, or large RAG — accepting preview limitations and citation gaps. Use Foundry-to-Foundry connected agents only for engineering-owned domains requiring structured JSON output, parallel SDK execution, or full observability; plan migration from classic Connected Agents to Workflows before March 2027. Target ratio: 60% CS inline child, 25% CS connected, 10% Foundry connected, 5% non-MSFT connected.**
{: .recommendation}

---

### 📚 References
- [Copilot Studio — Add Other Agents](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-add-other-agents)
- [Copilot Studio — Connect to a Foundry Agent (preview)](https://learn.microsoft.com/en-us/microsoft-copilot-studio/add-agent-foundry-agent)
- [Copilot Studio — Child/Connected Agent I/O](https://microsoft.github.io/mcscatblog/posts/copilot-studio-child-connected-agents-inputs-outputs/)
- [Foundry — Connected Agents (classic)](https://learn.microsoft.com/en-us/azure/foundry-classic/agents/how-to/connected-agents)
- [Foundry — Workflows (multi-agent orchestration)](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/workflow)
- [Foundry — Quotas and Limits](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/limits-quotas-regions)
- [Integrate Foundry Agents with Copilot Studio](https://techcommunity.microsoft.com/blog/azure-ai-foundry-blog/integrate-custom-azure-ai-agents-with-copilot-studio-and-m365-copilot/4405070)
- [Multi-Agent Orchestration — CS vs Foundry](https://ragnarheil.de/multi-agent-orchestration-copilot-studio-vs-azure-ai-foundry-heres-what-you-actually-need-to-know/)
- [Salesforce — Agentforce A2A](https://www.salesforce.com/blog/agent-interoperability/)
{: .ref-grid}

---
