---
layout: default
title: "🔌 8. Infrastructure Requirements"
parent: Enterprise Multi-Agent Architecture
nav_order: 11
---

## 8. 🔌 Infrastructure Requirements — Definitive Answer

### ✅ Required (Managed — Minimal Setup)

| Infrastructure | Required? | When | Setup Effort |
|---|---|---|---|
| **Dataverse** | ✅ Always | CS transcripts (auto) · agent metadata · governance · admin config · user profiles when CS-heavy | Zero — auto-provisioned |
| **Redis** | ✅ For Foundry agents | Low latency session state: userId→threadId lookups during active sessions | Medium — Azure Cache for Redis |
| **Azure AI Search** | ✅ If RAG needed | Knowledge base > 500 docs or semantic similarity retrieval | Low — add as knowledge source |
| **Azure APIM** | ✅ For production | External REST API governance — rate limiting, auth, logging | Low — managed service |
| **Azure Data Lake** | ✅ If compliance | Transcript retention > 30 days | Low — Synapse Link connector |

### 🟡 Conditional — Driven by Architecture Lean

| Infrastructure | When Needed | When NOT Needed |
|---|---|---|
| **Cosmos DB** | **Foundry-heavy architecture** — transcripts + agent step recording + user profiles in one store · OR compliance mandate (CMK, data residency, full LLM audit trail) | CS-heavy architecture — Dataverse covers all storage needs. Never needed for CS. |
| **Foundry Memory Feature** | **Preview acceptable** — replaces Cosmos DB for user profiles with zero infrastructure; AI-driven extraction and semantic retrieval | CS-heavy (Dataverse covers profiles) · Foundry-heavy (Cosmos DB covers profiles) · any architecture where preview dependency is a production risk |

### ❌ Not Required — Explicitly Ruled Out

| Infrastructure | Why Not Needed | What Replaces It |
|---|---|---|
| **SQL Server / Azure SQL** | Dataverse handles admin/governance data; Cosmos DB handles Foundry agent data; no role for a separate SQL tier | Dataverse (admin) · Cosmos DB (Foundry) |
| **Custom vector DB** | Azure AI Search is the managed vector store with no custom infra | Azure AI Search |
| **Custom audit database** | Agent 365 + Purview + App Insights + Cosmos DB agent-entity-store cover all audit needs | Agent 365 + Purview + App Insights |

### 📊 Infrastructure Matrix

```
CS-heavy architecture (most agents on Copilot Studio):
    ✅ Dataverse          → REQUIRED (transcripts auto · user profiles · metadata)
    ✅ Azure AI Search    → IF knowledge base > 500 docs
    ✅ Azure APIM         → FOR external API governance
    ✅ Azure Data Lake    → IF compliance requires >30 day retention
    ❌ Redis              → NOT REQUIRED
    ❌ Cosmos DB          → NOT REQUIRED
    ❌ Foundry Memory     → NOT REQUIRED

Foundry-heavy architecture (most agents on Foundry):
    ✅ Dataverse          → Governance metadata · CS agent transcripts if any
    ✅ Cosmos DB          → REQUIRED (transcripts + agent steps + user profiles)
    ✅ Redis              → REQUIRED (low latency userId→threadId session state)
    ✅ Azure AI Search    → IF knowledge base > 500 docs
    ✅ Azure APIM         → FOR external API governance
    ✅ Azure Data Lake    → IF compliance requires >30 day retention
    ❌ Foundry Memory     → NOT REQUIRED (Cosmos DB covers profiles)

Preview acceptable (either lean):
    ✅ Foundry Memory     → Replaces Cosmos DB for user profiles · zero infra · ⚠️ Preview
    ✅ Cosmos DB          → Still REQUIRED for transcripts + agent steps (Foundry Standard)
                           NOT REQUIRED for profiles if Foundry Memory is used
    ✅ Redis              → REQUIRED for Foundry agents (session state)
```

### 📚 References
- [Foundry Agent Setup Options](https://learn.microsoft.com/en-us/azure/ai-foundry/agents/how-to/standard-agent-setup)
- [Azure Cache for Redis](https://learn.microsoft.com/en-us/azure/azure-cache-for-redis/)
- [Azure AI Search](https://learn.microsoft.com/en-us/azure/search/semantic-search-overview)
- [Cosmos DB Foundry](https://learn.microsoft.com/en-us/azure/cosmos-db/gen-ai/azure-agent-service)
{: .ref-grid}

### 🏆 Recommendation
> **Infrastructure follows architecture lean — provision for your dominant platform, not per agent type. CS-heavy: Dataverse only — no Redis, no Cosmos DB, no Foundry Memory Feature. Foundry-heavy: Cosmos DB (transcripts + agent steps + user profiles) + Redis (session state) — no Foundry Memory Feature needed. Preview acceptable: Foundry Memory Feature replaces Cosmos DB for profiles only; Cosmos DB is still required for transcripts and agent step recording. Every component must be justified by the architecture — not assumed as a default.**
{: .recommendation}

---
