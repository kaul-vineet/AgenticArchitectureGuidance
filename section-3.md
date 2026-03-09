---
layout: default
title: "👑 3. As Parent / Master Agent"
parent: Enterprise Multi-Agent Architecture
nav_order: 4
---

## 3. 👑 As Parent / Master Agent

---

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

#### 🤝 Agent Connectivity — What CS Parent Supports

Copilot Studio as parent supports two tiers of agent connectivity: **any Copilot Studio agent as a child agent** and **any A2A-compliant agent as a connected agent**.

**Child Agents — Any Copilot Studio Agent**

A CS parent can embed any other Copilot Studio agent as an inline child agent directly within its topic flow.

| ✅ Pros | ❌ Cons |
|---|---|
| · **Any CS agent is eligible** — any agent built in Copilot Studio can be added as a child; no special configuration required | · **CS agents only** — non-MSFT agents (Agentforce, ServiceNow) and Foundry agents cannot be child agents; connected tier only |
| · **Shared container** — child inherits parent's variables, auth context, Dataverse connections, and Power Automate connectors with zero extra config | · **Sequential invocation** — child agents are called one at a time; no parallel fan-out within the child tier |
| · **Zero-latency inline execution** — child runs in the same session and transcript as the parent; no orchestration hop | · **Session chain limits apply** — the 30 min idle / 60 min total / 100 turn cap covers the full parent + child chain |
| · **Native identity inheritance** — same Entra connection, same Dataverse row-level security across parent and child | · **Instructions cap (8,000 chars)** — child agent system prompts must stay within limit |
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

---

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

---

### 🏆 Recommendation

| Criterion | Copilot Studio as Parent | Foundry as Parent |
|---|---|---|
| ⚡ Speed | ✅ **Wins** — hours to first agent vs. days | ⚠️ Slower — portal setup + infra provisioning required |
| 🧩 No-code authoring | ✅ **Wins** — no-code native, domain teams self-serve | ⚠️ Low-code (Workflows builder) — requires more technical skill |
| 📈 Scale to 100+ agents | ✅ **Wins** — agent templates, Component Collections, PP Pipelines built for factory-scale | ⚠️ Possible but requires custom ALM; no out-of-box factory model |
| 🔵 Reduced complexity | ✅ **Wins** — single platform, Dataverse only, no extra infra | ❌ Requires Cosmos DB, Redis, Azure Functions, Azure AI Search |
| 🔗 A2A / Agent interoperability | ✅ CS supports A2A connected agents — CS, Foundry, Agentforce, ServiceNow callable as connected agents | ✅ Foundry supports A2A via Workflows; connected agents callable across platforms |
| 🌐 MSFT + non-MSFT interoperability | ✅ **Wins** — A2A protocol connects to Agentforce, ServiceNow, and any A2A-compliant agent from CS parent | ✅ A2A supported — Foundry can call non-MSFT agents; fewer native connectors than CS |
| 🛡️ Governance | ✅ **Wins** — Agent 365 + CoE Toolkit + Entra Agent ID natively in M365 Admin Center; no custom audit infra | ⚠️ Governance via Azure Policy + Entra + App Insights; no unified agent registry out of box |
| 📋 Logging & observability | ⚠️ CS transcript + Dataverse logging; limited distributed tracing across agent hops | ✅ **Wins** — full OpenTelemetry + App Insights distributed tracing across all agent-to-agent calls |
| 🔀 Orchestration at scale (100+ agents) | ⚠️ Sequential orchestration — one agent at a time; domain hierarchy required to avoid star-topology bottleneck | ✅ **Wins** — parallel fan-out/fan-in via Workflows; deterministic state machines for complex multi-agent chains |
| 🏢 LOB system integration | ✅ **Wins** — 1,400+ Power Automate connectors out of box; SAP, Salesforce, ServiceNow, Oracle, Dynamics 365, and custom APIs available with no custom code | ⚠️ Foundry connectors available but narrower library; LOB integration typically requires Azure Functions or OpenAPI tool definitions |

> **Use Copilot Studio as the parent/master agent.** CS wins on speed, no-code authoring, governance, scale, and interoperability — all critical for a 100+ agent estate. Its A2A support covers both MSFT (Foundry) and non-MSFT (Agentforce, ServiceNow) connected agents, and Agent 365 + CoE Toolkit provide enterprise-grade governance without custom infrastructure. **Introduce Foundry as parent only** for domains that explicitly require parallel orchestration, distributed tracing across a complex reasoning chain, or deterministic state-machine workflows — and accept the additional infrastructure and ALM overhead. Target fewer than 3 Foundry-parent domains out of your total agent estate.
{: .recommendation}

---

### 📚 References
- [Microsoft — Copilot Studio vs Foundry](https://techcommunity.microsoft.com/blog/microsoft-security-blog/microsoft-copilot-studio-vs-microsoft-foundry-building-ai-agents-and-apps/4483160)
- [Microsoft CAF — Platform Selection](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/technology-solutions-plan-strategy)
- [Foundry — Flows and Workflows](https://learn.microsoft.com/en-us/azure/ai-foundry/agents/how-to/)
- [Microsoft Inside Track — CS or Foundry](https://www.microsoft.com/insidetrack/blog/customer-questions-answered-should-i-use-copilot-studio-or-azure-ai-foundry-to-build-my-agent/)
- [Copilot Studio — Add Other Agents](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-add-other-agents)
- [Foundry — Multi-Agent Workflows](https://techcommunity.microsoft.com/blog/azure-ai-foundry-blog/building-a-digital-workforce-with-multi-agents-in-azure-ai-foundry-agent-service/4414671)
{: .ref-grid}

---
