---
layout: default
title: "🔗 7. Child / Connected Agent"
parent: Enterprise Multi-Agent Architecture
nav_order: 9
---

## 7. 🔗 Child / Connected Agent

---

<h3 style="color:#0078d4;font-weight:700;border-left:4px solid #0078d4;padding-left:0.6rem;margin-top:1.5rem;">🟦 CS Child / Connected Agent where Master Agent is Copilot Studio</h3>

CS can act as an inline child agent or a connected agent when the parent is also Copilot Studio. Inline child agents share the parent's container — variables, auth, Dataverse, and connectors — with zero configuration. Connected agents run independently with their own session and ALM.

| ✅ Pros | ❌ Cons |
|---|---|
| · **Shared container (inline)** — variables, auth, Dataverse, PA connectors natively shared with no extra config | · **Sequential invocation only** — CS parent calls child agents one at a time; no parallel fan-out |
| · **Zero-latency inline option** — inline child agents have no orchestration hop; same session, same transcript | · **Session chain limits** — 30 min idle / 60 min total / 100 turns applies to the full parent + child chain |
| · **Typed I/O contracts (YAML)** — structured, parseable return values for connected agents | · **Separate transcript for connected agents** — requires correlationId pattern to link parent and child sessions |
| · **Native identity inheritance** — same Entra connection, same Dataverse row-level security; no cross-platform auth complexity | · **Instructions cap (8,000 chars)** — limits child agent complexity; prompts must be concise |
| · **Unified ALM** — Power Platform Pipelines promotes parent and child together through Dev→Test→Prod | · **Citation propagation gap** — known limitation when child uses knowledge sources; citations may not surface in parent response |
| · **Agent 365 + CoE Toolkit** — full governance visibility over both parent and child in M365 Admin Center | · **Model selection locked** — both parent and child limited to Microsoft-managed models |
| · **Reusable connected agents** — one CS child can serve multiple CS parent agents independently | |
| · **Independent ALM for connected agents** — child can be versioned and deployed without touching the parent | |
| · **GA and production-ready** — no preview limitations; full enterprise support | |

---

<h3 style="color:#e07a10;font-weight:700;border-left:4px solid #e07a10;padding-left:0.6rem;margin-top:1.5rem;">🟧 Foundry as Connected Agent where Master Agent is Copilot Studio</h3>

CS connects to a Foundry agent via a native platform connector using the Foundry project endpoint URL and Agent ID. This uses Microsoft's internal CS-to-Foundry integration, distinct from the open A2A protocol.

> ⚠️ **Status: Public Preview.** The CS → Foundry connection is not production-ready. Only agents created in the **new Foundry portal** are supported — legacy portal agents return `404 - Version not found`.

| ✅ Pros | ❌ Cons |
|---|---|
| · **No-code setup in CS** — Foundry agent added via UI wizard in the CS Agents page; no custom code required on the CS side | · **Public preview only** — not recommended for production; feature may change or be restricted |
| · **Full Foundry tool ecosystem** — Code Interpreter, File Search, Azure AI Search, Azure Functions, OpenAPI tools unavailable natively in CS | · **New Foundry portal only** — legacy classic portal agents return `404`; must rebuild or migrate |
| · **Model flexibility** — any Foundry catalog model (GPT-4o, Llama, DeepSeek, Grok) with full parameter control | · **Latency multiplies at each hop** — CS runs its own LLM orchestration, then Foundry runs its own; double reasoning cost |
| · **Separation of concerns** — Foundry agent developed and deployed independently by an engineering team | · **CS orchestration is strictly sequential** — CS calls connected agents one at a time; not suitable for concurrent scenarios |
| · **CS handles channel layer** — Teams, SharePoint, M365 Copilot channels stay in CS; Foundry handles complex reasoning | · **Citations not reliably passed back** — documented Microsoft limitation: citations may not propagate through CS orchestrator |
| · **Complex instructions supported** — no system prompt size limit on the Foundry side; full control over agent behaviour | · **CS strips grounding references** — SharePoint links and document refs removed by CS orchestrator before passing responses |
| · **Gradual migration** — existing Foundry agents onboarded without rebuild | · **Black-box orchestration** — CS generative orchestrator decides when to invoke Foundry; cannot always be predicted or controlled |
| | · **Reuse restriction** — a CS agent with connected agents cannot itself be used as a connected agent to another parent |
| | · **Responsible use burden** — implementer responsible for data flows, security, and output quality of the Foundry agent |

---

---

<h3 style="color:#0078d4;font-weight:700;border-left:4px solid #0078d4;padding-left:0.6rem;margin-top:1.5rem;">🟦 CS Child Agent where Master Agent is Foundry</h3>

Foundry has no native first-party way to call a CS agent. This requires a **Direct Line REST API workaround** — CS is exposed via its Direct Line channel secret and called as an HTTP tool from Foundry. This is not an officially supported enterprise integration pattern.

| ✅ Pros | ❌ Cons |
|---|---|
| · **No-code child authoring** — domain teams build CS agents with no code; integration complexity sits on the Foundry side | · **Not natively supported** — requires Direct Line REST API workaround; no first-party A2A from Foundry to CS |
| · **1,400+ PA connectors** — CS child brings Power Automate connector library into a Foundry-orchestrated system | · **Text-only return** — CS returns plain text to Foundry; no typed JSON contract, no structured handoff |
| · **M365 channel capability** — CS handles Teams / SharePoint interactions that Foundry cannot natively address | · **Auth complexity** — Foundry authenticates to CS via Direct Line channel secret; separate credential management |
| · **Dataverse access** — CS child reads/writes Dataverse natively without custom connectors | · **Fully separate session context** — CS session and Foundry thread are independent; correlationId must be injected manually |
| · **Team separation** — domain teams own CS agents; platform team owns Foundry orchestration | · **Distributed debugging** — traces split across App Insights (Foundry) and Dataverse/CoE (CS); no unified trace |
| · **Gradual migration** — existing CS agents callable from Foundry without rebuilding | · **ALM complexity** — PP Pipelines (CS) and Azure DevOps/GitHub Actions (Foundry) must stay in sync separately |
| | · **Latency overhead** — REST call adds 200–500ms per invocation vs native A2A |
| | · **Unsupported pattern** — subject to breaking changes; not an enterprise-grade integration path |

---

<h3 style="color:#e07a10;font-weight:700;border-left:4px solid #e07a10;padding-left:0.6rem;margin-top:1.5rem;">🟧 Foundry as Child when Master Agent is Foundry (Foundry-to-Foundry)</h3>

A parent Foundry agent invokes child Foundry agents via the **Workflows** feature (GA) or the deprecated Classic Connected Agents API. Workflows is the recommended path — it supports five orchestration patterns including Concurrent fan-out and the Magentic supervisor.

> ⚠️ **Classic Connected Agents API is deprecated** — retire March 31, 2027. Migrate to Foundry Workflows. **Multi-agent Workflows patterns are public preview** — plan accordingly.

| ✅ Pros | ❌ Cons |
|---|---|
| · **Five orchestration patterns** — Sequential, Concurrent (fan-out/fan-in), Handoff, Group Chat, Magentic (supervisor) | · **Classic Connected Agents deprecated** — `ConnectedAgentToolDefinition` retires March 2027; must migrate to Workflows |
| · **Concurrent fan-out** — all child agents receive same input simultaneously; results auto-aggregated; reduces latency for parallel tasks | · **Multi-agent Workflows in preview** — not production-ready per Microsoft |
| · **Magentic supervisor pattern (P1)** — manager dynamically selects which specialist executes next; stall detection; replanning; optional HITL | · **Classic depth limit = 2** — sub-agents cannot have their own sub-agents in classic model; Workflows removes this constraint |
| · **Full Foundry tooling per agent** — Code Interpreter, File Search, Azure AI Search, Azure Functions, OpenAPI tools per child | · **Citations not reliably propagated** — "It is currently not possible to guarantee citations will be passed from connected agents" |
| · **Structured JSON output** — child agents return typed JSON schemas; deterministic data passing between agents | · **Local function tools blocked (classic)** — connected agents cannot call local functions; use OpenAPI or Azure Functions instead |
| · **Full App Insights observability** — every parent-child message, tool call, and hop is traced end-to-end | · **Cost compounds** — each hop incurs its own LLM inference cost; no shared token context between agents |
| · **Parallel execution via SDK** — fan-out/fan-in in Python/.NET; multiple children concurrently | · **Session management per agent** — each agent maintains its own thread; explicit state design required |
| · **Visual Workflows builder** — branching, variables, human-in-the-loop approvals, Power Fx without custom orchestration code | · **Infrastructure overhead** — Cosmos DB, Azure AI Search, Azure Functions required |
| · **Independent ALM** — each child agent published separately with own Managed Identity and RBAC | · **Max 128 tools per agent** — caps connected agents + other tools on a single parent |

---

---

> 📎 How each of the 62 Key Architectural Principles applies to CS and Foundry in the child agent role is mapped in [1a. Alignment to Key Architectural Principles](../section-align).

### 📚 References
- [Copilot Studio — Add Other Agents](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-add-other-agents)
- [Copilot Studio — Connect to a Foundry Agent (preview)](https://learn.microsoft.com/en-us/microsoft-copilot-studio/add-agent-foundry-agent)
- [Copilot Studio — Child/Connected Agent I/O](https://microsoft.github.io/mcscatblog/posts/copilot-studio-child-connected-agents-inputs-outputs/)
- [Foundry — Connected Agents (classic, deprecated)](https://learn.microsoft.com/en-us/azure/foundry-classic/agents/how-to/connected-agents)
- [Foundry — Workflows (multi-agent orchestration)](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/workflow)
- [Agent Framework — Concurrent Orchestration](https://learn.microsoft.com/en-us/agent-framework/workflows/orchestrations/concurrent)
- [Agent Framework — Magentic Orchestration](https://learn.microsoft.com/en-us/agent-framework/workflows/orchestrations/magentic)
- [Foundry — Quotas and Limits](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/limits-quotas-regions)
- [Integrate Foundry Agents with Copilot Studio](https://techcommunity.microsoft.com/blog/azure-ai-foundry-blog/integrate-custom-azure-ai-agents-with-copilot-studio-and-m365-copilot/4405070)
- [Multi-Agent Orchestration — CS vs Foundry](https://ragnarheil.de/multi-agent-orchestration-copilot-studio-vs-azure-ai-foundry-heres-what-you-actually-need-to-know/)
{: .ref-grid}

---
