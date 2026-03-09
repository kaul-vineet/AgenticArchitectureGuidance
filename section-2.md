---
layout: default
title: "🔄 2. How a User Instruction is Processed"
parent: Enterprise Multi-Agent Architecture
nav_order: 4
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

Each safeguard is grounded in a validated research finding or architectural requirement:

| Risk | Safeguard Applied | Evidence |
|---|---|---|
| Agent verifies its own output (FM-3.3) | Deterministic read-back after every write action — separate tool call, not LLM confirmation | MAST/ITBench: 94% of failed traces show reasoning-action decoupling; self-verification is the #1 source of hallucinated success states |
| Ambiguous user input causes wrong routing | Explicit disambiguation CS Topic before any agentic branch — ask and confirm before routing | OpenEnv/Turing: ambiguity causes 50pp accuracy collapse (90% → 40%); clarification is not optional at enterprise scale |
| Agent loops without progress (FM-1.3) | Max step count + loop detector in orchestration code; never in LLM prompt | MAST FM-1.5: agents that do not know when to stop are the #1 failure predictor |
| Agent does not know when to stop (FM-1.5) | Termination conditions externalised in code — max turns, task completion signal, escalation threshold | MAST FM-1.5: externalized termination is a required architectural control, not a prompt engineering technique |
| Tool argument malformed | Structured error responses with explicit format examples on every connector; RFC3339 datetimes, typed enums | HuggingFace: >50% of agent failures trace to malformed tool arguments; argument quality is the single biggest performance lever |
| Context overload at the Gateway | Gateway routes intent only — owns zero tools, maintains no domain state | AgentArch: 18pp accuracy advantage for domain-specialized agents vs. generalist; MAST FM-1.4: 24% context loss in overloaded agents |
| Hallucination in cross-agent handoffs | All agent boundaries use typed I/O (YAML contracts for CS, structured JSON for Foundry); correlationId propagated end-to-end | AgentArch: 36% hallucination in unstructured peer-to-peer ReAct vs. 0% in orchestrator-led configurations |
| Uncontrolled peer-to-peer agent calls | All delegation is orchestrator-controlled — Gateway delegates to Domain, Domain delegates to Specialist; no lateral agent-to-agent calls without supervisor knowledge | AgentArch: peer-to-peer ReAct 36% hallucination; Microsoft architecture guidance: "orchestrator-controlled delegation only" |
| Context degradation beyond 12 tool turns | Each domain agent resets context at domain boundary — only domain-relevant context is passed; compaction step every N turns | LoCoBench/ReliabilityBench: beyond 12 tool turns, redundant operations emerge; 50% longer context → 3–5% efficiency loss |
| No auditability across agent hops | correlationId generated at Gateway and propagated to every downstream agent call; App Insights / Dataverse log every hop | MAST: 5.3 failure modes per trace in overloaded systems are only diagnosable with per-hop tracing |

### 📐 Alignment to Recommendation Criteria

This flow is the operational expression of the Gateway → Domain → Specialist architecture evaluated against the full criteria set:

| Criterion | How This Flow Satisfies It |
|---|---|
| 🧩 No-code over custom | CS Topics for Gateway routing and Domain routing; PA flows for all tool calls; zero custom orchestration code |
| 🔀 Orchestration across 100+ agents | Gateway → HR Domain → Leave Specialist is a 3-tier hierarchy; adding domains requires zero Gateway changes |
| 🥇 P1 Supervisor pattern | Gateway acts as supervisor — classifies intent, routes to correct domain, aggregates results |
| 🥇 P1 Supervisor-as-tool | Gateway can itself be called as an A2A endpoint by an enterprise-level meta-orchestrator |
| 🥇 P1 Hierarchical pattern | Gateway → Domain → Specialist is the exact pattern; each tier manages its own bounded context |
| 🌐 MSFT + non-MSFT interoperability | Gateway calls Agentforce via A2A in the same flow as native CS agents — no architectural seam |
| 🔗 A2A / Agent interoperability | correlationId + A2A payload links CS session and Agentforce session across platforms |
| 🛡️ Enterprise governance | Every hop logged; correlationId enables end-to-end audit trail; Agent 365 tracks all CS agents |
| 📋 Logging & observability | correlationId propagated end-to-end; Dataverse logs CS transcript; App Insights available for Foundry/A2A hops |
| 🏢 LOB system integration | PA flow calls HRIS (leave balance + submission) with no custom code; Agentforce handles CRM update via A2A |

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
