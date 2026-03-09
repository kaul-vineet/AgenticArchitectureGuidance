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
| 1️⃣ | **Minimise LLM calls** | [HuggingFace / smolagents](https://huggingface.co/blog/smolagents) | **CS:** Gateway and domain routing are CS Topics — zero LLM calls. PA flows handle all tool calls (leave balance, CRM update, HRIS write) without LLM involvement.<br>**Foundry:** Workflow nodes are deterministic by default; planning enabled only for genuinely open-ended tasks. Target: <20% of interactions involve generative processing. |
| 2️⃣ | **Default to Flows, escalate to Crews** | [DeepLearning.AI](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/) | **CS:** CS Topic flows are the default for every new domain; no generative topics unless deterministic routing fails.<br>**Foundry:** Sequential and HITL Workflows for 80% of tasks; Magentic / Group Chat only for genuinely open-ended scenarios. No Foundry build is approved if CS can reliably deliver the outcome. |
| 3️⃣ | **Clarification before action** | [MAST (FM-2.2)](https://huggingface.co/blog/ibm-research/itbenchandmast) | **CS:** Gateway has a mandatory disambiguation Topic before any routing decision; domain agents apply the same check before delegating to specialists.<br>**Foundry:** A clarification Workflow node is built into every branch where input intent is ambiguous. Ambiguity causes a 50pp accuracy collapse (OpenEnv/Turing) — this gate is non-negotiable on both platforms. |
| 4️⃣ | **External verification — never self-check** | [MAST (FM-3.3)](https://huggingface.co/blog/ibm-research/itbenchandmast) | **CS:** After every PA flow write, a separate PA flow reads back confirmation from the system of record — never the same call.<br>**Foundry:** A dedicated verification tool call follows every write in the Workflow; structured JSON enables deterministic state checks. Every write tool in the Agent Design Template must have a paired read-back verification tool. |
| 5️⃣ | **Externalize termination logic** | [MAST (FM-1.5, FM-3.1)](https://huggingface.co/blog/ibm-research/itbenchandmast) | **CS:** Session limits (100 turns, 60 min) are platform-enforced; Topic conditions declare explicit completion signals.<br>**Foundry:** `max_steps`, loop detection, and escalation thresholds are in Workflow YAML and SDK code — never in the system prompt. Magentic includes built-in stall detection. Termination conditions are declared in the Agent Design Template before build on both platforms. |
| 6️⃣ | **Tool argument quality is the #1 failure cause** | [HuggingFace / OpenEnv](https://huggingface.co/blog/openenv-turing) | **CS:** Every PA connector must include: (1) an example call, (2) RFC3339 datetime formats, (3) typed enums, (4) a structured error response with field name and expected format.<br>**Foundry:** Same four requirements apply to every Azure Function and OpenAPI tool definition. These are mandatory build checklist items — no connector is promoted without them. |
| 7️⃣ | **Consolidate tools** | [HuggingFace / smolagents](https://huggingface.co/blog/smolagents) | **CS:** PA flows that always execute in sequence (e.g. check balance + submit leave) are merged into one flow.<br>**Foundry:** Azure Functions consolidate multi-step operations where no intermediate decision is needed. Rule: if two calls always happen sequentially with no branch between them, they must be merged. Exception: if they require individual retry or independent verification. |
| 8️⃣ | **Multi-agent is a reliability tax** | [HuggingFace / AssetOpsBench](https://huggingface.co/blog/ibm-research/assetopsbench-playground-on-hugging-face) | **CS:** New boundaries require one of: different data sovereignty, separate compliance scope, or connectors that cannot be shared.<br>**Foundry:** Each Workflow sub-agent hop adds LLM inference cost; sub-agents added only where parallel execution or specialist isolation is demonstrably required. Organisational reasons are not valid justification on either platform. |
| 9️⃣ | **Context hygiene at scale** | [HuggingFace / Forge RL](https://huggingface.co/blog/dcarpintero/design-patterns-for-building-agentic-workflows) | **CS:** Gateway passes only intent, userId, and correlationId — no full chat history. Inline children share parent context; connected agents receive only the typed fields they need.<br>**Foundry:** Per-thread context per sub-agent; compaction every 10 tool turns; explicit state passed at each Workflow boundary. Context scope is defined per boundary in the Agent Design Template on both platforms. |
| 🔟 | **Sensitive state in side channel** | [HuggingFace / Neuro SAN](https://huggingface.co/blog/danyoung/neuro-san-is-all-you-need) | **CS:** Credentials live in Power Platform connection references — never in Topics or system prompts. Employee IDs, balances, and salary figures are fetched at tool execution time and never stored in the conversation context.<br>**Foundry:** Managed Identity for all service calls; secrets in Azure Key Vault — never in Workflow YAML or agent instructions. |
| 1️⃣1️⃣ | **Tool accuracy > 90% as deployment gate** | [HuggingFace / AssetOpsBench](https://huggingface.co/blog/ibm-research/assetopsbench-playground-on-hugging-face) | **CS:** CoE Toolkit tracks per-connector success rate; 90% baseline is recalibrated per workflow length — a 3-hop flow targeting 98% end-to-end requires 99.3%+ per step.<br>**Foundry:** App Insights tracks the same metric per Workflow node. Required per-step accuracy is computed in the Agent Design Template for each domain before build begins on both platforms. |
| 1️⃣2️⃣ | **Human-in-the-loop for irreversible actions** | [DeepLearning.AI](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/) · [Microsoft CAF](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/governance-security-across-organization) | Every action is pre-classified as: (1) **Advisory** — agent recommends, human decides (financial approvals, HR exceptions). (2) **Supervised** — agent executes, 15-min undo window (CRM updates, standard leave). (3) **Autonomous** — no review (read-only, notifications). **CS:** PA approval flows with Adaptive Cards show action, reasoning, source data, and undo path — reviewable in <30 sec.<br>**Foundry:** HITL Workflow node pauses before irreversible writes. |
| 1️⃣3️⃣ | **Use lowest complexity that reliably works** | [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) | Decision ladder enforced at intake: (1) PA flow without LLM → (2) single CS Topic → (3) specialist CS agent → (4) Foundry. Each rung must be documented as attempted and failed before the next is approved. **CS is the default; Foundry is the exception.** No Foundry build is approved without documented evidence that CS cannot meet the requirement. |
| 1️⃣4️⃣ | **Justify every agent boundary explicitly** | [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) | Three valid reasons for a new boundary: (a) security / compliance isolation, (b) prompt complexity > 8,000 chars, (c) tool overload > ~15 tools. **CS:** Applies to domain and specialist splits.<br>**Foundry:** Applies to each Workflow sub-agent node. Organisational preference is not a valid reason. Default is no new boundary — the burden of proof is on the team requesting one. |
| 1️⃣5️⃣ | **Single responsibility per agent** | [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) · [Microsoft CAF](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/governance-security-across-organization) | Each agent has a written job description and an explicit "does NOT handle" list in the Agent Design Template. **CS:** CoE Toolkit monthly review flags intent overlap between agents.<br>**Foundry:** App Insights flags redundant invocations across sub-agents. Overlapping agents are consolidated — not left running in parallel on either platform. |
| 1️⃣6️⃣ | **Right-size the model per agent role** | [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) | **CS:** Model selection locked to Microsoft-managed — no per-agent tuning (known limitation; accept the trade-off).<br>**Foundry:** Gateway uses a mid-tier model (classification); domain agents use a capable reasoning model; specialist agents use the smallest model that passes the capability test for the specific subtask. Model selection and capability test result are recorded in the Agent Design Template. |
| 1️⃣7️⃣ | **Validate output before passing downstream** | [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) | **CS:** Connected agents return typed YAML — receiving agent checks required fields before acting; inline agents use Topic condition checks.<br>**Foundry:** JSON schema validation at each Workflow node; malformed output halts the Workflow and triggers retry or escalation. Validation is enforced in code on both platforms — never delegated to LLM judgment. |
| 1️⃣8️⃣ | **Surface errors explicitly — never swallow failures** | [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) | **CS:** Every PA connector returns `{errorCode, field, expectedFormat, retryable}`. Topics have named error branches per failure type — no generic fallback.<br>**Foundry:** SDK catches specific exception types; errors propagate upward through the Workflow. Users see either a confirmed success with reference number or a named error with next steps — never a silent failure. |
| 1️⃣9️⃣ | **Idempotency for all writes** | [Microsoft Tech Community](https://techcommunity.microsoft.com/blog/educatordeveloperblog/ai-didn%E2%80%99t-break-your-production-%E2%80%94-your-architecture-did/4482848) | **CS:** PA flows include idempotency key `{correlationId}-{actionType}-{date}`; flow checks Dataverse for existing key before executing.<br>**Foundry:** Azure Functions and OpenAPI tools include the same key pattern. Prevents duplicate leave submissions, CRM updates, and financial records on retry. Idempotency key is a required field in the Agent Design Template for every write action on both platforms. |
| 2️⃣0️⃣ | **Kill switch — disable tools without redeployment** | [Microsoft CAF](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/governance-security-across-organization) | Three levels: (1) **Agent-level** — disable CS agent or Foundry project via portal, immediate effect. (2) **Tool-level** — disable a specific PA connector or Azure Function; agent degrades to read-only. (3) **Fleet-level** — CoE Toolkit broadcasts maintenance message to all CS agents simultaneously. Kill switch runbook for each domain is written before go-live — not after an incident. |
| 2️⃣1️⃣ | **Security trimming in every agent, not just the edge** | [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) | **CS:** Agents query Dataverse using the authenticated user's Entra identity — RLS trims results at the data layer, not by the LLM.<br>**Foundry:** Managed Identity scoped to minimum required permissions; search index security-trims results before retrieval. No agent response on either platform contains data not returned by the underlying system for that user's entitlement. |
| 2️⃣2️⃣ | **Content safety at every layer** | [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) | **CS:** AI Builder content safety on user input before the Topic fires; PA flow responses pass through a content check before inclusion in the agent response.<br>**Foundry:** Azure AI Content Safety applied at Workflow input and output nodes. Safety filter level is configurable per domain — stricter for HR and Finance — on both platforms. |
| 2️⃣3️⃣ | **Scope agent authority to operations, not tools** | [Microsoft Tech Community](https://techcommunity.microsoft.com/blog/educatordeveloperblog/ai-didn%E2%80%99t-break-your-production-%E2%80%94-your-architecture-did/4482848) | **CS:** PA connectors scoped to minimum operations (HRIS: submit + read balance only; Salesforce: update designated fields only). Rate cap: 10 write operations per session per specialist agent.<br>**Foundry:** RBAC per Workflow node; blast radius caps in SDK code. Scopes are enforced at the infrastructure layer — not declared in the system prompt on either platform. |
| 2️⃣4️⃣ | **Instrument all handoffs, not just final output** | [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) · [Microsoft Azure Blog](https://azure.microsoft.com/en-us/blog/agent-factory-top-5-agent-observability-best-practices-for-reliable-ai/) | **CS:** CorrelationId generated at Gateway and passed to every downstream agent call, PA flow, and tool invocation. Dataverse logs each hop.<br>**Foundry:** Full OpenTelemetry via App Insights; every hop tagged with the same correlationId. A single correlationId must reconstruct the complete end-to-end trace across all agents and platforms — non-negotiable acceptance criterion for every domain. |
| 2️⃣5️⃣ | **Automated evaluation in CI/CD** | [Microsoft Azure Blog](https://azure.microsoft.com/en-us/blog/agent-factory-top-5-agent-observability-best-practices-for-reliable-ai/) | **CS:** PP Pipelines trigger the 50-sample eval harness per agent, connector health checks, and routing accuracy regression on every solution import.<br>**Foundry:** Azure DevOps / GitHub Actions run the same eval harness plus regression against the last 30 days of production traces on every Workflow YAML or prompt change. A prompt change has the same gate as a code change on both platforms. LLM-as-Judge is the scoring method; exact-match is not used. |
| 2️⃣6️⃣ | **Evaluate continuously in production** | [Microsoft Azure Blog](https://azure.microsoft.com/en-us/blog/agent-factory-top-5-agent-observability-best-practices-for-reliable-ai/) | **CS:** CoE Toolkit tracks weekly: intent resolution rate, connector success rate, session abandonment, and escalation rate. A >5% week-over-week drop triggers a review ticket.<br>**Foundry:** App Insights tracks the same metrics per Workflow hop. Monthly: full eval harness re-run on both platforms to catch silent prompt drift from model updates. |
| 2️⃣7️⃣ | **Maintain agent inventory — shadow agents are a risk** | [Microsoft CAF](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/governance-security-across-organization) | **CS:** Agent 365 is the system of record — every agent has a named owner, domain, production date, last-reviewed date, and decommission plan. No deployment without prior registration.<br>**Foundry:** No native registry; custom Azure Policy + Entra tracking required. Monthly CoE review flags agents unused for 30 days or unreviewed for 90 days. Shadow agents found by environment scan are treated as security incidents on both platforms. |
| 2️⃣8️⃣ | **Externalize and version-control prompts** | [Azure Well-Architected Framework](https://learn.microsoft.com/en-us/azure/well-architected/ai/application-design) | **CS:** Agent instructions stored in solution environment variables — not hardcoded in Topics — so prompt updates promote through PP Pipelines as configuration changes without Topic rebuilds.<br>**Foundry:** System prompts in Azure App Configuration or Key Vault — not in Workflow YAML. Every prompt change requires a PR with reviewer sign-off and must pass the eval harness before merge on both platforms. A prompt change is a code deployment. |

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
