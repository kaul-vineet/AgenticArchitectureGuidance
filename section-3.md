---
layout: default
title: "3. CS vs Foundry — As Parent Agent"
parent: Enterprise Multi-Agent Architecture
nav_order: 4
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
