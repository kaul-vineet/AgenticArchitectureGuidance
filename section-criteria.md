---
layout: default
title: "📐 1. Recommendation Criteria"
parent: Enterprise Multi-Agent Architecture
nav_order: 3
---

## 📐 Recommendation Criteria

Every recommendation in this document is evaluated against 18 named criteria. Each criterion has a unique icon. The **Priority** column classifies criteria as foundational (P0 💎) — platform cannot be recommended without satisfying these — or important-but-not-blocking (P2 🥈). There are no P1 criteria: **14 criteria are P0** and **4 are P2**.

---

### **Priority Key**

| Badge | Meaning |
|---|---|
| `P0 💎` | Foundational — platform cannot be recommended without satisfying this |
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
| 🔗 | P0 💎 | **A2A / Agent Interoperability** | Any agent can call any other agent via A2A protocol or equivalent — not limited to same-platform agents. Gateway → Domain → Specialist calls are all supported. |
| 🌐 | P0 💎 | **MSFT + Non-MSFT Interoperability** | Architecture integrates Copilot Studio, Azure AI Foundry, Agentforce, ServiceNow, and custom agents under one orchestration layer. Platform choice per agent does not break the hierarchy. |
| 🛡️ | P0 💎 | **Enterprise Governance** | Agent inventory, access control, policy enforcement, and audit trail without custom-built infrastructure. Agent 365 + Purview + Defender + CoE Toolkit are the governance stack. |
| 📋 | P0 💎 | **Logging & Observability** | A single correlationId reconstructs every agent hop, tool call, and handoff from gateway to specialist. Transcript-only logging does not qualify. |
| 🔀 | P0 💎 | **Orchestration at Scale** | Routes and manages 100+ agents across domains without a single-orchestrator bottleneck. Domain hierarchy eliminates the star topology accuracy collapse documented at 30+ agents. |
| 🏢 | P0 💎 | **LOB System Integration** | Connects to SAP, Salesforce, ServiceNow, Dynamics 365, and Oracle with minimal custom code. Power Automate connectors and Azure APIM are the integration layer. |
| 📱 | P0 💎 | **Multi-Channel Exposure** | Agents reachable across web, mobile, Teams, SharePoint, M365 Copilot, and custom surfaces without rebuilding per channel. |
| 📝 | P0 💎 | **Complex Agent Instructions** | Each agent supports rich, detailed system prompts with multi-step conditional logic. Instruction size or expressiveness is not a platform constraint. |
| 👑 | P0 💎 | **Supervisor Pattern** | A central supervisor agent routes tasks to specialist sub-agents, evaluates their outputs, and decides next steps — without hardcoded routing logic. |
| 🔧 | P2 🥈 | **Supervisor-as-Tool Pattern** | The supervisor is callable as a tool by a higher-level orchestrator — enabling composable multi-level delegation where any agent can be both orchestrator and sub-agent. |
| 🏛️ | P2 🥈 | **Hierarchical Orchestration Pattern** | Architecture supports multi-tier hierarchies (Gateway → Domain → Specialist) where each layer independently manages its own sub-agents without sharing a global context. |
| ⚙️ | P2 🥈 | **Custom Orchestration Pattern** | Engineering-owned domains can implement fully custom orchestration logic (SDK-level code, state machines, conditional branching) when declarative platform patterns are insufficient. |
| 🕸️ | P2 🥈 | **Network / Mesh Pattern** | Architecture supports peer-to-peer agent collaboration where agents can call each other laterally — not only top-down — with guardrails to prevent unbounded recursion. |

---


## 🎯 Scoring Strategy

Platforms are scored against all 18 criteria using the **Asymmetric Priority-Weighted (APW)** method. P0 non-adherence is penalised heavily and triggers a gate flag. P2 is upside-only.

### **Scoring Table**

| Tier | Criteria | Full Adherence | Partial Adherence | No Adherence | Max | Min |
|---|---|---|---|---|---|---|
| P0 💎 | 14 | **+2.0** | +0.75 | **−3.0** ⚠️ | +28.0 | −42.0 |
| P2 🥈 | 4 | **+0.50** | +0.25 | **0** | +2.0 | 0 |
| | | | | **Score range** | **+30.0** | **−42.0** |

**⚠️ Gate rule:** Any P0 = None automatically flags the platform **"Conditional"** — an approved mitigation plan is required before deployment, regardless of total score.

---

### **Score Interpretation**

| Score | % of Max (30) | Rating | Action |
|---|---|---|---|
| ≥ 25 | ≥ 83% | ✅ **Strongly Recommended** | Deploy with standard governance |
| 18–24 | 60–80% | ✅ **Recommended** | Deploy with documented gap mitigations |
| 10–17 | 33–57% | 🟡 **Acceptable** | Approved only with mitigation plan per gap |
| 1–9 | < 30% | ⚠️ **Not Recommended** | Requires architectural remediation before approval |
| ≤ 0 | — | ❌ **Disqualified** | Structural gaps cannot be mitigated |
| Any P0 = None | — | ⚠️ **Conditional** | Override — applies regardless of total score |

---

### 📚 References

- [Microsoft CAF — AI Agents](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/)
- [Azure Architecture Center — AI Agent Orchestration Patterns](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns)
- [DeepLearning.AI — Agentic Design Patterns](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/)
- [MAST — Why Enterprise Agents Fail](https://huggingface.co/blog/ibm-research/itbenchandmast)
- [Gartner — Multiagent Systems](https://www.gartner.com/en/articles/multiagent-systems)
{: .ref-grid}
