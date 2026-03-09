---
layout: default
title: "8. Final Summary"
parent: Enterprise Multi-Agent Architecture
nav_order: 9
---

## 8. 📌 Final Summary

### ✅ What to Build With

| Layer | Technology | Notes |
|---|---|---|
| 🚪 Gateway + Domain Agents | **Copilot Studio** | No-code, fastest time to agent |
| 🔧 Specialist child agents | **Copilot Studio** (inline or connected) | CS domain agents only · shared container, typed I/O |
| 🔗 Specialist connected agents | **Foundry Agent Service** (A2A) | Complex reasoning, code execution, large RAG — connected tier only |
| 🔗 Non-MSFT connected agents | **Agentforce / ServiceNow** (A2A/REST) | Connected tier only — never child agents |
| ⚡ Workflows | **Power Automate** + **Foundry Flows** | No-code, 1,400+ connectors |
| 🔌 Shared tools | **MCP servers** + **Azure APIM** | One integration per enterprise system |
| 💾 CS agent data | **Dataverse** | Transcripts (auto), metadata, admin config |
| ⚡ Foundry session state | **Redis** | Low latency userId→threadId, hot cache |
| 👤 Foundry cross-session memory | **Memory Feature** (preview) | User profiles, session summaries |
| 🗄️ Foundry full transcript + audit | **Cosmos DB** (compliance only) | CMK / data residency / LLM audit trail |
| 🔍 Large-scale RAG | **Azure AI Search** | Knowledge base > 500 docs |
| 📁 Long-term archive | **Azure Data Lake** (Synapse Link) | Compliance retention > 30 days |
| 🛡️ Governance | **Agent 365 + CoE Toolkit + Entra Agent ID** | No custom audit infra needed |

### ❌ What to Avoid

| ❌ Avoid | Why |
|---|---|
| Single orchestrator for all agents (star topology) | Unusable at 30+ agents, catastrophic at 100 |
| Cosmos DB for CS agents | Dataverse is the correct store; Cosmos DB is Foundry Standard only |
| SQL Server | No role — Dataverse covers admin data; Cosmos DB covers Foundry agent data |
| Redis for CS-only architecture | Not needed when Dataverse covers session state adequately |
| Agent Framework for CS agents | Dev complexity where no-code suffices |
| Free multi-agent peer-to-peer chaining | Least reliable pattern; orchestrator-controlled delegation only |
| Planning for known deterministic workflows | Use CS Topics / Foundry Workflows instead |
| Agent self-verification of its own output | FM-3.3: agents hallucinate success states — always use external verification |
| Building before defining termination conditions | FM-1.5: agents that do not know when to stop are the #1 failure predictor |
| Promoting tools below 90% accuracy | HuggingFace: 33-point accuracy gap; tool quality is the biggest performance lever |

### 🗓️ 100-Agent Delivery Timeline

```
Month 1–2:   Platform foundation
             ├── Dev / Test / Prod environments (per domain)
             ├── ALM pipelines (Power Platform Pipelines)
             ├── 3–5 agent templates
             │     Gateway · Domain Agent · FAQ Specialist
             │     Task Specialist · Integration Specialist
             ├── CoE Toolkit deployment
             ├── Entra Agent ID strategy
             └── Agent 365 Frontier enrollment

Month 3–4:   Pilot wave (10–15 agents)
             ├── Gateway + 2–3 domain agents
             ├── Validate architecture, verification gates,
             │   correlationId pattern, termination logic
             ├── Establish LLM-as-a-Judge evaluation baseline
             └── Validate Agent 365 telemetry

Month 5–12:  Factory mode (8–12 agents/month)
             ├── Domain teams clone templates and configure
             ├── CoE reviews each agent before Test promotion
             ├── ALM pipeline promotes Test → Prod automatically
             └── Agent 365 registry grows with each release
```

---

*Last updated: March 2026*
*Sources: Microsoft Cloud Adoption Framework · Azure Architecture Center · DeepLearning.AI Agentic Design Patterns · HuggingFace / MAST / smolagents / AssetOpsBench / mem-agent · Gartner Multiagent Systems Research · Microsoft Agent Factory Blog Series*

---
