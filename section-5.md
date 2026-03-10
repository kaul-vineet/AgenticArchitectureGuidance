---
layout: default
title: "💾 4. Session Management"
parent: Enterprise Multi-Agent Architecture
nav_order: 10
---

## 4. 💾 Session Management, Transcript Storage & Persistent Storage

### 🔄 Session Management

| Platform | How Session is Natively Managed | Mitigation |
|---|---|---|
| **Copilot Studio** | Automatic — session variables maintained per conversation; 30 min idle · 60 min total · 100 turns hard limits | Inactivity reset topic · Store key state to session variables · Async pattern for tasks >5 min |
| **Foundry** | Automatic — thread-based; each session is a thread (up to 100k messages); no time limit; threadId maps to userId | **Redis** for userId→threadId registry (sub-ms lookup) · Set own retention policy · Use Memory feature for cross-session continuity |
| **Agentforce** | Salesforce-managed — session state owned by Salesforce platform | Pass correlationId in every A2A call to maintain continuity across platforms |

### 🧠 Context Architecture

*HuggingFace / Forge RL research finding: attention dilution degrades performance as context grows. Implement explicit compaction:*

| Tier | Name | What It Holds | CS Implementation | Foundry Implementation | Trigger |
|---|---|---|---|---|---|
| **Tier 1** | Active Context | Current task · recent tool results · active variables | Managed automatically | Managed automatically | — |
| ↓ | *Compaction step* | — | — | — | N turns or token threshold reached |
| **Tier 2** | Session Store | Intermediate results · extracted entities · task state | Dataverse table written via PA flow | Redis (hot session state, userId→threadId) + Memory feature (preview) | Compaction step fires |
| ↓ | *Archive step* | — | — | — | Session close |
| **Tier 3** | Long-Term Storage | User preferences · past conversation summaries · learned facts | Dataverse custom table (read via PA flow on session start) | **CS-heavy:** Dataverse via PA flow · **Foundry-heavy:** CosmosDB · **Preview acceptable:** Foundry Memory Feature | Session close |

### 📝 Conversation Transcript Storage

| Platform | Where Stored | Default Retention | For Compliance >30 Days |
|---|---|---|---|
| **Copilot Studio** | Dataverse `ConversationTranscript` (auto) | **30 days** | Synapse Link → Azure Data Lake |
| **Foundry** | Cosmos DB (Standard) or Microsoft-managed (Basic) | **No auto-expiry — you set it** | TTL policy on Cosmos DB containers |
| **Agentforce** | Salesforce Event Monitor | Salesforce-managed | Export via MuleSoft → Azure Data Lake |

**Cross-agent transcript correlation:**
- CS inline child → single parent transcript (no correlation needed)
- CS connected child → separate transcript → join by `correlationId`
- Foundry child → Foundry thread → join by `correlationId`
- Agentforce → Salesforce Event Monitor → export and join by `correlationId`

### Decision Matrix — Which Technology Serves Each Requirement

*Products in scope: Redis (cache) · CosmosDB (session) · SQL Server (profile) · Dataverse · Foundry Memory Feature*

---

#### 🟦 Copilot Studio

| Requirement | Storage Recommendation | Why — cannot be met by any other product |
|---|---|---|
| 📝 **Full conversation transcript** | **Dataverse** | • Auto-written by CS runtime to `ConversationTranscript` table — zero setup, no custom code<br>• Native 30-day retention; extend via Synapse Link → Azure Data Lake for compliance<br>• Redis: volatile by default — not durable for compliance transcripts<br>• CosmosDB: not in CS native stack; requires custom integration<br>• SQL Server: not in CS data path; requires custom connector<br>• Foundry Memory Feature: unavailable in CS context |
| 👤 **User profile across sessions** | **Dataverse** | • Custom Dataverse table read via PA flow on session start — native, no extra infra<br>• Fully integrated with CS security model and Entra ID<br>• Redis: no native CS connector; volatile without persistence config<br>• SQL Server: possible but adds custom connector complexity; Dataverse already provisioned<br>• CosmosDB: not natively reachable from CS without custom code<br>• Foundry Memory Feature: not available in CS context |
| 🔍 **Agent step recording** | **Dataverse** | • Custom table written via PA flow captures decision points and tool call outcomes<br>• Same managed identity and ALM lifecycle as the agent — no extra infrastructure<br>• Redis: not a durable audit log; evicts data without persistence config<br>• SQL Server: not natively accessible from CS Power Automate flows<br>• CosmosDB: no native CS integration path<br>• Foundry Memory Feature: not applicable to CS |
| ⚡ **Low latency (< 50ms)** | **CS Native Session Variables** | • Within-session state is held in CS runtime memory — no external store needed, zero network hop<br>• None of the 5 products applies: all are external stores with network overhead<br>• Dataverse (200–500ms REST) is the closest listed option but exceeds the 50ms target<br>• Redis, CosmosDB, SQL Server, Foundry Memory Feature: not in the CS native data path |

---

#### 🟧 Foundry AI

| Requirement | Storage Recommendation | Why — cannot be met by any other product |
|---|---|---|
| 📝 **Full conversation transcript** | **CosmosDB** | • `thread-message-store` stores every message verbatim<br>• `agent-entity-store` captures every LLM input/output with full payload and timestamp<br>• Redis: volatile — data lost on eviction without persistence config; not a durable transcript store<br>• SQL Server: no native schema for threaded conversations; concurrent writes degrade performance at scale<br>• Dataverse: 200–500ms REST overhead + service protection throttling — unsuitable for high-volume real-time writes<br>• Foundry Memory Feature: stores extracted summaries only; raw transcript is permanently lost |
| 👤 **User profile across sessions** | **Option 1: Foundry Memory Feature** ⚠️ Preview<br>**Option 2: CosmosDB** — Foundry-heavy architecture<br>**Option 3: Dataverse via PA flow** — CS-heavy architecture | **Option 1 — Foundry Memory Feature** *(when preview is acceptable)*<br>• Purpose-built for cross-session knowledge — AI-driven auto-extraction, zero custom code<br>• Hybrid search retrieval (semantic + keyword) for context-aware profile reads<br>• Zero infrastructure — portal toggle only<br>• ⚠️ Preview — not GA; do not use as sole production dependency<br><br>**Option 2 — CosmosDB** *(when most agents in the architecture are Foundry-based)*<br>• CosmosDB is already in the stack for transcript + step recording — no new infrastructure<br>• Durable, single-digit ms point reads by userId — direct SDK, no intermediary<br>• Structured profile schema — deterministic, no AI extraction needed for known enterprise fields<br>• GA — no preview risk<br><br>**Option 3 — Dataverse via PA flow** *(when most agents in the architecture are Copilot Studio-based)*<br>• Dataverse is already the dominant store — no new infrastructure<br>• Profile read once at session start — 200–500ms acceptable for session init, not mid-turn<br>• Requires PA flow intermediary (Foundry → PA → Dataverse); total ~1–2s at session start<br>• ⚠️ Validate Dataverse service protection limits at concurrent session scale |
| 🔍 **Agent step recording** | **CosmosDB** | • `agent-entity-store` natively captures every reasoning step, tool call, and model decision with full payload and timestamp<br>• No custom schema required — Foundry writes this automatically in Standard setup<br>• Redis: not a durable audit log; data lost on eviction without persistence config<br>• SQL Server: high-volume concurrent agent writes strain relational engines at scale<br>• Dataverse: service protection limits and 200–500ms write latency unsuitable for high-frequency step logging<br>• Foundry Memory Feature: not designed for execution step capture; no equivalent audit structure |
| ⚡ **Low latency (< 50ms)** | **Redis** | • Sub-millisecond key-value lookups — userId→threadId mapping resolved per request<br>• Hot user profile cache for prompt injection within a turn<br>• Scales horizontally with Azure Cache for Redis<br>• CosmosDB: single-digit ms — adequate for reads but not sub-ms for high-frequency per-turn lookups<br>• SQL Server: 10–100ms, degrades significantly under concurrent agent load<br>• Dataverse: 200–500ms REST overhead — fails the 50ms requirement<br>• Foundry Memory Feature: async extraction — not designed for real-time per-turn lookups |

---

#### 🟥 Agentforce

> ⚠️ Agentforce runs on Salesforce's own infrastructure. None of the five products (Redis, CosmosDB, SQL Server, Dataverse, Foundry Memory Feature) apply natively within Agentforce. Recommendations below follow Salesforce best practice. Integration with the Microsoft stack is via A2A/REST with `correlationId` propagation.

| Requirement | Storage Recommendation | Why — cannot be met by any other product |
|---|---|---|
| 📝 **Full conversation transcript** | **Salesforce Event Monitoring** | • Platform-native audit trail for all agent interactions — no custom setup required<br>• Per Salesforce best practice: Event Monitoring is the authoritative source for Agentforce conversation logs<br>• For long-term retention: export via MuleSoft → Azure Data Lake; join Microsoft-side traces by `correlationId`<br>• None of the 5 Microsoft products are in the Agentforce runtime data path |
| 👤 **User profile across sessions** | **Salesforce Data Cloud** | • Unified customer profile across all Salesforce products — purpose-built for cross-session identity<br>• Per Salesforce best practice: Data Cloud consolidates CRM, Marketing Cloud, and agent interaction data into a single profile<br>• Einstein AI features provide semantic retrieval over unified profiles natively<br>• Agentforce reads Data Cloud profiles without custom integration<br>• None of the 5 Microsoft products are accessible within Agentforce without an external API call |
| 🔍 **Agent step recording** | **Salesforce Event Monitoring + Agentforce Action Logs** | • Event Monitoring captures all agent actions, tool invocations, and decisions with timestamps<br>• Per Salesforce best practice: combine Event Monitoring with Agentforce-native Action Logs for full execution audit<br>• For Microsoft-side correlation: export logs and join by `correlationId` in Azure Data Lake<br>• None of the 5 Microsoft products are in the Agentforce execution path |
| ⚡ **Low latency (< 50ms)** | **Salesforce Platform Cache** | • Salesforce's native cache layer (org cache + session cache) — sub-100ms reads within the Salesforce runtime<br>• Per Salesforce best practice: Platform Cache is the recommended store for hot session state within Agentforce flows<br>• None of the 5 Microsoft products are accessible within the Salesforce runtime without a round-trip API call, which exceeds the 50ms target |

---

### Recommended Storage Assignment

```
📝 Full conversation transcript
    Copilot Studio  →  Dataverse (auto, zero setup — CS only)
    Foundry agents  →  Cosmos DB (Standard setup)
                       (no low-latency alternative for full
                        raw thread + LLM audit trail)

👤 User profile across sessions
    Architecture lean drives the choice — provision one store, not one per agent type:
    CS-heavy        →  Dataverse (via PA flow on session start)
                       Already the dominant store · zero new infra
    Foundry-heavy   →  CosmosDB (already in stack for transcript + steps)
                       Direct SDK read · single-digit ms · GA
    Preview OK      →  Foundry Memory Feature
                       AI-driven extraction · semantic retrieval · zero infra

🔍 Agent step recording
    All Foundry     →  Cosmos DB agent-entity-store
                       + App Insights (latency, failure metrics)

⚡ Low latency session state
    userId→threadId →  Redis (sub-ms lookups during active sessions)
    User profile    →  Memory Feature retrieval OR Redis cache
```

> 📎 The definitive infrastructure matrix — what to provision per architecture lean — is in [5. Infrastructure Requirements](../section-6).

### 📚 References
- [Copilot Studio — Transcript Controls](https://learn.microsoft.com/en-us/microsoft-copilot-studio/admin-transcript-controls)
- [Foundry — Threads, Runs, Messages](https://learn.microsoft.com/en-us/azure/ai-foundry/agents/concepts/threads-runs-messages)
- [Foundry — Memory](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/what-is-memory)
- [Foundry Standard Agent Setup](https://learn.microsoft.com/en-us/azure/ai-foundry/agents/how-to/standard-agent-setup)
- [Cosmos DB Foundry Integration](https://learn.microsoft.com/en-us/azure/cosmos-db/gen-ai/azure-agent-service)
- [Introducing Memory in Foundry](https://devblogs.microsoft.com/foundry/introducing-memory-in-foundry-agent-service/)
- [Forge RL — Context Compaction](https://huggingface.co/blog/MiniMax-AI/forge-scalable-agent-rl-framework-and-algorithm)
- [DeepLearning.AI — Agent Memory](https://www.deeplearning.ai/short-courses/llms-as-operating-systems-agent-memory/)
- [mem-agent Persistent Memory Research](https://huggingface.co/blog/driaforall/mem-agent)
- [Dataverse Capacity for Agents](https://www.microsoft.com/en-us/power-platform/blog/2025/12/04/dataverse-capacity/)
{: .ref-grid}

### 🏆 Recommendation
> **User profile store follows architecture lean — provision one store, not one per agent type. CS-heavy: Dataverse is the only store needed (transcripts auto-written, profiles via PA flow, session state in variables). Foundry-heavy: CosmosDB covers transcripts, agent steps, and profiles in one store; add Redis for low-latency session state. Preview acceptable: Foundry Memory Feature replaces CosmosDB for profiles with zero infrastructure. Do not mix profile stores across agent types — pick based on where your architecture leans and stay consistent.**
{: .recommendation}

---
