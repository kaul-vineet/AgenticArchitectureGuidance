---
layout: default
title: "🔗 4. CS vs Foundry — As Child Agent"
parent: Enterprise Multi-Agent Architecture
nav_order: 5
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
- [Copilot Studio — Add Other Agents](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-add-other-agents)
- [Copilot Studio — Child/Connected Agent I/O](https://microsoft.github.io/mcscatblog/posts/copilot-studio-child-connected-agents-inputs-outputs/)
- [Foundry — Multi-Agent Workflows](https://techcommunity.microsoft.com/blog/azure-ai-foundry-blog/building-a-digital-workforce-with-multi-agents-in-azure-ai-foundry-agent-service/4414671)
- [Salesforce — Agentforce A2A](https://www.salesforce.com/blog/agent-interoperability/)
{: .ref-grid}

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
