---
layout: default
title: "🧠 Key Architectural Principles"
parent: Enterprise Multi-Agent Architecture
nav_order: 2
---

## 🧠 Key Architectural Principles

*Consolidated from DeepLearning.AI, HuggingFace / MAST, smolagents, Microsoft CAF, Azure Architecture Center, and Azure Well-Architected Framework:*

| # | Principle | Source | Applied To This Architecture |
|---|---|---|---|
| 1️⃣ | **Minimise LLM calls** | HuggingFace / smolagents | Gateway routing is a CS Topic — zero LLM calls for intent classification. Domain routing is a CS Topic. PA flows handle all tool calls (leave balance, CRM update, HRIS write) without any LLM involvement. Target: fewer than 20% of all agent interactions should involve generative processing; 80% resolved by deterministic Topics and PA flows. Every new agent is reviewed for opportunities to replace LLM decisions with explicit Topic conditions. |
| 2️⃣ | **Default to Flows, escalate to Crews** | DeepLearning.AI | Every new domain starts as a CS Topic flow. Only when the task is genuinely exploratory — multi-document synthesis, open-ended research, complex planning — does it graduate to a Foundry agent with planning enabled. The architecture enforces this escalation ladder: CS is the default layer; Foundry is the exception layer. No agent is built in Foundry if the same outcome can be reliably delivered in CS. |
| 3️⃣ | **Clarification before action** | MAST (FM-2.2) | The Gateway has a mandatory disambiguation Topic that fires before any routing decision. If user intent matches more than one domain, or confidence is below threshold, the Gateway asks one clarifying question before delegating — never routes on ambiguity. Domain agents apply the same check before delegating to specialist agents. OpenEnv/Turing research: ambiguity causes a 50pp accuracy collapse (90% → 40%); disambiguation is not optional at enterprise scale. |
| 4️⃣ | **External verification — never self-check** | MAST (FM-3.3) | After every PA flow write (leave submission, CRM update, HRIS record change), a separate PA flow reads back the confirmation state from the system of record. The reading call is a different connector invocation from the writing call — the agent never confirms its own write. This pattern is enforced in the Agent Design Template: every tool that writes must have a corresponding verification tool that reads. MAST research: self-verification is the #1 source of hallucinated success states. |
| 5️⃣ | **Externalize termination logic** | MAST (FM-1.5, FM-3.1) | CS: session limits (100 turns, 60 min) are platform-enforced; Topic conditions check for explicit completion signals before continuing. Foundry: max_steps, loop detection, and escalation thresholds are in Workflow YAML and SDK code — never in the system prompt. Magentic pattern includes built-in stall detection. Every agent design must declare termination conditions in the Agent Design Template before build begins. |
| 6️⃣ | **Tool argument quality is the #1 failure cause** | HuggingFace (>50% of failures) | Every PA connector in this architecture must have: (1) a description field with an example call, (2) explicit RFC3339 datetime format requirements, (3) typed enums for categorical fields, (4) a structured error response including the field name that failed and expected format. These four elements are a build checklist item in the Agent Design Template. No connector is promoted to production without them. HuggingFace: >50% of agent failures trace to malformed tool arguments. |
| 7️⃣ | **Consolidate tools** | HuggingFace / smolagents | The leave management agent's PA flow reads balance and submits the request in one flow where the system allows it — not two sequential calls. The CRM agent's PA flow checks existing record and updates in one operation. Rule: if two tool calls always happen in sequence with no branching decision between them, they must be merged. Exception: if they need to be individually retried or individually verified after execution. |
| 8️⃣ | **Multi-agent is a reliability tax** | HuggingFace / AssetOpsBench | Valid boundaries in this architecture: HR vs. CRM (different data sovereignty), Finance (approval authority), Legal (confidentiality). Not valid: "HR Leave" vs. "HR Benefits" — these share a domain agent. Before creating a new agent, the team must answer: does this require a separate security boundary, a separate compliance scope, or a separate connector set that genuinely cannot be shared? If none, the capability stays in the existing agent. |
| 9️⃣ | **Context hygiene at scale** | HuggingFace / Forge RL | Gateway passes only: intent classification, userId, correlationId, and the minimum typed parameters for the domain — never full chat history. Domain agents pass only domain context and typed I/O to specialist agents. Inline CS children share parent context (acceptable). Connected agents receive only the fields they need. Foundry: compaction triggered every 10 tool turns. Context scope is defined per agent boundary in the Agent Design Template and enforced in code. |
| 🔟 | **Sensitive state in side channel** | HuggingFace / Neuro SAN | PA connectors store credentials in Power Platform environment connection references — never in Topics or system prompts. Employee IDs, salary figures, and leave balances are retrieved at tool execution time via PA flows and never stored in the agent conversation context. CorrelationIds are opaque tokens, not PII. Foundry agents use Managed Identity — no secrets in Workflow YAML or agent system prompts. |
| 1️⃣1️⃣ | **Tool accuracy > 90% as deployment gate** | HuggingFace / AssetOpsBench | The 90% gate is recalibrated per workflow length (Karpathy's march of nines). For the minimum Gateway → Domain → Specialist flow (3 hops): to achieve 98% end-to-end, each hop must reach 99.3%+ accuracy. The CoE Toolkit tracks per-connector call success rate. No domain goes to production until all connectors in its critical path meet the recalibrated threshold. The required per-step accuracy is computed and published in the Agent Design Template for each domain before build. |
| 1️⃣2️⃣ | **Human-in-the-loop for irreversible actions** | DeepLearning.AI · Microsoft CAF | Three categories per action: (1) Advisory — agent recommends, human decides (financial approvals, HR exceptions). (2) Supervised — agent executes, human has a 15-minute undo window (CRM updates, standard leave). (3) Autonomous — no review required (read-only queries, status checks, notifications). Every agent action is classified before build. PA approval flow for category 1 shows proposed action, agent reasoning, source data, and undo path in one Adaptive Card — reviewable in under 30 seconds. |
| 1️⃣3️⃣ | **Use lowest complexity that reliably works** | Azure Architecture Center | Decision ladder enforced at intake for every new agent request: (1) Can a PA flow without LLM solve this? Build it. (2) Can a single CS Topic with one LLM call solve this? Build it. (3) Does it need a specialist CS agent? Build it. (4) Does it need Foundry? Only if it requires parallel execution, a prompt > 8,000 chars, or Code Interpreter. Each rung must be documented as attempted and failed before the next rung is approved. |
| 1️⃣4️⃣ | **Justify every agent boundary explicitly** | Azure Architecture Center | Three valid reasons to add a new agent: (a) Security boundary — different data access levels or compliance scope. (b) Prompt complexity — task genuinely requires more than 8,000 chars or a Foundry model. (c) Tool overload — more than ~15 tools in a single agent degrades routing accuracy. Agents created for organisational reasons ("the HR team wants their own agent") rather than technical reasons are a governance problem, not an architecture decision. Default is no new boundary. |
| 1️⃣5️⃣ | **Single responsibility per agent** | Azure Architecture Center · Microsoft CAF | Each agent has a written job description: "Routes user intent to the correct domain" (Gateway), "Handles all leave-related tasks" (Leave Domain). The Agent Design Template requires a "this agent does NOT handle" section to make exclusions explicit. The CoE Toolkit monitors for intent overlap — if two agents resolve the same intent type, one of them is wrong. Monthly CoE review flags overlapping agents for consolidation. |
| 1️⃣6️⃣ | **Right-size the model per agent role** | Azure Architecture Center | CS: model selection is locked to Microsoft-managed — not configurable per agent (known limitation). Foundry: Gateway uses a mid-tier model (classification task); domain agents use a capable reasoning model; specialist agents use the smallest model that passes the capability test for their specific subtask. Model selection is recorded in the Agent Design Template with a capability test result attached — not assumed from the general domain category. |
| 1️⃣7️⃣ | **Validate output before passing downstream** | Azure Architecture Center | Connected CS agents return typed YAML — the receiving agent checks required fields before acting. Foundry agents use JSON schema validation at each Workflow node — malformed output halts the Workflow and triggers retry or escalation. The Gateway never passes a domain response to the user without checking for required fields (confirmationRef, status). Validation is enforced in code, not delegated to LLM judgment. |
| 1️⃣8️⃣ | **Surface errors explicitly — never swallow failures** | Azure Architecture Center | Every PA connector returns a structured error object: `{errorCode, field, expectedFormat, retryable: true/false}`. CS Topics have explicit error branches for each connector failure type — not a generic fallback. Foundry SDK code catches specific exception types and propagates them upward. The user receives either a confirmed success with a reference number or a named error with next steps — never a silent failure or an ambiguous response. |
| 1️⃣9️⃣ | **Idempotency for all writes** | Microsoft Tech Community | Every PA flow that writes to an external system includes an idempotency key: `{correlationId}-{actionType}-{date}`. Before writing, the flow checks whether the key already exists in Dataverse. If it does, it returns the existing result without re-executing. This prevents duplicate leave submissions, CRM updates, and financial records when PA flows are retried after timeout. Idempotency key generation is a required field in the Agent Design Template for every write action. |
| 2️⃣0️⃣ | **Kill switch — disable tools without redeployment** | Microsoft CAF | Three levels: (1) Agent-level — disable individual CS agent or Foundry project via portal, immediate effect. (2) Tool-level — disable specific PA connector or Azure Function without disabling the agent; agent degrades gracefully to read-only for that operation. (3) Fleet-level — CoE Toolkit broadcasts a maintenance message to all CS agents simultaneously. Kill switch procedure for each domain is documented in the runbook before that domain goes live — not after an incident. |
| 2️⃣1️⃣ | **Security trimming in every agent, not just the edge** | Azure Architecture Center | CS agents query Dataverse using the authenticated user's Entra identity — Dataverse RLS trims results at the data layer before they reach the agent. Foundry agents query with a Managed Identity scoped to the minimum required permissions. Knowledge base search results are security-trimmed by the search index — not filtered after retrieval by the LLM. No agent response includes data that was not returned by the underlying system for that specific user's entitlement. |
| 2️⃣2️⃣ | **Content safety at every layer** | Azure Architecture Center | CS: AI Builder content safety classifier on user input before the Topic fires; PA flow responses pass through a content check before inclusion in the agent response. Foundry: Azure AI Content Safety applied at the Workflow input and output nodes. No LLM-generated content surfaces to the user without passing the output content check. Safety filter level is configurable per domain — stricter for HR and Finance, standard for general enquiry agents. |
| 2️⃣3️⃣ | **Scope agent authority to operations, not tools** | Microsoft Tech Community | Every PA connector is scoped to the minimum operation: the Leave agent's HRIS connector can only submit leave and read balance — it cannot modify employee records. The CRM agent's Salesforce connector can only update designated fields — it cannot create or delete records. Rate limits: each specialist agent is capped at 10 write operations per session. These scopes are enforced in connection reference configuration at the infrastructure layer — not in the system prompt. |
| 2️⃣4️⃣ | **Instrument all handoffs, not just final output** | Azure Architecture Center · Microsoft Azure Blog | CorrelationId is generated at the Gateway on the user's first message and passed as a parameter to every downstream agent call, PA flow execution, and tool invocation. Every Dataverse log entry includes the correlationId. App Insights tags all Foundry traces with the same correlationId. A single correlationId must be sufficient to reconstruct the complete end-to-end trace for any user interaction across all agents, tools, and platforms — this is a non-negotiable acceptance criterion for every domain. |
| 2️⃣5️⃣ | **Automated evaluation in CI/CD** | Microsoft Azure Blog | PP Pipelines for CS: solution import triggers an automated test suite running the 50-sample eval harness per agent, checks connector health, and validates that intent routing accuracy has not degraded vs. the previous baseline. Azure DevOps for Foundry: every Workflow YAML change triggers the same eval harness plus a regression test against the last 30 days of production traces. A prompt change has the same gate as a code change — no exceptions. LLM-as-Judge scores are the acceptance criterion; exact-match assertions are not used. |
| 2️⃣6️⃣ | **Evaluate continuously in production** | Microsoft Azure Blog | CoE Toolkit dashboards track weekly: intent resolution rate per Gateway topic, tool call success rate per connector, session abandonment rate per domain agent, and escalation rate to human. Any metric dropping more than 5% week-over-week triggers an automatic review ticket. Foundry App Insights tracks the same metrics with per-hop breakdown. Monthly: re-run the full eval harness against current production state to detect prompt drift — model updates from Microsoft can silently degrade behaviour. |
| 2️⃣7️⃣ | **Maintain agent inventory — shadow agents are a risk** | Microsoft CAF | Agent 365 is the system of record for every CS agent: named owner, business domain, production date, last-reviewed date, and decommission plan. No agent is deployed to production without prior registration in Agent 365. Monthly CoE review flags agents with no owner, no usage in 30 days, or no review in 90 days — these are queued for decommission. Shadow agents discovered by environment scanning are treated as security incidents, not housekeeping items. |
| 2️⃣8️⃣ | **Externalize and version-control prompts** | Azure Well-Architected Framework | CS agent instructions are stored in solution environment variables — not hardcoded in Topics — so a prompt update is a configuration change that promotes through PP Pipelines without a Topic rebuild. Foundry system prompts are stored in Azure App Configuration or Key Vault secrets — not in Workflow YAML directly. Every prompt change requires a pull request with at least one reviewer and must pass the eval harness before merge. A prompt change is a code deployment and is treated as one. |

### 📚 References
- [DeepLearning.AI — Agentic Design Patterns](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/)
- [MAST Failure Taxonomy](https://huggingface.co/blog/ibm-research/itbenchandmast)
- [AssetOpsBench](https://huggingface.co/blog/ibm-research/assetopsbench-playground-on-hugging-face)
- [HuggingFace — Design Patterns for Agentic Workflows](https://huggingface.co/blog/dcarpintero/design-patterns-for-building-agentic-workflows)
- [smolagents](https://huggingface.co/blog/smolagents)
- [OpenEnv — Tool Argument Failures](https://huggingface.co/blog/openenv-turing)
- [Neuro SAN — Side Channel Pattern](https://huggingface.co/blog/danyoung/neuro-san-is-all-you-need)
- [Agent Leaderboard — Tool Quality Benchmarks](https://huggingface.co/blog/pratikbhavsar/agent-leaderboard)
- [Azure Architecture Center — AI Agent Orchestration Patterns](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns)
- [Microsoft CAF — Governance and Security for AI Agents](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/governance-security-across-organization)
- [Azure Well-Architected Framework — Application Design for AI Workloads](https://learn.microsoft.com/en-us/azure/well-architected/ai/application-design)
- [Microsoft Copilot Studio — Multi-Agent Patterns](https://learn.microsoft.com/en-us/microsoft-copilot-studio/guidance/architecture/multi-agent-patterns)
- [Microsoft Azure Blog — Agent Observability Best Practices](https://azure.microsoft.com/en-us/blog/agent-factory-top-5-agent-observability-best-practices-for-reliable-ai/)
- [Microsoft Tech Community — AI Production Architecture](https://techcommunity.microsoft.com/blog/educatordeveloperblog/ai-didn%E2%80%99t-break-your-production-%E2%80%94-your-architecture-did/4482848)
- [Microsoft for Developers — Designing Multi-Agent Intelligence](https://developer.microsoft.com/blog/designing-multi-agent-intelligence)
{: .ref-grid}

---

### ⚙️ Flows vs. Crews

| | 🔵 Flow (Deterministic) | 🟠 Crew (Autonomous) |
|---|---|---|
| **Nature** | Sequential, explicit steps | Exploratory, emergent |
| **Reliability** | High | Lower — each step adds failure risk |
| **Best for** | Compliance, known workflows | Research, genuinely open-ended tasks |
| **Microsoft mapping** | CS Topics · PA Flows · Foundry Workflows | Foundry Agent Service with planning |
| **Use ratio** | **~80% of tasks** | **~20% of tasks** |

> 💡 **HuggingFace / smolagents principle**: "Regularize towards not using any agentic behaviour." Reduce LLM calls to the minimum. Every added LLM call is a new error vector.

---

### 🧠 Agentic Pattern Reliability Hierarchy

*Based on Andrew Ng (DeepLearning.AI) and MAST empirical research:*

<table class="reliability-table">
  <thead>
    <tr>
      <th class="rel-label">Pattern</th>
      <th class="rel-pct">Reliability</th>
      <th class="rel-bar-cell"></th>
      <th class="rel-note">When to use</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="rel-label">🔧 Tool Use / Deterministic Flow</td>
      <td class="rel-pct">100%</td>
      <td class="rel-bar-cell"><div class="rel-bar" style="width:100%"></div></td>
      <td class="rel-note">CS Topics, PA flows, known workflows — default for ~80% of all tasks</td>
    </tr>
    <tr>
      <td class="rel-label">🔁 Reflection / External Verification</td>
      <td class="rel-pct">80%</td>
      <td class="rel-bar-cell"><div class="rel-bar" style="width:80%"></div></td>
      <td class="rel-note">Separate critic agent or deterministic check — add for quality-sensitive outputs. <strong>Never</strong> let an agent verify its own output.</td>
    </tr>
    <tr>
      <td class="rel-label">📋 Planning</td>
      <td class="rel-pct">40%</td>
      <td class="rel-bar-cell"><div class="rel-bar" style="width:40%"></div></td>
      <td class="rel-note">Dynamic task decomposition — only for genuinely open-ended exploratory tasks. Never for known workflows.</td>
    </tr>
    <tr>
      <td class="rel-label">🤝 Multi-Agent Collaboration</td>
      <td class="rel-pct">30%</td>
      <td class="rel-bar-cell"><div class="rel-bar" style="width:30%"></div></td>
      <td class="rel-note">Each handoff costs ~10–15 accuracy points (68% single-agent → 47% multi-agent). Use only where domain isolation is genuinely required.</td>
    </tr>
  </tbody>
</table>

---
