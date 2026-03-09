---
layout: default
title: "🔗 4. As Child / Connected Agent"
parent: Enterprise Multi-Agent Architecture
nav_order: 6
---

## 4. 🔗 As Child / Connected Agent

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

<h3 style="color:#107c10;font-weight:700;border-left:4px solid #107c10;padding-left:0.6rem;margin-top:1.5rem;">🏆 Recommendation — CS as Child</h3>

*When parent is Copilot Studio: choose between CS child agent and Foundry connected agent.*

| Criterion | 🟦 CS Child / Connected *(CS parent)* | 🟧 Foundry Connected *(CS parent)* |
|---|---|---|
| ⚡ Speed | ✅ **Wins** — hours to build; no new platform | ⚠️ Slower — Foundry portal setup + agent publish required |
| 🧩 No-code authoring | ✅ **Wins** — fully no-code CS Topics | ✅ CS wizard (no-code to add); Foundry agent setup requires portal work |
| 🏭 Production ready | ✅ **Wins** — GA; full enterprise support | ❌ Public preview only — not for production |
| 🔵 Reduced complexity | ✅ **Wins** — shared container; zero extra infra | ⚠️ Separate session; CS strips grounding; double LLM hop |
| 🛡️ Governance | ✅ **Wins** — Agent 365 + CoE Toolkit covers both parent and child | ⚠️ Split — CS governance + Foundry App Insights; no unified view |
| 📋 Logging & observability | ✅ Unified CS transcript (inline) / correlationId (connected) | ⚠️ Split — CS orchestration (black box) + Foundry App Insights |
| 📈 Complex agent instructions | ⚠️ 8,000 char limit applies to child agent | ✅ **Wins** — no system prompt size limit on Foundry side |
| 📈 Complex reasoning / tools | ⚠️ CS model limits; no Code Interpreter | ✅ **Wins** — Code Interpreter, File Search, any Foundry model |
| 🔗 A2A / Interoperability | ✅ CS child reusable across CS parents via A2A | ⚠️ CS → Foundry uses internal connector, not open A2A |
| 🏢 LOB system integration | ✅ **Wins** — 1,400+ PA connectors; no custom code | ⚠️ Foundry LOB requires Azure Functions or OpenAPI tools |
| 🥇 P1 — Supervisor pattern | ✅ CS parent acts as supervisor over CS child agents | ✅ CS parent delegates to Foundry for specialist tasks |
| 🥇 P1 — Complex instructions | ⚠️ Child prompt capped at 8,000 chars | ✅ **Wins** — unlimited Foundry system prompt |

> **When your parent is CS, use CS child agents as the default** — they are GA, zero-config, governance-native, and factory-ready. Add Foundry as a connected agent only when the specific task exceeds what CS can deliver: complex reasoning beyond CS model limits, Code Interpreter, large-scale RAG, or a system prompt that cannot fit in 8,000 characters. Do not add Foundry as a connected agent for tasks that CS can handle — the preview status, double LLM hop, and citation gap are real production risks.
{: .recommendation}

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

<h3 style="color:#107c10;font-weight:700;border-left:4px solid #107c10;padding-left:0.6rem;margin-top:1.5rem;">🏆 Recommendation — Foundry as Child</h3>

*When parent is Foundry: choose between CS child via workaround and Foundry-to-Foundry.*

| Criterion | 🟦 CS Child *(Foundry parent — workaround)* | 🟧 Foundry Child *(Foundry parent)* |
|---|---|---|
| ⚡ Speed | ⚠️ Moderate — CS authoring fast; Direct Line setup adds integration time | ⚠️ Moderate — Foundry Workflow node setup required per child |
| 🧩 No-code authoring | ✅ CS child is no-code; integration work is on Foundry side | ⚠️ Workflows portal for 3 patterns; Concurrent and Magentic require SDK |
| 🏭 Production ready | ❌ Unsupported pattern — Direct Line only; subject to breaking changes | ⚠️ Workflows in preview; Classic deprecated |
| 🔵 Reduced complexity | ❌ Two ALM pipelines; split tracing; auth complexity; text-only return | ⚠️ Infrastructure overhead (Cosmos DB, Functions); per-agent thread management |
| 🛡️ Governance | ❌ No unified trace; CS governance separate from Foundry | ✅ **Wins** — Managed Identity per agent; RBAC; full App Insights audit trail |
| 📋 Logging & observability | ❌ Split — App Insights + Dataverse/CoE; no end-to-end trace | ✅ **Wins** — full OpenTelemetry; every hop, tool call, and handoff traceable |
| 📈 Complex reasoning / tools | ✅ CS brings PA connectors + Dataverse + M365 channels | ✅ **Wins** — Code Interpreter, File Search, Azure AI Search, structured JSON |
| 🔀 Orchestration at scale | ❌ Sequential REST calls only; no parallel fan-out | ✅ **Wins** — Concurrent fan-out; Magentic supervisor; Group Chat |
| 🏢 LOB system integration | ✅ **Wins** — 1,400+ PA connectors; Dataverse native; M365 channels | ⚠️ LOB via Azure Functions or OpenAPI tools; narrower connector library |
| 🥇 P1 — Supervisor / Magentic | ❌ No supervisor capability; Foundry orchestrates one REST call | ✅ **Wins** — Magentic pattern: dynamic selection, stall detection, replanning |
| 🥇 P1 — Complex instructions | ⚠️ CS child capped at 8,000 chars | ✅ **Wins** — no system prompt limit per Foundry child |
| 🥈 P2 — Custom orchestration | ❌ No custom logic; text-only return | ✅ **Wins** — full Python/C#/JS/Java SDK; state machines; conditional branching |

> **When your parent is Foundry, use Foundry-to-Foundry child agents via Workflows** — they deliver full observability, parallel execution, supervisor patterns (Magentic), structured JSON output, and independent governance per agent. The infrastructure overhead is the accepted cost of Foundry-parent domains. Use CS child agents via Direct Line only as a transitional workaround for existing CS agents handling M365 channel interactions or Dataverse writes that cannot be rebuilt in Foundry — treat it as technical debt and plan migration when first-party Foundry → CS support becomes GA. Never use the CS workaround for new agent builds.
{: .recommendation}

---

<h3 style="color:#323130;font-weight:700;border-left:4px solid #323130;padding-left:0.6rem;margin-top:1.5rem;">📝 Design Notes — Alignment to Key Architectural Principles</h3>

Both child agent scenarios are evaluated against the 28 Key Architectural Principles. The table below shows how each principle is satisfied — or where trade-offs apply — for CS as Child and Foundry as Child.

| # | Principle | 🟦 CS as Child | 🟧 Foundry as Child |
|---|---|---|---|
| 1 | Minimise LLM calls | Inline child adds zero extra LLM hop — same session as parent; connected adds one | Each Foundry child agent is a separate LLM call; accept only where specialist value is proven |
| 2 | Default to Flows, escalate to Crews | Child Topics are deterministic; PA flows handle all tool calls in the child | Workflow sub-agent can use deterministic tools; planning only if task is genuinely open-ended |
| 3 | Clarification before action | Parent CS Topics handle disambiguation before delegation to child | Parent Workflow node or Magentic manager requests clarification before invoking specialist |
| 4 | External verification — never self-check | Parent reads back typed I/O output from child; deterministic read-back, not agent self-check | Parent validates child JSON output schema before passing downstream; structured output enables checks |
| 5 | Externalize termination logic | Parent Topic conditions + session chain limits (100 turns, 60 min) for full parent+child chain | Workflow termination nodes + SDK `max_steps` per child; never in LLM prompt |
| 6 | Tool argument quality | Typed YAML I/O for connected agents; shared parent connector for inline; structured error responses | Structured JSON schema per child; argument validation at each hop; App Insights flags failures |
| 7 | Consolidate tools | Inline child uses parent's PA connectors — no duplication; connected child has own connector set | Each Foundry child has own tool set; consolidation is an explicit SDK design decision |
| 8 | Multi-agent is a reliability tax | Inline child: zero extra hop; connected child: one extra hop — each adds reliability cost | Each Workflow node adds one LLM inference cost; use only where specialisation value outweighs cost |
| 9 | Context hygiene at scale | Inline: parent context shared with no extra config; connected: own session, correlationId links | Per-thread context per child; explicit state passed across agent boundaries; compaction in SDK code |
| 10 | Sensitive state in side channel | Inline child inherits parent Key Vault / PA connector credentials; connected uses own Entra connection | Managed Identity per child; credentials never in Workflow parameters or child system prompt |
| 11 | Tool accuracy > 90% as deployment gate | CoE deployment gate per child agent before PP Pipelines promotion | App Insights evaluation threshold per child before production promotion |
| 12 | Human-in-the-loop for irreversible actions | PA approval flow in parent or child before irreversible writes; parent waits for confirmation | HITL Workflow node; parent Workflow pauses for approval before triggering next child |
| 13 | Use lowest complexity | ✅ **Wins** for CS-native tasks — inline child is lowest complexity option; zero extra infra | ⚠️ Infrastructure overhead; accept only where Foundry capabilities (parallel, Magentic, JSON schema) are required |
| 14 | Justify every agent boundary | Each child agent scoped to one domain or compliance boundary; Gateway → Domain → Specialist enforced | Each Workflow sub-agent boundary justified by specialization need; no boundary without demonstrated failure reason |
| 15 | Single responsibility per agent | Each CS child has a narrow domain; CoE Toolkit monitors for overlap | Each Foundry child has one specialist task in the Workflow; App Insights flags redundant invocations |
| 16 | Right-size the model per agent role | Child model locked to Microsoft-managed — no per-child tuning | Per-child model selection; smaller, cheaper models for extraction or formatting children |
| 17 | Validate output before passing downstream | Typed YAML I/O contract for connected agents; Topic condition checks on inline output | JSON schema validation per child; parent orchestrator halts or retries on malformed output |
| 18 | Surface errors explicitly | Error Topics in CS child; connector structured error responses; parent handles child errors | SDK error handling per child; errors propagated to parent orchestrator; never swallowed |
| 19 | Idempotency for all writes | Idempotency key in child PA flows; safe to re-trigger | Idempotency key in child Azure Functions or OpenAPI tool definitions |
| 20 | Kill switch | Child agent disabled in CS portal independently of parent | Foundry child disabled in Foundry portal; parent Workflow fails gracefully or reroutes |
| 21 | Security trimming in every agent | Dataverse RLS per child CS agent; child cannot return data above user entitlement | Managed Identity RBAC per Foundry child; trimming enforced in each child's response logic |
| 22 | Content safety at every layer | AI Builder content safety filter per child CS agent; applied at input and output | Azure AI Content Safety filter per Foundry child; applied at every agent boundary |
| 23 | Scope agent authority to operations | Topic-level permissions per child; PA connector scopes limited to required operations | RBAC per child; blast radius caps enforced in SDK orchestration code |
| 24 | Instrument all handoffs | correlationId propagated from parent to each child; Dataverse logs every parent-child hop | Full OpenTelemetry; every parent-child message, tool call, and handoff traceable in App Insights |
| 25 | Automated evaluation in CI/CD | PP Pipelines with solution validation per child agent before promotion | Azure DevOps / GitHub Actions per Foundry child; evaluation step before production |
| 26 | Evaluate continuously in production | CoE Toolkit monitoring per child agent; intent resolution and tool accuracy dashboards | App Insights dashboards per child; custom evaluation queries per specialist |
| 27 | Maintain agent inventory | ✅ **Wins** — Agent 365 tracks all CS child agents; registered, owned, versioned | ⚠️ No native registry for Foundry children; custom Azure Policy + Entra tracking required |
| 28 | Externalize and version-control prompts | Child agent instructions in CS solution; promoted via PP Pipelines with controlled rollout | Child prompts in Azure DevOps; versioned separately from parent Workflow YAML |

---

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
