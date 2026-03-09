---
layout: default
title: "2. How a User Instruction is Processed"
parent: Enterprise Multi-Agent Architecture
nav_order: 3
---

## 2. 🔄 How a User Instruction is Processed

```
👤 User: "Apply for leave and update my Salesforce records"
    │
    ▼
🚪 Gateway Agent (Copilot Studio)
    │  FLOW: Classifies intent → multi-domain
    │  Disambiguation check: if intent is ambiguous → enter
    │  clarification topic BEFORE routing (HuggingFace finding:
    │  ambiguity causes 50-point accuracy drop)
    │  correlationId generated and propagated downstream
    │
    ▼
📦 HR Domain Agent (Copilot Studio)
    │  FLOW: Routes to Leave Management child agent
    │  Passes typed parameters (userId, leaveType)
    │
    ▼
🔧 Leave Management Agent (CS connected child)
    │  TOOL USE: PA flow → reads HRIS (leave balance)
    │  TOOL USE: PA flow → submits leave request
    │  VERIFICATION GATE: reads back confirmation state
    │    deterministically — does NOT ask agent to confirm
    │    its own output (FM-3.3: hallucinated success)
    │  Returns: typed output (confirmationRef, managerEmail)
    │
    ▼
📦 HR Domain Agent aggregates → passes result to Gateway
    │
    ▼
🚪 Gateway Agent
    │  Routes CRM update to Agentforce (A2A + correlationId)
    │
    ▼
🟥 Agentforce Agent
    │  Receives: A2A payload with full chat history + contextId
    │  TOOL USE: Updates Salesforce record
    │  Returns: CRM confirmation
    │
    ▼
🚪 Gateway aggregates both results → composes final response
    │
    ▼
👤 "Leave approved (Ref: L2026-441). Salesforce updated."
```

### 📋 Key Safeguards in the Flow

| Risk | Safeguard Applied |
|---|---|
| Agent verifies its own output (FM-3.3) | Deterministic read-back after every write action |
| Ambiguous user input causes wrong routing | Explicit disambiguation topic before any agentic branch |
| Agent loops without progress (FM-1.3) | Max step count + loop detector in Foundry orchestration code |
| Agent does not know when to stop (FM-1.5) | Termination conditions in code — never in LLM prompt |
| Tool argument malformed (>50% of failures per HuggingFace) | Structured error responses with format examples on every connector |

### 📚 References
- [Copilot Studio — Multi-Agent Patterns](https://learn.microsoft.com/en-us/microsoft-copilot-studio/guidance/multi-agent-patterns)
- [AssetOpsBench — Enterprise Agent Benchmarking](https://huggingface.co/blog/ibm-research/assetopsbench-playground-on-hugging-face)
- [OpenEnv — Tool-Using Agents Evaluation](https://huggingface.co/blog/openenv-turing)
- [DeepLearning.AI — Tool Use Pattern](https://www.deeplearning.ai/the-batch/agentic-design-patterns-part-3-tool-use/)
- [A2A Protocol](https://a2a-protocol.org/latest/)

### 🏆 Recommendation
> ⚡🔵 **This flow requires zero custom code. CS Topics handle routing, Power Automate handles tool calls, A2A/REST handles Agentforce. Build this in 1–2 sprints per domain. Do not add reflection or planning until the basic deterministic flow is stable and evaluated.**

---
