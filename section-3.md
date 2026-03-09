---
layout: default
title: "⚖️ 3. Master & Domain Agents — Platform Recommendation"
parent: Enterprise Multi-Agent Architecture
nav_order: 4
---

## 3. ⚖️ Master & Domain Agents — Platform Recommendation

---

### 👑 As Parent / Master Agent

#### 🟦 Copilot Studio as Parent

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

#### 🤝 Agent Connectivity — What CS Parent Supports

Copilot Studio as parent supports two tiers of agent connectivity: **any Copilot Studio agent as a child agent** and **any A2A-compliant agent as a connected agent**.

**Child Agents — Any Copilot Studio Agent**

A CS parent can embed any other Copilot Studio agent as an inline child agent directly within its topic flow.

| ✅ Pros | ❌ Cons |
|---|---|
| · **Any CS agent is eligible** — any agent built in Copilot Studio can be added as a child; no special configuration required | · **CS agents only** — non-MSFT agents (Agentforce, ServiceNow) and Foundry agents cannot be child agents; connected tier only |
| · **Shared container** — child inherits parent's variables, auth context, Dataverse connections, and Power Automate connectors with zero extra config | · **Sequential invocation** — child agents are called one at a time; no parallel fan-out within the child tier |
| · **Zero-latency inline execution** — child runs in the same session and transcript as the parent; no orchestration hop | · **Session chain limits apply** — the 30 min idle / 60 min total / 100 turn cap covers the full parent + child chain |
| · **Native identity inheritance** — same Entra connection, same Dataverse row-level security row across parent and child | · **Instructions cap (8,000 chars)** — child agent system prompts must stay within limit |
| · **Unified ALM** — Power Platform Pipelines promotes parent and child together through Dev → Test → Prod | · **Model selection locked** — both parent and child are bound to Microsoft-managed models |

**Connected Agents — Any A2A-Compliant Agent**

A CS parent can call any agent that exposes an A2A-compliant endpoint as a connected agent — including other CS agents, Foundry agents, Agentforce, and ServiceNow Virtual Agent.

| ✅ Pros | ❌ Cons |
|---|---|
| · **Cross-platform reach** — CS parent can delegate to CS, Foundry, Agentforce, or ServiceNow agents via the A2A protocol | · **Separate session context** — connected agent runs its own thread; correlationId must be injected to link parent and child traces |
| · **Independent ALM** — connected agents are versioned and deployed independently; upgrading a connected agent does not require touching the parent | · **Text-only return (non-CS agents)** — Foundry and non-MSFT connected agents return plain text; no typed variable contract |
| · **Reusable** — a single connected agent can serve multiple CS parent agents simultaneously | · **Preview limitations** — CS → Foundry and CS → non-MSFT A2A connections are in preview; subject to breaking changes |
| · **Team separation** — domain teams own connected agents; platform team owns the parent gateway | · **Citation propagation gap** — citations from a connected agent's knowledge sources may not surface in the parent response |
| · **No rebuild required** — existing agents on any A2A-compatible platform can be onboarded as connected agents without rewriting | · **Latency overhead** — A2A hop adds 200–500 ms per connected agent invocation vs inline child |

#### 🟧 Foundry Agent Service as Parent

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

#### 🏆 Recommendation

| Criterion | Verdict |
|---|---|
| ⚡ Speed | **Copilot Studio wins** — hours to first agent vs. days |
| 🧩 No-code | **Copilot Studio wins** — no-code native |
| 📈 Scale | **Copilot Studio wins** — agent templates, Component Collections, PP Pipelines |
| 🏭 100+ agents | **Copilot Studio wins** — factory model built for this scale |
| 🔵 Reduced complexity | **Copilot Studio wins** — single platform, Dataverse only, no extra infra |

> **Use Copilot Studio as the parent/master agent. Introduce Foundry as parent only for domains requiring parallel execution, deterministic state-machine workflows, or compliance-grade BYO storage. Target fewer than 3 Foundry-parent domains out of your total agent estate.**
{: .recommendation}

---

### 🔗 As Child / Connected Agent

#### 🟦 Scenario 1 — CS Child / Connected Agent where Master Agent is Copilot Studio

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

#### 🟦 Scenario 2 — CS Child / Connected Agent where Master Agent is Foundry

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

#### 🟧 Foundry as Child

| ✅ Pros | ❌ Cons |
|---|---|
| · Complex computation — Code Interpreter, large RAG, multi-step chains | · Opaque to parent — text-only return, no typed variable contract |
| · Foundry Flows (low-code) — visual builder for agent sub-workflows | · Max depth 2 — cannot chain further Foundry sub-agents |
| · Native Memory (preview) — cross-session without custom code | · CS → Foundry integration still in preview |
| · BYO Cosmos DB for PII-regulated outputs | · Infrastructure awareness — Standard setup requires Cosmos DB config |

#### 🏆 Recommendation

| Criterion | Inline Child Agent | Connected Agent |
|---|---|---|
| ⚡ Speed | Fastest — no boundary, no hop | Fast — team builds independently in hours |
| 🧩 No-code | ✅ CS Topics — fully no-code | ✅ CS — fully no-code |
| 📈 Scale | Tightly scoped sub-tasks only | ✅ Reusable across multiple parents, independent ALM |
| 🏭 100+ agents | Not a factory unit — embedded in parent | ✅ Factory model — one per domain function, template-based |
| 🔵 Reduced complexity | Shared transcript, shared container — zero extra infra | Own transcript (correlationId required), own ALM |
| 🔗 Agent connectivity | CS domain agents only | CS domain agents (A2A) · Foundry domain agents cannot call CS connected |

> **Use CS inline topics for tightly scoped sub-tasks within the same domain where shared context and zero-latency matter. Use CS connected agents when the sub-domain needs independent governance, team ownership, or reuse across multiple parents. Foundry agents are always in the connected tier (A2A) — never inline. Non-MSFT agents (Agentforce, ServiceNow) are connected tier only. Target ratio: 60% CS inline child, 25% CS connected, 10% Foundry connected, 5% non-MSFT connected.**
{: .recommendation}

---

### 📚 References
- [Microsoft — Copilot Studio vs Foundry](https://techcommunity.microsoft.com/blog/microsoft-security-blog/microsoft-copilot-studio-vs-microsoft-foundry-building-ai-agents-and-apps/4483160)
- [Microsoft CAF — Platform Selection](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/technology-solutions-plan-strategy)
- [Foundry — Flows and Workflows](https://learn.microsoft.com/en-us/azure/ai-foundry/agents/how-to/)
- [Microsoft Inside Track — CS or Foundry](https://www.microsoft.com/insidetrack/blog/customer-questions-answered-should-i-use-copilot-studio-or-azure-ai-foundry-to-build-my-agent/)
- [Copilot Studio — Add Other Agents](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-add-other-agents)
- [Copilot Studio — Child/Connected Agent I/O](https://microsoft.github.io/mcscatblog/posts/copilot-studio-child-connected-agents-inputs-outputs/)
- [Foundry — Multi-Agent Workflows](https://techcommunity.microsoft.com/blog/azure-ai-foundry-blog/building-a-digital-workforce-with-multi-agents-in-azure-ai-foundry-agent-service/4414671)
- [Salesforce — Agentforce A2A](https://www.salesforce.com/blog/agent-interoperability/)
{: .ref-grid}

---
