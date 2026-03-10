---
layout: default
title: "📐 Recommendation Criteria"
parent: Enterprise Multi-Agent Architecture
nav_order: 3
---

## 📐 Recommendation Criteria

Every recommendation in this document is evaluated against 18 named criteria. Each criterion has a unique icon (see [Design Requirements / Priorities](../section-req) for the icon audit that resolved duplicates in the original set). The **Priority** column distinguishes must-have requirements (P1 🥇) from important-but-not-blocking ones (P2 🥈); P0 💎 marks foundational platform criteria evaluated before any architecture decision.

---

### **Priority Key**

| Badge | Meaning |
|---|---|
| `P0 💎` | Foundational — platform cannot be recommended without satisfying this |
| `P1 🥇` | Must-have — architecture fails if this is not met |
| `P2 🥈` | Important — gaps here require a mitigation plan |

---

### **Full Criteria Table**

| Icon | Priority | Criterion | Meaning |
|---|---|---|---|
| ⚡ | P0 💎 |**High Speed of Development** | First agent ships in days, not weeks. A platform that requires weeks of infrastructure setup before any agent can be built does not qualify for this architecture. |
| 🧩 | P0 💎 |**No-Code Over Custom Features** | Platform-native features are accepted at 80% coverage rather than writing custom code for 100%. Every custom line is a maintenance liability at agent #100. |
| 📈 | P0 💎 |**Scale Over Custom Dev** | The architecture that works for agent #1 works unchanged for agent #100. No re-architecting, no platform migration, no new infrastructure tier. |
| 🏭 | P0 💎 |**100+ Agents in 6–12 Months** | The platform supports an agent factory: standard templates, ALM pipelines, domain reuse, and no-code onboarding for domain teams. Delivery rate: 8–12 agents per month from month 5 onward. |
| 🔵 | P0 💎 |**Reduced Complexity** | Fewest moving parts, fewest distinct technologies, fewest custom servers. Complexity is measured at agent #100, not agent #1. |
| 🔗 | P1 🥇 | **A2A / Agent Interoperability** | Any agent can call any other agent via A2A protocol or equivalent — not limited to same-platform agents. Gateway → Domain → Specialist calls are all supported. |
| 🌐 | P1 🥇 | **MSFT + Non-MSFT Interoperability** | Architecture integrates Copilot Studio, Azure AI Foundry, Agentforce, ServiceNow, and custom agents under one orchestration layer. Platform choice per agent does not break the hierarchy. |
| 🛡️ | P1 🥇 | **Enterprise Governance** | Agent inventory, access control, policy enforcement, and audit trail without custom-built infrastructure. Agent 365 + Purview + Defender + CoE Toolkit are the governance stack. |
| 📋 | P1 🥇 | **Logging & Observability** | A single correlationId reconstructs every agent hop, tool call, and handoff from gateway to specialist. Transcript-only logging does not qualify. |
| 🔀 | P1 🥇 | **Orchestration at Scale** | Routes and manages 100+ agents across domains without a single-orchestrator bottleneck. Domain hierarchy eliminates the star topology accuracy collapse documented at 30+ agents. |
| 🏢 | P1 🥇 | **LOB System Integration** | Connects to SAP, Salesforce, ServiceNow, Dynamics 365, and Oracle with minimal custom code. Power Automate connectors and Azure APIM are the integration layer. |
| 📱 | P1 🥇 | **Multi-Channel Exposure** | Agents reachable across web, mobile, Teams, SharePoint, M365 Copilot, and custom surfaces without rebuilding per channel. |
| 📝 | P1 🥇 | **Complex Agent Instructions** | Each agent supports rich, detailed system prompts with multi-step conditional logic. Instruction size or expressiveness is not a platform constraint. |
| 👑 | P1 🥇 | **Supervisor Pattern** | A central supervisor agent routes tasks to specialist sub-agents, evaluates their outputs, and decides next steps — without hardcoded routing logic. |
| 🔧 | P1 🥇 | **Supervisor-as-Tool Pattern** | The supervisor is callable as a tool by a higher-level orchestrator — enabling composable multi-level delegation where any agent can be both orchestrator and sub-agent. |
| 🏛️ | P1 🥇 | **Hierarchical Orchestration Pattern** | Architecture supports multi-tier hierarchies (Gateway → Domain → Specialist) where each layer independently manages its own sub-agents without sharing a global context. |
| ⚙️ | P2 🥈 | **Custom Orchestration Pattern** | Engineering-owned domains can implement fully custom orchestration logic (SDK-level code, state machines, conditional branching) when declarative platform patterns are insufficient. |
| 🕸️ | P2 🥈 | **Network / Mesh Pattern** | Architecture supports peer-to-peer agent collaboration where agents can call each other laterally — not only top-down — with guardrails to prevent unbounded recursion. |

---

### **Criteria by Priority Group**

#### P0 💎 — Foundational (5 criteria)

These five criteria are evaluated first. A platform that cannot satisfy all five is eliminated regardless of its score on P1/P2 criteria.

| Icon | Criterion | Minimum Bar |
|---|---|---|
| ⚡ | High Speed of Development | First agent live within 1 sprint (2 weeks) |
| 🧩 | No-Code Over Custom Features | >70% of agent capability delivered via platform-native tooling |
| 📈 | Scale Over Custom Dev | Same architecture at agent #1 and agent #100 |
| 🏭 | 100+ Agents in 6–12 Months | Template-driven onboarding; no bespoke infrastructure per domain |
| 🔵 | Reduced Complexity | Fewer than 5 distinct technology components in the core stack |

#### P1 🥇 — Must-Have (12 criteria)

These twelve criteria are non-negotiable for production readiness. A platform that scores below threshold on any P1 criterion requires a documented mitigation before go-live.

| Icon | Criterion |
|---|---|
| 🔗 | A2A / Agent Interoperability |
| 🌐 | MSFT + Non-MSFT Interoperability |
| 🛡️ | Enterprise Governance |
| 📋 | Logging & Observability |
| 🔀 | Orchestration at Scale |
| 🏢 | LOB System Integration |
| 📱 | Multi-Channel Exposure |
| 📝 | Complex Agent Instructions |
| 👑 | Supervisor Pattern |
| 🔧 | Supervisor-as-Tool Pattern |
| 🏛️ | Hierarchical Orchestration Pattern |

#### P2 🥈 — Important (2 criteria)

These patterns are valuable and supported by the recommended architecture, but gaps require a mitigation plan rather than blocking go-live.

| Icon | Criterion |
|---|---|
| ⚙️ | Custom Orchestration Pattern |
| 🕸️ | Network / Mesh Pattern |

---

### 📚 References

- [Microsoft CAF — AI Agents](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/)
- [Azure Architecture Center — AI Agent Orchestration Patterns](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns)
- [DeepLearning.AI — Agentic Design Patterns](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/)
- [MAST — Why Enterprise Agents Fail](https://huggingface.co/blog/ibm-research/itbenchandmast)
- [Gartner — Multiagent Systems](https://www.gartner.com/en/articles/multiagent-systems)
{: .ref-grid}
