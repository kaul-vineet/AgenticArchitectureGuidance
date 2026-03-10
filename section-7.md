---
layout: default
title: "🔧 9. Agentic Framework and Agent 365"
parent: Enterprise Multi-Agent Architecture
nav_order: 12
---

## 9. 🔧 Agentic Framework and Agent 365 — Where They Fit

### 🤖 Microsoft Agent Framework (Semantic Kernel + AutoGen)

*Merged into a single open-source SDK October 2025.*

| Use It When | Skip It When |
|---|---|
| · ✅ Foundry agents need deterministic routing (not LLM-driven) | · ❌ Pure Copilot Studio no-code agents |
| · ✅ Embedding agent logic into existing .NET / Python apps | · ❌ Standard workflow automations (Power Automate is correct) |
| · ✅ Building Reflection loops or custom Planning pipelines in Foundry | · ❌ Team-authored agents |
| · ✅ LLM-as-a-Judge evaluation harnesses | · ❌ Agents with standard connector tool use |

### 🏢 Microsoft Agent 365

| Capability | Value at 100-Agent Scale |
|---|---|
| 🗂️ **Agent Registry** | All 100 agents in one view including shadow agents |
| 🔐 **Access Governance** | Blueprint-level policies applied fleet-wide |
| 📊 **Role-Based Dashboards** | IT, Security, Business each see relevant metrics |
| 🛡️ **Security Integration** | Defender detects anomalous tool call patterns |
| 🔗 **Interoperability Governance** | Controls which agents can call which other agents |

**What Agent 365 does NOT cover:**
- Agentforce logs → Salesforce Event Monitor → export, join by `correlationId`
- Foundry message-level traces → Azure Monitor + App Insights
- Power Platform maker-level inventory → CoE Toolkit

### 📋 CoE Toolkit vs. Agent 365

| | Agent 365 | CoE Toolkit |
|---|---|---|
| **Layer** | M365 consumption / governance | Power Platform maker / builder |
| **Covers** | Who uses agents, performance, security | Who built agents, which environment, ALM health |
| **Availability** | Frontier program (limited) | GA, free, open source |

### 📚 References
- [Microsoft Agent Framework](https://azure.microsoft.com/en-us/blog/introducing-microsoft-agent-framework/)
- [Microsoft Agent 365](https://www.microsoft.com/en-us/microsoft-agent-365)
- [Agent 365 Announcement](https://www.microsoft.com/en-us/microsoft-365/blog/2025/11/18/microsoft-agent-365-the-control-plane-for-ai-agents/)
- [Power Platform CoE Starter Kit](https://learn.microsoft.com/en-us/power-platform/guidance/coe/starter-kit)
{: .ref-grid}

### 🏆 Recommendation
> **Deploy CoE Toolkit immediately — free, GA, provides full inventory foundation for 100 agents. Enroll in Agent 365 Frontier now. Skip Agent Framework for CS agents — it adds developer complexity where no-code suffices. Use it only for Foundry agents requiring deterministic routing or custom evaluation.**
{: .recommendation}



---
