---
layout: default
title: "📊 13. Platform Scoring"
parent: Enterprise Multi-Agent Architecture
nav_order: 15
---

## 📊 Platform Recommendation Scoring

All 21 criteria evaluated across three deployment scenarios using the **Asymmetric Priority-Weighted (APW)** method. Session management and infrastructure requirements are factored into every score.

**Adherence symbols:** ✅ Full · 🔶 Partial · ❌ None

**Score range:** +35.0 (max) to −49.0 (min) · ⚠️ Gate: any P0 = None triggers **Conditional** flag regardless of total score.

---

## Part A — As Parent / Master Agent

*Evaluates Copilot Studio and Azure AI Foundry as the top-level orchestrator: the agent that receives every user request, routes to domain and specialist agents, and owns session context end-to-end.*

### Scoring Table

| Icon | Pri | Criterion | 🟦 CS as Parent | CS Score | 🟧 Foundry as Parent | Foundry Score |
|---|---|---|---|---|---|---|
| ⚡ | P0 | **High Speed of Development** | ✅ Full — hours to first agent; agent templates + PP Pipelines out of box; no infra setup | **+2.0** | 🔶 Partial — Workflow YAML + CosmosDB + Redis + Azure Functions provisioning required before first agent | **+0.75** |
| 🧩 | P0 | **No-Code Over Custom Features** | ✅ Full — fully no-code; Topics, Actions, Knowledge, PA connectors; domain teams self-serve | **+2.0** | 🔶 Partial — portal visual builder for Sequential/Handoff/Group Chat; Concurrent and Magentic require SDK code | **+0.75** |
| 📈 | P0 | **Architectural Stability at Scale** | ✅ Full — same CS topology at agent #100 as agent #1; agent templates, Component Collections, PP Pipelines | **+2.0** | 🔶 Partial — architecture stable but custom ALM pipelines required; deprecated Classic Connected Agents adds migration risk | **+0.75** |
| 🏭 | P0 | **100+ Agents in 6–12 Months** | ✅ Full — clone-and-configure template model; PP Pipelines; domain self-service; 8–12 agents/month achievable | **+2.0** | 🔶 Partial — scalable but no out-of-box factory model; engineering bottleneck per domain; no template factory equivalent | **+0.75** |
| 🔵 | P0 | **Reduced Complexity** | ✅ Full — Dataverse only; no Redis, CosmosDB, or custom servers; same simplicity at agent #100 | **+2.0** | ❌ None — CosmosDB + Redis + Azure Functions + Azure AI Search required; Classic Agents deprecated (migration overhead) | **−3.0** |
| 🔗 | P0 | **A2A / Agent Interoperability** | 🔶 Partial — A2A connected agents GA for CS/Foundry/Agentforce/ServiceNow; CS→Foundry A2A in preview; no native MCP invocation | **+0.75** | ✅ Full — A2A Tool GA (Python/JS); OpenAPI tool; MCP servers integrable; calls any A2A endpoint across orgs and clouds | **+2.0** |
| 🌐 | P0 | **MSFT + Non-MSFT Interoperability** | ✅ Full — A2A connects CS, Foundry (preview), Agentforce, ServiceNow, custom; 1,400+ PA connectors for LOB | **+2.0** | 🔶 Partial — A2A Tool GA for cross-platform; narrower native connector library; OpenAPI covers remainder | **+0.75** |
| 🛡️ | P0 | **Enterprise Governance** | ✅ Full — Agent 365 + CoE Toolkit + Entra Agent ID native in M365 Admin Center; agent inventory, access control, policy, audit trail zero-config | **+2.0** | ❌ None — no unified agent registry; governance requires custom build: Azure Policy + Entra + App Insights; no CoE Toolkit equivalent | **−3.0** |
| 📋 | P0 | **Logging & Observability** | 🔶 Partial — CS transcript + Dataverse auto; correlationId pattern available; limited distributed tracing across A2A hops | **+0.75** | ✅ Full — full OpenTelemetry + App Insights; every agent hop, tool call, and handoff traceable end-to-end; correlationId propagates automatically | **+2.0** |
| 🔀 | P0 | **Orchestration at Scale** | 🔶 Partial — sequential only; domain hierarchy mitigates star-topology collapse; no native parallel fan-out | **+0.75** | ✅ Full — Concurrent fan-out, Magentic supervisor, Group Chat, Handoff, Human-in-the-loop all GA | **+2.0** |
| 🏢 | P0 | **LOB System Integration** | ✅ Full — 1,400+ PA connectors; SAP, Salesforce, Dynamics 365, Oracle, ServiceNow with zero custom code | **+2.0** | 🔶 Partial — Foundry connectors available; LOB typically requires Azure Functions or OpenAPI tool definitions | **+0.75** |
| 📱 | P0 | **Multi-Channel Exposure** | ✅ Full — Teams, SharePoint, M365 Copilot, web, mobile, Direct Line zero-config | **+2.0** | ❌ None — API endpoint only; no native channel support; requires CS wrapper or custom front-end for any channel | **−3.0** |
| 📝 | P0 | **Complex Agent Instructions** | 🔶 Partial — 8,000 char hard cap; complex domain prompts require compression | **+0.75** | ✅ Full — no system prompt size limit; full parameter control (temperature, top-p, max tokens) | **+2.0** |
| 👑 | P0 | **Supervisor Pattern** | ✅ Full — CS Topics act as generative supervisor routing to specialist agents; explicit or generative routing logic | **+2.0** | ✅ Full — Magentic pattern: dynamic specialist selection, stall detection, replanning, optional HITL | **+2.0** |
| 🧪 | P0 | **Evaluation & Quality Gates** | 🔶 Partial — CS Analytics (CSAT, escalation rate, session volume) available; no built-in groundedness/relevance/coherence eval; App Insights for custom metrics | **+0.75** | ✅ Full — Foundry Evaluations GA: groundedness, relevance, coherence, fluency, safety, jailbreak; regression gates in portal | **+2.0** |
| 📚 | P0 | **Grounding & Knowledge Integration** | ✅ Full — SharePoint, Dataverse, websites, uploaded files, Azure AI Search as knowledge sources; generative answers built in; zero custom retrieval code | **+2.0** | ✅ Full — Azure AI Search, File Search, Code Interpreter, CosmosDB; Foundry Standard setup includes AI Search natively | **+2.0** |
| 💾 | P1 | **Session Continuity** | 🔶 Partial — 30 min idle / 60 min total / 100 turns limits; cross-session via Dataverse PA flow (CS-heavy) or CosmosDB (Foundry-heavy); cross-channel requires explicit state design | **+0.50** | ✅ Full — thread-based, no time limit, 100k messages per thread; Redis for userId→threadId sub-ms lookup; CosmosDB for cross-session profiles; Foundry Memory Feature (preview) | **+1.0** |
| 🔧 | P2 | **Supervisor-as-Tool Pattern** | ✅ Full — CS published as A2A endpoint; callable by Foundry or non-MSFT orchestrators as a tool | **+0.50** | ✅ Full — Foundry agent callable as A2A tool; parent retains full control across delegation | **+0.50** |
| 🏛️ | P2 | **Hierarchical Orchestration Pattern** | ✅ Full — Gateway → Domain → Specialist fully supported; each tier is independent CS agent with own ALM | **+0.50** | ✅ Full — Workflow nodes chain into multi-tier hierarchies; no depth limit in Workflows (unlike deprecated Classic limit of 2) | **+0.50** |
| ⚙️ | P2 | **Custom Orchestration Pattern** | ❌ None — Topics only; no SDK access; no custom state machines or conditional branching | **0** | ✅ Full — full Python, C#, JS, Java SDK; custom state machines, conditional branching, replay logic | **+0.50** |
| 🕸️ | P2 | **Network / Mesh Pattern** | 🔶 Partial — possible via A2A connected agents but always orchestrator-controlled; no true peer-to-peer routing | **+0.25** | 🔶 Partial — Group Chat supports multi-agent lateral collaboration; pure peer-to-peer not recommended (36% hallucination rate per AgentArch benchmark) | **+0.25** |

### Score Summary — As Parent

| | 🟦 Copilot Studio | 🟧 Azure AI Foundry |
|---|---|---|
| **P0 score** (16 criteria · max +32 · min −48) | **+25.75** | **+9.50** |
| **P1 score** (1 criterion · max +1 · min −1) | **+0.50** | **+1.0** |
| **P2 score** (4 criteria · max +2 · min 0) | **+1.25** | **+1.75** |
| **Total** | **+27.50** | **+12.25** |
| **% of max (35)** | **79%** | **35%** |
| **Rating** | ✅ **Recommended** | 🟡 **Acceptable** |
| **P0 = None violations** | None | 🔵 Reduced Complexity · 🛡️ Enterprise Governance · 📱 Multi-Channel Exposure |
| **Gate flag** | — | ⚠️ **Conditional** — 3 P0 gaps require approved mitigation plans |

> **Use Copilot Studio as the parent/master agent.** CS scores 79% of maximum (+27.50) with zero gate violations. It wins on speed, no-code authoring, governance, LOB integration, multi-channel exposure, and factory-scale ALM. All are P0 foundational criteria. The only CS gaps — A2A preview limitations, sequential orchestration, 8,000 char instruction cap, and limited distributed tracing — have documented mitigations in the architecture. Introduce Foundry as parent only for domains explicitly requiring parallel fan-out (Concurrent), dynamic supervisor with stall detection (Magentic), system prompts beyond 8,000 chars, or compliance-grade BYO CosmosDB. Accept the three P0 mitigation plans required for Foundry-parent domains: channels via CS wrapper, governance via custom Azure Policy + Entra build, and complexity managed via dedicated infrastructure team.
{: .recommendation}

---

## Part B — As Child / Connected Agent (CS as Parent)

*Evaluates two sub-agent options when the parent orchestrator is Copilot Studio: (1) a CS child agent (inline or connected), and (2) a Foundry agent connected to CS (preview).*

### Scoring Table

| Icon | Pri | Criterion | 🟦 CS Child — CS Parent | CS Score | 🟧 Foundry Connected — CS Parent | Foundry Score |
|---|---|---|---|---|---|---|
| ⚡ | P0 | **High Speed of Development** | ✅ Full — hours to build; no new platform; clone existing CS agent templates | **+2.0** | 🔶 Partial — Foundry portal setup + agent publish required before CS wizard connection | **+0.75** |
| 🧩 | P0 | **No-Code Over Custom Features** | ✅ Full — fully no-code Topics; inline child shares parent container; no config | **+2.0** | 🔶 Partial — CS wizard is no-code to add; Foundry agent setup requires portal work and tools may need Azure Functions | **+0.75** |
| 📈 | P0 | **Architectural Stability at Scale** | ✅ Full — same architecture at agent #100; unified ALM; GA; no preview risk | **+2.0** | 🔶 Partial — CS→Foundry integration is public preview; subject to breaking changes; new Foundry portal required (legacy returns 404) | **+0.75** |
| 🏭 | P0 | **100+ Agents in 6–12 Months** | ✅ Full — clone-and-configure template model; PP Pipelines includes child agents; GA production support | **+2.0** | 🔶 Partial — each Foundry connected agent requires separate engineering work; preview status blocks production factory model | **+0.75** |
| 🔵 | P0 | **Reduced Complexity** | ✅ Full — shared container (inline); zero extra infra; same ALM lifecycle as parent | **+2.0** | ❌ None — separate session context; CS strips grounding references; double LLM hop (CS + Foundry both reason); citation gap; black-box CS orchestration; Foundry infra overhead | **−3.0** |
| 🔗 | P0 | **A2A / Agent Interoperability** | ✅ Full — CS child reusable across CS parents via A2A (connected agent); GA | **+2.0** | 🔶 Partial — uses internal CS→Foundry connector, not open A2A protocol; not reusable as A2A endpoint from other platforms | **+0.75** |
| 🌐 | P0 | **MSFT + Non-MSFT Interoperability** | ✅ Full — CS child inherits 1,400+ PA connectors, Dataverse, M365 channels from parent context | **+2.0** | 🔶 Partial — Foundry child handles complex reasoning; CS parent handles LOB connectors and M365 channels; LOB in child requires Azure Functions | **+0.75** |
| 🛡️ | P0 | **Enterprise Governance** | ✅ Full — Agent 365 + CoE Toolkit covers both parent and child in unified M365 Admin Center view | **+2.0** | 🔶 Partial — split governance: CS (Agent 365) + Foundry (App Insights); no unified agent registry covering both; responsible use burden on implementer | **+0.75** |
| 📋 | P0 | **Logging & Observability** | ✅ Full — unified CS transcript for inline child; correlationId links connected child sessions; single pane of glass | **+2.0** | 🔶 Partial — split: CS orchestration (black box) + Foundry App Insights; no end-to-end correlationId trace across both platforms | **+0.75** |
| 🔀 | P0 | **Orchestration at Scale** | 🔶 Partial — sequential invocation only; one child at a time; domain hierarchy mitigates scale issues but no parallel fan-out | **+0.75** | 🔶 Partial — CS calls connected agents sequentially regardless of Foundry's internal capabilities; bottleneck is the CS orchestration layer | **+0.75** |
| 🏢 | P0 | **LOB System Integration** | ✅ Full — 1,400+ PA connectors; Dataverse native; no custom code for SAP, Salesforce, Dynamics 365, Oracle | **+2.0** | 🔶 Partial — Foundry child handles reasoning tasks; LOB connectors remain in CS parent; Foundry child LOB requires Azure Functions | **+0.75** |
| 📱 | P0 | **Multi-Channel Exposure** | ✅ Full — channels managed by CS parent; child agent exposed through all CS channels automatically | **+2.0** | ✅ Full — CS parent handles all channel routing; Foundry child handles reasoning; no channel gap from end-user perspective | **+2.0** |
| 📝 | P0 | **Complex Agent Instructions** | 🔶 Partial — child agent system prompt capped at 8,000 chars same as parent; limits child agent specialisation depth | **+0.75** | ✅ Full — no system prompt size limit on Foundry side; full parameter control over Foundry agent behaviour | **+2.0** |
| 👑 | P0 | **Supervisor Pattern** | ✅ Full — CS parent acts as supervisor over CS child agents; generative or explicit routing logic | **+2.0** | 🔶 Partial — CS parent delegates to Foundry for specialist tasks but black-box CS orchestration limits predictable supervisor control; Foundry cannot initiate back | **+0.75** |
| 🧪 | P0 | **Evaluation & Quality Gates** | 🔶 Partial — CS Analytics available at parent level; no built-in groundedness eval for child outputs; App Insights for custom quality metrics | **+0.75** | 🔶 Partial — Foundry Evaluations available for Foundry-side quality; no unified evaluation gate spanning CS orchestration layer + Foundry response | **+0.75** |
| 📚 | P0 | **Grounding & Knowledge Integration** | ✅ Full — SharePoint, Dataverse, websites, uploaded files as knowledge sources in child; generative answers built in | **+2.0** | 🔶 Partial — Foundry has Azure AI Search, File Search; but CS orchestrator strips SharePoint links and document references before returning to user (documented Microsoft limitation) | **+0.75** |
| 💾 | P1 | **Session Continuity** | 🔶 Partial — 30/60/100 limits apply to full parent+child chain; cross-session via Dataverse PA flow; inline child shares parent session; connected child requires correlationId | **+0.50** | 🔶 Partial — separate session context for Foundry agent; CS chain limits still govern; correlationId required to link sessions; no native cross-session continuity spanning both platforms | **+0.50** |
| 🔧 | P2 | **Supervisor-as-Tool Pattern** | ✅ Full — CS child published as A2A endpoint; reusable as connected agent by multiple CS parents or external orchestrators | **+0.50** | 🔶 Partial — CS agent with Foundry connected agents cannot itself be used as a connected agent to another CS parent (documented restriction) | **+0.25** |
| 🏛️ | P2 | **Hierarchical Orchestration Pattern** | ✅ Full — multi-tier CS hierarchy; each tier independent with own ALM; unlimited depth | **+0.50** | 🔶 Partial — CS → Foundry is max 2 effective hops from CS perspective; Foundry cannot delegate back up hierarchy to CS | **+0.25** |
| ⚙️ | P2 | **Custom Orchestration Pattern** | ❌ None — CS Topics only; no SDK access; no custom state machines | **0** | ✅ Full — Foundry agent has full SDK; custom state machines, conditional branching, structured outputs | **+0.50** |
| 🕸️ | P2 | **Network / Mesh Pattern** | 🔶 Partial — via A2A connected agents but always CS-orchestrator-controlled; no true peer-to-peer | **+0.25** | ❌ None — CS orchestration is unidirectional; Foundry connected agent cannot initiate lateral calls to other CS agents | **0** |

### Score Summary — As Child (CS Parent)

| | 🟦 CS Child | 🟧 Foundry Connected |
|---|---|---|
| **P0 score** (16 criteria · max +32 · min −48) | **+28.25** | **+10.75** |
| **P1 score** (1 criterion · max +1 · min −1) | **+0.50** | **+0.50** |
| **P2 score** (4 criteria · max +2 · min 0) | **+1.25** | **+1.00** |
| **Total** | **+30.00** | **+12.25** |
| **% of max (35)** | **86%** | **35%** |
| **Rating** | ✅ **Strongly Recommended** | 🟡 **Acceptable** |
| **P0 = None violations** | None | 🔵 Reduced Complexity |
| **Gate flag** | — | ⚠️ **Conditional** — double LLM hop, citation gap, black-box orchestration, and preview status require documented mitigation before production use |

> **When your parent is CS, use CS child agents as the default.** CS child scores 86% (+30.00) — Strongly Recommended with zero gate violations. It is GA, zero-config, governance-native, and factory-ready. Add Foundry as a connected agent only when the specific task exceeds CS capability: complex reasoning beyond CS model limits, Code Interpreter, large-scale Azure AI Search RAG, or system prompts that cannot fit in 8,000 chars. The Foundry connected agent scores 35% (+12.25) and carries a Conditional gate flag. Its single P0 gap (Reduced Complexity = None) requires a documented mitigation covering: the double LLM hop latency, citation propagation failure, CS stripping grounding references, and black-box CS orchestration behaviour. Do not use Foundry as a CS connected agent for tasks CS can handle.
{: .recommendation}

---

## Part C — As Child / Connected Agent (Foundry as Parent)

*Evaluates two sub-agent options when the parent orchestrator is Azure AI Foundry: (1) a CS agent called via Direct Line REST workaround, and (2) a Foundry child agent via Foundry Workflows.*

> ⚠️ **The CS child via Direct Line is not an officially supported enterprise integration pattern.** It is evaluated here to document why it should not be used for new builds. The score reflects architectural reality.

### Scoring Table

| Icon | Pri | Criterion | 🟦 CS Child — Foundry Parent *(workaround)* | CS Score | 🟧 Foundry Child — Foundry Parent | Foundry Score |
|---|---|---|---|---|---|---|
| ⚡ | P0 | **High Speed of Development** | 🔶 Partial — CS authoring is fast; Direct Line setup on Foundry side adds significant integration time and custom REST code | **+0.75** | 🔶 Partial — Foundry Workflow node setup required per child agent; moderate setup time per agent | **+0.75** |
| 🧩 | P0 | **No-Code Over Custom Features** | 🔶 Partial — CS child is no-code; all integration complexity sits on the Foundry side as custom REST/Direct Line code | **+0.75** | 🔶 Partial — Workflows portal for Sequential, Handoff, Group Chat; Concurrent and Magentic require SDK code | **+0.75** |
| 📈 | P0 | **Architectural Stability at Scale** | ❌ None — unsupported pattern; subject to breaking changes at any Direct Line API update; not recommended for new builds; cannot be part of a stable architecture at agent #100 | **−3.0** | 🔶 Partial — stable Foundry Workflow architecture; multi-agent Workflows in preview; Classic Connected Agents deprecated (migration risk) | **+0.75** |
| 🏭 | P0 | **100+ Agents in 6–12 Months** | ❌ None — unsupported pattern; cannot be included in a factory model; each integration is bespoke; no template reuse possible | **−3.0** | 🔶 Partial — independent ALM per child agent; custom ALM pipelines required; no out-of-box factory model; preview Workflows adds risk | **+0.75** |
| 🔵 | P0 | **Reduced Complexity** | ❌ None — two separate ALM pipelines (PP Pipelines + Azure DevOps/GitHub); split tracing (App Insights + Dataverse/CoE); Direct Line secret credential management; text-only return from CS; manual correlationId injection | **−3.0** | 🔶 Partial — infrastructure overhead (CosmosDB, Azure Functions, Redis); per-agent thread management required; but single unified platform | **+0.75** |
| 🔗 | P0 | **A2A / Agent Interoperability** | ❌ None — not natively supported; Direct Line is a proprietary REST workaround, not A2A protocol; no A2A discovery, capability negotiation, or protocol compliance | **−3.0** | ✅ Full — Workflow Nodes (same project, GA); A2A Tool (cross-project, GA); OpenAPI Tool; full protocol compliance | **+2.0** |
| 🌐 | P0 | **MSFT + Non-MSFT Interoperability** | 🔶 Partial — CS child brings 1,400+ PA connectors and Dataverse into a Foundry-orchestrated system; M365 channel capability; but workaround pattern | **+0.75** | ✅ Full — A2A Tool connects to any A2A endpoint across orgs and clouds; OpenAPI for REST; no platform boundary restrictions | **+2.0** |
| 🛡️ | P0 | **Enterprise Governance** | ❌ None — no unified trace across CS and Foundry; CS governance (Agent 365) entirely separate from Foundry (App Insights); no unified agent registry; split audit trail; responsible use burden on implementer | **−3.0** | 🔶 Partial — Managed Identity per agent; RBAC; full App Insights audit trail; but no Agent 365 equivalent for Foundry registry; governance is custom-built | **+0.75** |
| 📋 | P0 | **Logging & Observability** | ❌ None — split traces across App Insights (Foundry) and Dataverse/CoE (CS); no end-to-end correlationId trace possible; distributed debugging across two tooling stacks | **−3.0** | ✅ Full — full OpenTelemetry + App Insights; every hop, tool call, and handoff traceable; correlationId propagates automatically through Workflow nodes | **+2.0** |
| 🔀 | P0 | **Orchestration at Scale** | ❌ None — sequential REST calls only; no parallel fan-out; not designed for orchestration-at-scale; a CS agent via Direct Line cannot participate in Concurrent or Magentic patterns | **−3.0** | ✅ Full — Concurrent fan-out; Magentic supervisor; Group Chat; Handoff; all GA or near-GA in Workflows | **+2.0** |
| 🏢 | P0 | **LOB System Integration** | ✅ Full — 1,400+ PA connectors; Dataverse native; M365 channels; best LOB option available in a Foundry-parent system | **+2.0** | 🔶 Partial — LOB via Azure Functions or OpenAPI tool definitions; narrower connector library than CS | **+0.75** |
| 📱 | P0 | **Multi-Channel Exposure** | 🔶 Partial — CS child carries M365 channel capability into a Foundry system but via unsupported workaround; Foundry parent still has no native channel; not a sustainable channel architecture | **+0.75** | ❌ None — Foundry child is API-only; no native channel support; parent Foundry agent also has no channels; requires CS wrapper at the gateway tier for any channel exposure | **−3.0** |
| 📝 | P0 | **Complex Agent Instructions** | 🔶 Partial — CS child capped at 8,000 chars; limits specialist agent complexity | **+0.75** | ✅ Full — no system prompt size limit per Foundry child agent; full parameter control | **+2.0** |
| 👑 | P0 | **Supervisor Pattern** | ❌ None — no supervisor capability on the CS child side; Foundry orchestrates one synchronous REST call; CS cannot dynamically route or evaluate outputs for supervisor decisions | **−3.0** | ✅ Full — Magentic pattern: dynamic specialist selection, stall detection, replanning, optional HITL per Workflow node | **+2.0** |
| 🧪 | P0 | **Evaluation & Quality Gates** | ❌ None — split evaluation; CS Analytics not accessible from Foundry; Foundry cannot evaluate CS agent outputs with its built-in evaluation framework; no unified quality gate | **−3.0** | ✅ Full — Foundry Evaluations (groundedness, relevance, coherence, fluency, safety) applied per child agent; regression gates in Foundry portal | **+2.0** |
| 📚 | P0 | **Grounding & Knowledge Integration** | 🔶 Partial — CS child can use SharePoint, Dataverse, uploaded files as knowledge; results return as plain text to Foundry with no structured grounding context or citations | **+0.75** | ✅ Full — Azure AI Search, File Search, Code Interpreter per child agent; structured JSON output with citations within Foundry Workflow | **+2.0** |
| 💾 | P1 | **Session Continuity** | ❌ None — fully separate sessions; CS session and Foundry thread are entirely independent; manual correlationId injection required; no native cross-session continuity across platforms; Direct Line maintains no persistent state | **−1.0** | ✅ Full — each Foundry child has its own thread; Redis for session state; CosmosDB for cross-session profiles (Foundry-heavy lean); explicit state design required but architecture fully supports it | **+1.0** |
| 🔧 | P2 | **Supervisor-as-Tool Pattern** | ❌ None — CS child returns plain text only via Direct Line; cannot act as a structured tool in Foundry's tool-calling framework | **0** | ✅ Full — Foundry child callable as A2A tool; parent retains full delegation control | **+0.50** |
| 🏛️ | P2 | **Hierarchical Orchestration Pattern** | ❌ None — workaround pattern not suitable for hierarchical orchestration; single synchronous REST call only; no depth | **0** | ✅ Full — Workflow nodes chain into multi-tier hierarchies; no depth limit | **+0.50** |
| ⚙️ | P2 | **Custom Orchestration Pattern** | ❌ None — CS child has no custom orchestration; text-only return; no structured state | **0** | ✅ Full — full Python/C#/JS/Java SDK per child; custom state machines; conditional branching | **+0.50** |
| 🕸️ | P2 | **Network / Mesh Pattern** | ❌ None — CS child cannot initiate calls to other agents from Foundry context; no lateral communication | **0** | 🔶 Partial — Group Chat supports lateral multi-agent collaboration; pure peer-to-peer not recommended (hallucination risk) | **+0.25** |

### Score Summary — As Child (Foundry Parent)

| | 🟦 CS Child *(workaround)* | 🟧 Foundry Child |
|---|---|---|
| **P0 score** (16 criteria · max +32 · min −48) | **−20.50** | **+18.25** |
| **P1 score** (1 criterion · max +1 · min −1) | **−1.0** | **+1.0** |
| **P2 score** (4 criteria · max +2 · min 0) | **0** | **+1.75** |
| **Total** | **−21.50** | **+21.00** |
| **% of max (35)** | **−** | **60%** |
| **Rating** | ❌ **Disqualified** | ✅ **Recommended** |
| **P0 = None violations** | 📈 Scale · 🏭 100+ Agents · 🔵 Complexity · 🔗 A2A · 🛡️ Governance · 📋 Observability · 🔀 Orchestration · 👑 Supervisor · 🧪 Evaluation | 📱 Multi-Channel Exposure |
| **Gate flag** | ❌ **Disqualified** — 9 P0 = None; structural gaps cannot be mitigated | ⚠️ **Conditional** — Multi-Channel gap is a parent-tier concern; requires CS wrapper at gateway tier |

> **When your parent is Foundry, use Foundry-to-Foundry child agents via Workflows.** Foundry child scores 60% (+21.00) — Recommended — and is the only viable path in a Foundry-parent domain. It delivers full observability, parallel execution, Magentic supervisor, structured JSON output, and independent governance per agent. The one Conditional gate (Multi-Channel Exposure = None) is a parent-tier concern: address it by placing a CS agent as the gateway-tier entry point that routes to Foundry domains, rather than exposing Foundry directly to users across channels. The CS child via Direct Line is **Disqualified** (−21.50) with 9 P0 = None violations. Use CS child agents via Direct Line only as a transitional workaround for existing CS agents that cannot be rebuilt in Foundry — classify as technical debt and plan migration when first-party Foundry → CS A2A becomes GA. **Never use the CS Direct Line workaround for new agent builds.**
{: .recommendation}

---

## Consolidated Score Summary

| Scenario | Platform | Score | Rating | Gate |
|---|---|---|---|---|
| **Parent / Master Agent** | 🟦 Copilot Studio | **+27.50 / 79%** | ✅ Recommended | — |
| **Parent / Master Agent** | 🟧 Azure AI Foundry | **+12.25 / 35%** | 🟡 Acceptable | ⚠️ Conditional (3 P0 gaps) |
| **Child — CS Parent** | 🟦 CS Child | **+30.00 / 86%** | ✅ Strongly Recommended | — |
| **Child — CS Parent** | 🟧 Foundry Connected | **+12.25 / 35%** | 🟡 Acceptable | ⚠️ Conditional (1 P0 gap) |
| **Child — Foundry Parent** | 🟦 CS Child *(workaround)* | **−21.50** | ❌ Disqualified | ❌ 9 P0 = None |
| **Child — Foundry Parent** | 🟧 Foundry Child | **+21.00 / 60%** | ✅ Recommended | ⚠️ Conditional (channel gap at parent tier) |

---

### 📚 References

- [Microsoft CAF — AI Agents](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/)
- [Foundry — Workflow Orchestration Patterns](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/workflow)
- [Foundry — A2A Tool](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/tools/agent-to-agent)
- [Copilot Studio — Add Other Agents](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-add-other-agents)
- [Copilot Studio — Connect to a Foundry Agent (preview)](https://learn.microsoft.com/en-us/microsoft-copilot-studio/add-agent-foundry-agent)
- [AgentArch Benchmark — Multi-Agent Architectures (arXiv 2509.10769)](https://arxiv.org/abs/2509.10769)
- [MAST — Why Enterprise Agents Fail](https://huggingface.co/blog/ibm-research/itbenchandmast)
{: .ref-grid}

---
