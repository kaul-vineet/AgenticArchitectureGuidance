---
layout: default
title: "🔄 5. How a User Instruction is Processed"
parent: Enterprise Multi-Agent Architecture
nav_order: 7
---

## 5. 🔄 How a User Instruction is Processed

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

> 📎 Every safeguard applied in this flow is grounded in the [Key Architectural Principles](../section-principles) — see principles 3️⃣ 4️⃣ 5️⃣ 6️⃣ 9️⃣ 1️⃣7️⃣ 2️⃣4️⃣ for the specific controls governing disambiguation, verification, termination, tool quality, context hygiene, output validation, and end-to-end tracing.

### 📚 References
- [Copilot Studio — Multi-Agent Patterns](https://learn.microsoft.com/en-us/microsoft-copilot-studio/guidance/multi-agent-patterns)
- [AgentArch Benchmark — Multi-Agent Architectures (arXiv 2509.10769)](https://arxiv.org/abs/2509.10769)
- [MAST — Failure Taxonomy for Multi-Agent Systems (arXiv 2503.13657)](https://arxiv.org/abs/2503.13657)
- [AssetOpsBench — Enterprise Agent Benchmarking](https://huggingface.co/blog/ibm-research/assetopsbench-playground-on-hugging-face)
- [OpenEnv — Tool-Using Agents Evaluation](https://huggingface.co/blog/openenv-turing)
- [LoCoBench — Context Degradation in Agent Loops (arXiv 2511.13998)](https://arxiv.org/abs/2511.13998)
- [Microsoft Multi-Agent Reference Architecture](https://microsoft.github.io/multi-agent-reference-architecture/docs/reference-architecture/Reference-Architecture.html)
- [A2A Protocol](https://a2a-protocol.org/latest/)
{: .ref-grid}

### 🏆 Recommendation
> ⚡🔵 **This flow requires zero custom code and satisfies all P1 orchestration requirements. CS Topics handle supervisor routing, Power Automate handles LOB tool calls, A2A handles Agentforce. The Gateway → Domain → Specialist hierarchy delivers 18pp higher decision accuracy (AgentArch) and 0% hallucination rate (vs. 36% in peer-to-peer) while requiring no custom infrastructure. Build this in 1–2 sprints per domain. Do not add reflection or planning until the deterministic flow is stable, evaluated, and above 90% tool accuracy on every connector.**
{: .recommendation}

---
