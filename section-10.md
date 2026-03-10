---
layout: default
title: "📖 12. Implementation Guide"
parent: Enterprise Multi-Agent Architecture
nav_order: 15
---

## 12. 📖 Implementation Guide

> For each of the 62 Key Architectural Principles, this section provides concise implementation guidance — platform-specific steps, thresholds, checklists, and options to evaluate. Use this when building or reviewing any agent in the architecture.

---

<a id="p1"></a>
### P1 · Minimise LLM calls

**Source:** [HuggingFace / smolagents](https://huggingface.co/blog/smolagents)

Route all structured, predictable tasks through CS Topics or PA flows — no LLM invocation. Reserve generative processing for tasks requiring language understanding that cannot be expressed as deterministic logic.

**CS:** Default every new topic to deterministic flow. Generative AI node requires explicit justification in the Agent Design Template.
**Foundry:** Implement read/write operations via PA flows called as tools. The `tools` array contains only tools that genuinely require LLM judgment.

**Gate:** Generative processing <20% of total interactions. Monitor weekly in App Insights; review any intent category above 20% for deterministic alternatives.

---

<a id="p2"></a>
### P2 · Default to Flows, escalate to Crews

**Source:** [DeepLearning.AI](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/)

Start every new requirement as a PA flow. Only escalate to an agentic crew when the flow demonstrably cannot handle the task.

**Escalation criteria (must document at least one):** (1) Task requires open-ended reasoning across variable inputs, (2) Multiple decision branches that cannot be expressed as if/switch logic, (3) Tool selection must be dynamic based on intermediate results.

**Agent Design Template requirement:** Record the PA flow attempt and documented failure mode before any crew build is approved. "PA flow is inconvenient" is not a valid escalation reason.

---

<a id="p3"></a>
### P3 · Clarification before action

**Source:** [MAST (FM-2.2)](https://huggingface.co/blog/ibm-research/itbenchandmast)

Resolve ambiguous intent with a single targeted question before routing or acting.

**CS:** Add an ambiguity detection condition to the gateway topic. If confidence score is below threshold OR two or more domains match with similar confidence, trigger a clarification turn before routing.
**Foundry:** Add an explicit `clarify_if_ambiguous` instruction in the system prompt with concrete examples of ambiguous input patterns for the domain.

**Gate:** Include at least 10 ambiguous inputs in the 50-sample golden dataset. Ambiguity resolution rate measured in the eval harness.

---

<a id="p4"></a>
### P4 · External verification — never self-check

**Source:** [MAST (FM-3.3)](https://huggingface.co/blog/ibm-research/itbenchandmast)

After every write action, perform a deterministic read-back from the target system. Never ask the LLM to verify its own output.

**CS:** Add a PA flow step after every write: read the written record back, compare key fields against the input, surface a typed error on mismatch.
**Foundry:** Add a verification tool call as the mandatory next step after any write tool call. The verification is deterministic code — not an LLM reasoning step.

**Agent Design Template:** Every write action lists its verification step in the I/O contract section.

---

<a id="p5"></a>
### P5 · Externalize termination logic

**Source:** [MAST (FM-1.5, FM-3.1)](https://huggingface.co/blog/ibm-research/itbenchandmast)

Hard-code all stop conditions in code before deployment. Never rely on agent reasoning to decide when to stop.

**CS:** Set maximum turn count in CS conversation settings. Add a step counter variable; if counter exceeds max steps, route to a fallback topic with a typed error.
**Foundry:** Set `max_steps` explicitly in every Workflow YAML and agent invocation. Add a loop detector: if the same tool is called with the same arguments twice, terminate with error code `LOOP_DETECTED`.

**Agent Design Template:** Maximum steps, timeout duration, and loop detection logic are mandatory fields before build begins.

---

<a id="p6"></a>
### P6 · Tool argument quality is the #1 failure cause

**Source:** [HuggingFace / OpenEnv](https://huggingface.co/blog/openenv-turing)

Every tool connector must have all four before promotion: typed parameter schema, example call with realistic values, structured error response schema, and a description that distinguishes it from related tools.

**CS:** Define all connector parameters as typed fields. Include example invocation in the action description. Define error response codes as an enum.
**Foundry:** Define tool schemas with `type`, `description`, `example`, and `required` for every parameter. Tool descriptions explain *when* to use the tool, not just *what* it does.

**Gate:** Any tool with >10% argument error rate in the 50-sample eval harness blocks promotion.

---

<a id="p7"></a>
### P7 · Consolidate tools

**Source:** [HuggingFace / smolagents](https://huggingface.co/blog/smolagents)

Identify tool pairs always called in sequence with no branch between them. Merge into a single atomic operation.

**Review trigger:** During Agent Design Template review, map the tool call sequence per intent. Flag any pair where Tool B is always called immediately after Tool A with no decision between them.

**Exception:** Keep separate if tools require independent retry logic, produce output used by different downstream paths, or have different error handling requirements.

---

<a id="p8"></a>
### P8 · Multi-agent is a reliability tax

**Source:** [HuggingFace / AssetOpsBench](https://huggingface.co/blog/ibm-research/assetopsbench-playground-on-hugging-face)

Challenge every proposed agent boundary. The default is no new boundary; the burden of proof rests with the proposing team.

**Valid justifications (must document one):** (1) Different data sovereignty or compliance scope, (2) Connectors that cannot be shared due to auth model, (3) Prompt complexity exceeds platform limit.

**Agent Design Template:** New boundary proposals must include estimated accuracy cost (10–15pp) and evidence that the valid justification applies. "The team prefers it" or "it maps to org structure" are explicitly invalid.

---

<a id="p9"></a>
### P9 · Context hygiene at scale

**Source:** [HuggingFace / Forge RL](https://huggingface.co/blog/dcarpintero/design-patterns-for-building-agentic-workflows)

Define exactly what state crosses each agent boundary. Never pass full conversation history downstream.

**CS → CS handoff:** Pass only correlationId, resolved intent, extracted entities, and required session variables. Strip all raw conversation turns.
**CS → Foundry handoff:** Construct a typed payload — not a transcript. Define the payload schema in the Agent Design Template I/O contract for both agents.

**Gate:** Context scope (exact fields passed) must be defined in the Agent Design Template before build begins. Any "pass everything" spec blocks approval.

---

<a id="p10"></a>
### P10 · Sensitive state in side channel

**Source:** [HuggingFace / Neuro SAN](https://huggingface.co/blog/danyoung/neuro-san-is-all-you-need)

Credentials, PII, and API keys never appear in conversation context, system prompts, or Workflow YAML.

**CS:** All credentials stored as Power Platform connection references. API keys in Azure Key Vault, referenced via PA flow — never hardcoded in Topics.
**Foundry:** All credentials in Azure Key Vault. Reference via managed identity or environment variable — never in agent instructions or tool schema descriptions.

**Security review gate:** Agent Design Template security section lists every sensitive data element with confirmation it is in Key Vault or connection reference — not in any prompt or context variable.

---

<a id="p11"></a>
### P11 · Tool accuracy > 90% as deployment gate

**Source:** [HuggingFace / AssetOpsBench](https://huggingface.co/blog/ibm-research/assetopsbench-playground-on-hugging-face)

Back-calculate the required per-step accuracy from target end-to-end accuracy before setting the threshold.

**Calculation (required in Agent Design Template):** For a 3-hop chain targeting 98% end-to-end accuracy: required per-step = 98%^(1/3) = 99.3%. Document this calculation before build begins.

**Measurement:** Run the 50-sample eval harness with real tool calls (not mocked). Record pass rate per tool. Any tool below the required per-step accuracy blocks promotion — not just overall chain accuracy.

---

<a id="p12"></a>
### P12 · Human-in-the-loop for irreversible actions

**Source:** [DeepLearning.AI](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/) · [Microsoft CAF](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/governance-security-across-organization)

Classify every action as Advisory, Supervised, or Autonomous in the Agent Design Template before build begins.

**Advisory:** Agent presents recommendation; human decides. No PA approval flow needed.
**Supervised:** Agent executes; 15-minute undo window. PA flow writes action to an undo queue; background flow deletes after 15 minutes if not reversed.
**Autonomous:** Only valid for fully reversible, low-stakes, high-volume actions. Requires explicit sign-off in Agent Design Template.

**Gate:** Any irreversible write without Supervised or Advisory classification blocks go-live.

---

<a id="p13"></a>
### P13 · Use lowest complexity that reliably works

**Source:** [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns)

Escalation path: PA flow → CS Topic → CS specialist agent → Foundry agent. Document failure at each rung before escalating.

**Agent Design Template requirement:** Each escalation step records: (1) what was attempted at the lower rung, (2) the specific failure mode that prevented it working, (3) why the next rung resolves that failure. "Not tried" blocks approval at any rung.

---

<a id="p14"></a>
### P14 · Justify every agent boundary explicitly

**Source:** [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns)

Three valid reasons only: (a) security/compliance isolation required, (b) prompt complexity exceeds 8,000 characters, (c) tool count exceeds ~15.

**Agent Design Template:** Boundary justification section is mandatory. CoE reviewer verifies at least one valid reason is documented with evidence. Boundaries failing this check are rejected at intake — not deferred to build.

---

<a id="p15"></a>
### P15 · Single responsibility per agent

**Source:** [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) · [Microsoft CAF](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/governance-security-across-organization)

Every agent has a written job description and an explicit "does NOT handle" list in the Agent Design Template.

**Monthly intent overlap review:** Query App Insights for intents handled by more than one agent. Any overlap is flagged for consolidation review — not left as-is.

**CS:** Topic names map 1:1 to intents. If a topic grows beyond one coherent intent, split or reassign — not expand.

---

<a id="p16"></a>
### P16 · Right-size the model per agent role

**Source:** [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns)

**Gateway:** Classification model — smallest that passes the intent routing capability test.
**Domain agents:** Mid-tier reasoning model — handles multi-step planning and tool orchestration.
**Specialist agents:** Smallest model that passes the task-specific capability test (see [→ P38](#p38)).

**CS constraint:** CS locks to Microsoft-managed models — model selection unavailable. Document as named platform constraint per [→ P33](#p33). Route capability-sensitive agents to Foundry if model selection is required.
**Foundry:** Select model in agent configuration. Record model name and capability test result in Agent Design Template.

---

<a id="p17"></a>
### P17 · Validate output before passing downstream

**Source:** [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns)

Schema-validate every inter-agent handoff in code. A downstream agent receiving malformed input must fail loudly.

**CS:** Add a JSON parse and required-field check between agent handoff and next topic execution. Validation failure surfaces typed error to gateway — not inline retry.
**Foundry:** Add Pydantic or JSON Schema validation after each agent call. Validation failure routes to the error envelope path (see [→ P36](#p36)).

**Agent Design Template:** Output schema (required fields, types) must be defined for every agent in the I/O contract section.

---

<a id="p18"></a>
### P18 · Surface errors explicitly — never swallow failures

**Source:** [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns)

Every failure returns a typed envelope: `{errorCode, field, expectedFormat, retryable, correlationId}`. Silent nulls are not acceptable.

**CS:** Implement a dedicated error topic. All PA flow errors route there — not to a generic "something went wrong" message.
**Foundry:** Define the error schema in the agent's output type. Every tool call includes error handling that populates the envelope before returning.

**User experience:** User sees confirmed success with a reference number, or a named error with next steps. Never a silent failure.

---

<a id="p19"></a>
### P19 · Idempotency for all writes

**Source:** [Microsoft Tech Community](https://techcommunity.microsoft.com/blog/educatordeveloperblog/ai-didn%E2%80%99t-break-your-production-%E2%80%94-your-architecture-did/4482848)

Every write carries an idempotency key: `{correlationId}-{actionType}-{date}`. The target system rejects duplicate keys.

**CS:** Generate the idempotency key in the gateway topic. Pass it in every handoff. PA connector checks for existing key before writing.
**Foundry:** Pass idempotency key in tool call arguments. Tool implementation checks key against the target system before executing. Key stored in Redis for session duration.

**Agent Design Template:** Idempotency key pattern is a required I/O contract field for every write action.

---

<a id="p20"></a>
### P20 · Kill switch — disable tools without redeployment

**Source:** [Microsoft CAF](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/governance-security-across-organization)

Three granularity levels, all operable in <5 minutes without a code deployment:

**Agent-level:** CS — portal disable. Foundry — disable the agent endpoint. Confirm operable before go-live.
**Tool-level:** Feature flag per tool in App Configuration or Dataverse. PA flow checks flag before calling connector; if disabled, agent falls back to read-only mode (see [→ P35](#p35)).
**Fleet-level:** Maintenance broadcast — CS topic or Foundry system message that intercepts all sessions and routes to maintenance response.

**Kill switch runbook** is a mandatory pre-go-live deliverable for every domain.

---

<a id="p21"></a>
### P21 · Security trimming in every agent

**Source:** [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns)

Data entitlement enforced at the data layer — not in agent reasoning.

**CS:** All Dataverse queries use the authenticated user's security role — not a service account. SharePoint search includes user context for RLS.
**Foundry:** Azure AI Search queries include a security filter parameter. CosmosDB queries scoped to userId partition. Never retrieve a broader dataset and filter in agent reasoning.

**Security review gate:** Agent Design Template security section lists every data source and confirms entitlement enforcement is at the data layer for each.

---

<a id="p22"></a>
### P22 · Content safety at every layer

**Source:** [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns)

Three mandatory safety layers:

**Layer 1 — User input:** Azure Content Safety or CS built-in content moderation on every user message before it reaches the agent.
**Layer 2 — Tool response:** Prompt Shield or custom sanitisation on every tool response from a user-writable system before re-entering LLM context (see [→ P31](#p31)).
**Layer 3 — Agent output:** Content Safety output filter before every user-facing response.

**Configuration:** Safety filter sensitivity is configurable per domain — stricter for HR and Finance. Default level set in Agent Design Template before go-live. "Default" is not a configuration.

---

<a id="p23"></a>
### P23 · Scope agent authority to operations, not tools

**Source:** [Microsoft Tech Community](https://techcommunity.microsoft.com/blog/educatordeveloperblog/ai-didn%E2%80%99t-break-your-production-%E2%80%94-your-architecture-did/4482848)

Connector scope set at infrastructure layer — never declared in the system prompt.

**CS:** Power Platform connection references scoped to specific operations. Write operations capped at 10 per session per specialist agent via a session counter variable.
**Foundry:** Tool definitions include only specific API operations required. Azure API Management policy enforces operation-level scope.

**Agent Design Template:** Operations scope list is a mandatory security field. "Full API access" is not an acceptable entry.

---

<a id="p24"></a>
### P24 · Instrument all handoffs

**Source:** [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) · [Microsoft Azure Blog](https://azure.microsoft.com/en-us/blog/agent-factory-top-5-agent-observability-best-practices-for-reliable-ai/)

A single correlationId reconstructs the complete end-to-end trace across all agents, tools, and platforms.

**CS:** Log correlationId + agentId + intent + outcome to App Insights at every topic transition and PA flow completion.
**Foundry:** Pass correlationId as thread metadata. Log to App Insights at every tool call start and completion. Include correlationId in every A2A payload header to Agentforce or other external agents.

**Acceptance criterion:** Using only the correlationId, a developer can reconstruct the full end-to-end trace in App Insights within 5 minutes. Tested before go-live.

---

<a id="p25"></a>
### P25 · Automated evaluation in CI/CD

**Source:** [Microsoft Azure Blog](https://azure.microsoft.com/en-us/blog/agent-factory-top-5-agent-observability-best-practices-for-reliable-ai/)

Every solution import or prompt change triggers the 50-sample eval harness before promotion.

**CS:** GitHub Actions pipeline on PR merge to release branch triggers eval harness. Block environment promotion if pass rate <90%.
**Foundry:** Azure DevOps pipeline runs eval harness against the Foundry agent endpoint in the test environment. LLM-as-Judge scores each sample. Promotion blocked if judge score below threshold.

**Minimum Sprint 0 harness:** 50 golden samples, LLM-as-Judge rubric, routing accuracy test for every defined domain intent. See [→ P42](#p42) for build sequencing.

---

<a id="p26"></a>
### P26 · Evaluate continuously in production

**Source:** [Microsoft Azure Blog](https://azure.microsoft.com/en-us/blog/agent-factory-top-5-agent-observability-best-practices-for-reliable-ai/)

Weekly KPI review + monthly full eval re-run. Thresholds are not optional.

**Weekly KPIs:** Intent resolution rate, connector success rate per tool, session completion rate, HITL approval-without-review rate. Alert if any drops >5% week-over-week.
**Monthly full eval re-run:** Re-run the 50-sample harness against current production. If pass rate drops >5pp month-over-month, trigger a review ticket.

**Generalisation gap:** Track delta between CI eval pass rate and production outcome rate per domain. >10pp gap for two consecutive weeks triggers eval harness review (see [→ P47](#p47)).

---

<a id="p27"></a>
### P27 · Maintain agent inventory

**Source:** [Microsoft CAF](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/governance-security-across-organization)

Every agent registered in Agent 365 before deployment. Unregistered agents are treated as security incidents.

**Registration fields:** Agent name, named owner, domain, platform, go-live date, last review date, status (active/deprecated/decommission-pending).
**Monthly CoE review:** Flag agents unused for 30 days (decommission review) or unreviewed for 90 days (trigger owner review). Agents without a named owner are disabled pending assignment.

Agent 365 is the system of record. Spreadsheet inventories are not acceptable alternatives.

---

<a id="p28"></a>
### P28 · Externalize and version-control prompts

**Source:** [Azure Well-Architected Framework](https://learn.microsoft.com/en-us/azure/well-architected/ai/application-design)

System prompts are code. They live in version control — not hardcoded in Topics or Workflow YAML.

**CS:** Store system prompt in Azure App Configuration or Power Platform Environment Variables. Prompt change requires PR with reviewer sign-off + eval harness pass.
**Foundry:** Store agent instructions in Azure App Configuration. Foundry agent reads from App Configuration at startup. Prompt change triggers the CI/CD eval pipeline (see [→ P25](#p25)).

**Rollback:** Rollback is an App Configuration revision revert — no redeployment required. Confirm rollback path works before go-live.

---

<a id="p29"></a>
### P29 · Gateway routing must be measured and bounded at scale

**Source:** [Microsoft Copilot Studio — Agent Instructions](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-best-practices) · [MAST](https://huggingface.co/blog/ibm-research/itbenchandmast)

**Implementation note:** The CS instruction limit is 8,000 characters. At 8–12 domain agents, each requiring a precise routing description plus safety instructions, the gateway prompt approaches this ceiling. Route by intent taxonomy using a bounded decision tree — not open-ended LLM prompt matching.

**Implementation steps:**
- Externalise routing descriptions to a Dataverse lookup table; inject only the matched description at runtime
- Measure gateway prompt token consumption at 6, 9, and 12 domain agents during the pilot wave before committing to a routing pattern
- Define an intent taxonomy with bounded categories before build begins; routing accuracy collapses when the intent space is open-ended

---

<a id="p30"></a>
### P30 · Classify workflows as independent or dependent before routing

**Source:** [Microsoft Copilot Studio — Quotas](https://learn.microsoft.com/en-us/microsoft-copilot-studio/requirements-quotas) · [Microsoft MARA](https://microsoft.github.io/multi-agent-reference-architecture/docs/reference-architecture/Reference-Architecture.html)

**Implementation note:** CS sessions handle independent multi-domain calls correctly. Dependent cross-domain workflows — where steps have ordering constraints, shared state, or compensation requirements — require durable orchestration outside the CS session limit (60 min / 100 turns).

**Implementation steps:**
- Classify intents at the gateway as "independent multi-domain" vs. "dependent multi-domain" (needs orchestration)
- Power Automate workflow as orchestrator for dependent processes — no session limits, native compensation branches, PA approval flows for human gates
- Foundry Workflow (state machine) for compliance-grade processes requiring full audit trail
- Define a Workflow Registry in Dataverse parallel to the Agent Registry: named workflows with ordered steps, compensating actions, timeout policies, and correlationId prefix

---

<a id="p31"></a>
### P31 · Tool response sanitisation is mandatory for every user-writable system

**Source:** [OWASP LLM Top 10 — LLM02](https://owasp.org/www-project-top-10-for-large-language-model-applications/) · [Azure Content Safety — Prompt Shield](https://learn.microsoft.com/en-us/azure/ai-services/content-safety/concepts/jailbreak-detection)

**Implementation note:** Content safety at the output layer does not intercept malicious content embedded in tool responses from SharePoint, HRIS, CRM, or Jira. At 100 agents making tool calls across the enterprise, the indirect injection attack surface is enterprise-wide. This is a production blocker — not a recommendation.

**Implementation steps:**
- Add a response sanitisation step between tool response and LLM context re-entry — distinct from output content safety
- Add to Agent Design Template tool checklist: "Is the tool response from a user-writable system?" If yes, sanitisation required before go-live
- Evaluate Azure Content Safety Prompt Shield for indirect injection detection on tool responses
- Conduct a prompt injection red-team exercise against at least one domain pilot before Month 5 scale-out

---

<a id="p32"></a>
### P32 · Preview features must not be the sole production dependency

**Source:** [Azure AI Foundry — Memory Feature (Preview)](https://learn.microsoft.com/en-us/azure/ai-services/agents/how-to/tools/azure-ai-memory)

**Implementation note:** A 100-agent factory deployment targeting 6–12 months ships before preview features reach GA. API or behaviour changes in preview require rebuilding components across the entire agent fleet.

**Implementation steps:**
- Build Redis + Cosmos DB as the durable profile store; add Memory Feature as a retrieval layer only when GA
- Gate Foundry agents on Memory Feature only after GA or a committed preview SLA is confirmed
- Define a profile store abstraction so the underlying store can be swapped without rewriting agent logic
- Flag every preview dependency in the Agent Design Template with its documented GA fallback

---

<a id="p33"></a>
### P33 · Platform capability constraints documented against principles — never silently dropped

**Source:** [Azure Architecture Center — AI Agent Design Patterns](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) · [Microsoft Copilot Studio — Generative AI Features](https://learn.microsoft.com/en-us/microsoft-copilot-studio/nlu-gpt-overview)

**Implementation note:** CS locks to Microsoft-managed models — Principle 16 cannot be implemented for CS agents. This is a named platform constraint, not a principle abandonment. For every principle a platform cannot satisfy, the constraint must be documented.

**Implementation steps:**
- For each CS agent in the Agent Design Template, record any principle it cannot satisfy, the reliability gap that creates, and the mitigation strategy
- Route classification and extraction agents that would benefit from smaller models to Foundry instead of CS
- Monitor whether CS adds model selection capability; treat P16 as Foundry-only for now
- Silent non-compliance with a principle is never acceptable — the constraint must be named and the mitigation documented

---

<a id="p34"></a>
### P34 · LLM-as-Judge calibrated to >80% human annotation agreement before use as an automated gate

**Source:** [Sebastian Raschka — 4 Approaches to LLM Evaluation](https://magazine.sebastianraschka.com/p/llm-evaluation-4-approaches) · [MAST](https://huggingface.co/blog/ibm-research/itbenchandmast)

**Implementation note:** Known biases — positional, verbosity, self-enhancement — mean an uncalibrated judge produces systematically wrong quality signals, giving false confidence before production promotion.

**Implementation steps:**
- Calibration protocol: N≥200 samples per domain, annotated independently by at least 3 domain reviewers, targeting >80% Cohen's kappa agreement before the judge gates any production promotion
- Calibrate during the Month 3–4 pilot wave for the first 3 domains — before Month 5 scale-out begins
- Track judge model version in Agent Design Template; judge model update requires recalibration before use
- For domains where calibration cannot reach >80% agreement, fall back to ensemble judging (multiple model judges + 10% human spot-check)

---

<a id="p35"></a>
### P35 · Circuit breaker per tool — defined degradation fallback before go-live

**Source:** [Azure Architecture Center — AI Agent Design Patterns](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) · [Microsoft CAF — Governance and Security](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/governance-security-across-organization)

**Implementation note:** The 90% tool accuracy gate (P11) is a deployment gate, not a runtime guarantee. A failing tool in production continues to be called, accumulating errors and corrupting downstream state.

**Implementation steps:**
- Define a degradation mode per agent: what the agent does when tool error rate exceeds a configurable threshold (default: fall back to read-only or FAQ-only mode)
- Add error rate monitoring in App Insights per tool per agent with an alert threshold and automatic disablement trigger
- Align with P20 (kill switch): tool-level disablement must be operable in <5 minutes without redeployment
- Degradation fallback must be tested before go-live — not designed after an incident

---

<a id="p36"></a>
### P36 · Typed error envelope propagated upstream without reinterpretation — only the gateway translates

**Source:** [MAST — Failure Taxonomy](https://huggingface.co/blog/ibm-research/itbenchandmast) · [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns)

**Implementation note:** In a 4-hop chain, intermediate agents that reinterpret errors introduce hallucination risk — the error message gains confidence and loses accuracy at each hop.

**Implementation steps:**
- Define typed error envelope: `{ errorCode, errorMessage, retryable, agentId, stepId, correlationId }` in the Agent Design Template I/O contract for every agent
- Intermediate agents forward the envelope untouched — they never reinterpret it
- Only the gateway translates the error envelope into user-facing language
- Error envelope is a mandatory output field in every Agent Design Template

---

<a id="p37"></a>
### P37 · correlationId is an enforced contract — responses missing it are rejected, not forwarded

**Source:** [A2A Protocol](https://a2a-protocol.org/latest/) · [Microsoft MARA](https://microsoft.github.io/multi-agent-reference-architecture/docs/reference-architecture/Reference-Architecture.html)

**Implementation note:** In heterogeneous systems (CS + Foundry + Agentforce), correlationId propagation breaks silently without enforcement. A naming convention is not an enforced contract.

**Implementation steps:**
- Gateway-level validation: any agent response without a matching correlationId is treated as an error, not passed to the user
- Define correlationId as a required field in the A2A payload schema, not an optional header
- Naming convention: `{domainCode}-{agentId}-{sessionTimestamp}` — used consistently across all platforms
- correlationId propagation test is a go-live gate in the Agent Design Template Definition of Done — not a checkbox

---

<a id="p38"></a>
### P38 · Task-specific capability test required before model approval — benchmark scores are not sufficient

**Source:** [Andrej Karpathy — Intro to Large Language Models](https://www.youtube.com/watch?v=zjkBMFhNj_g) · [State of GPT (Build 2023)](https://www.youtube.com/watch?v=bZQun8Y4L2A)

**Implementation note:** LLMs have jagged capability profiles — a model scoring well on general benchmarks may fail on the specific subtask required. "Seems capable" is not an acceptable model selection justification.

**Implementation steps:**
- Run a mandatory pass/fail capability test: N=20 examples of the actual task type, scored against domain acceptance criteria, before a model is approved for a role
- Record capability test result (task type, N, pass rate, failure patterns) in Agent Design Template model selection section before build begins
- Capability test must use the actual tool schemas and real data representative of production inputs — not synthetic examples
- A model that passes the general benchmark but fails the task capability test is not approved for that role

---

<a id="p39"></a>
### P39 · Eval results report distribution statistics — not just median pass rate

**Source:** [Andrej Karpathy — Intro to Large Language Models](https://www.youtube.com/watch?v=zjkBMFhNj_g)

**Implementation note:** A single eval run is one sample from the output distribution — not evidence of reliability. A high-variance result is not production-ready even at high median pass rate.

**Implementation steps:**
- Require eval harness results to report: pass rate, variance across 50 samples, and failure mode distribution — not just overall pass/fail
- Flag any domain where variance across 50 samples exceeds 10% as not ready for production regardless of median outcome
- Run the same 50-sample harness across multiple model versions and prompt variants to measure distribution stability
- Variance is a mandatory reported metric alongside pass rate in every eval report

---

<a id="p40"></a>
### P40 · HITL review interface engineered as UX — a rubber-stamp approval flow is not a safety control

**Source:** [Andrej Karpathy — Autonomy Sliders](https://x.com/karpathy) · [DeepLearning.AI — Agentic Design Patterns](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/)

**Implementation note:** A HITL step with bad UX — too much information, no visible undo path, time pressure — is not a safety control; it is a rubber-stamp with liability.

**Implementation steps:**
- Explicit UX requirements for all Adaptive Card approval flows: action statement, reasoning summary, source data reference, and undo path must each be visible without scrolling
- Measure actual review time (target: <30 sec) and approval-without-review rate (target: <5%) per domain in the first 30 days post-launch
- For high-stakes domains (Finance, HR exceptions), require usability testing with at least 5 reviewers before go-live
- Review interface must show the consequence of approving — not just a confirm/reject button

---

<a id="p41"></a>
### P41 · The orchestration layer is the primary engineering surface

**Source:** [Andrej Karpathy — Software 3.0 / LLM OS](https://www.youtube.com/watch?v=LCEmiRjPEtQ)

**Implementation note:** Engineering effort is commonly misallocated toward model selection and prompt prose. Once a model passes the capability test gate (P38), additional model-selection effort delivers diminishing returns.

**Implementation steps:**
- Sprint allocation: orchestration layer design (context scope, tool argument standards, boundary justification, termination logic) receives the majority of engineering time
- Agent Design Template quality is the primary technical review artifact — not the agent's demo or system prompt readability
- Engineering checklist before any model or prompt work begins: context scope, tool argument standards, boundary justification, and termination logic must all be signed off first

---

<a id="p42"></a>
### P42 · Eval harness is a Sprint 0 deliverable — built before or alongside the agent, never retrofitted

**Source:** [Andrej Karpathy — Software 3.0](https://www.youtube.com/watch?v=LCEmiRjPEtQ)

**Implementation note:** An eval harness built after deployment has no gate for the first deployment, no regression signal for early iterations, and is designed to match the built agent rather than test it objectively.

**Implementation steps:**
- Eval harness sign-off is required before build begins — not before go-live
- Minimum Sprint 0 harness: (1) 50-example golden dataset with expected outputs, (2) LLM-as-Judge scoring rubric, (3) routing accuracy test for every defined domain intent
- Gate Sprint 1 build start on eval harness completion — not on design approval
- Golden dataset must be reviewed by a domain subject matter expert — not written only by the build team

---

<a id="p43"></a>
### P43 · Failure mode profile characterised per agent before deployment — not discovered in production

**Source:** [Ilya Sutskever — NeurIPS 2024](https://www.youtube.com/watch?v=1yvBqasHLZs) · [MAST](https://huggingface.co/blog/ibm-research/itbenchandmast)

**Implementation note:** The 90% accuracy gate catches average performance but not the shape of the failure surface. Failure rate is not uniform across input types.

**Implementation steps:**
- Add a failure mode characterisation section to the Agent Design Template: document input patterns most likely to produce failures per domain (ambiguous intent, unusual date formats, edge-case entities, maximum argument complexity)
- Complete failure mode analysis on the first 50-sample eval run — before build begins, not after deployment
- Use failure mode documentation as the basis for selecting adversarial eval inputs in the CI/CD harness (P25)
- Failure patterns from production monitoring fed back into the eval harness quarterly

---

<a id="p44"></a>
### P44 · Safety infrastructure complete before any autonomous capability goes live

**Source:** [Ilya Sutskever — SSI Founding Principles](https://ssi.inc/)

**Implementation note:** Safety infrastructure items that are "planned" or "in progress" at go-live time mean capability has outrun safety.

**Mandatory pre-go-live checklist — all four confirmed, not planned:**
- (1) Kill switch tested and confirmed (P20)
- (2) HITL flows user-tested with real reviewers (P40)
- (3) Content safety layers active and configured for the domain (P22)
- (4) Eval harness passing at gate threshold (P25)

Safety infrastructure completion is a separate milestone from capability completion in the delivery roadmap.

---

<a id="p45"></a>
### P45 · Maximum reasoning depth bounded as an explicit architectural parameter per agent role

**Source:** [Ilya Sutskever — NeurIPS 2024](https://www.technology.org/2024/12/16/ilya-sutskever-the-more-ai-reasons-the-more-unpredictable-it-becomes/) · [Dwarkesh Patel Interview 2025](https://www.dwarkesh.com/p/ilya-sutskever-2)

**Implementation note:** An agent with open-ended reasoning scope can generate chains of arbitrary length — producing outputs proportionally harder to validate.

**Implementation steps:**
- Define maximum reasoning depth in Agent Design Template per role: Gateway (reasoning disabled — classification only); Domain agents (max N steps, defined per domain); Specialist agents (bounded to specific subtask); Foundry planning nodes (explicit `max_steps` in Workflow YAML)
- Prefer models with bounded, verifiable reasoning for Autonomous-tier agent roles
- Add reasoning scope violation (agent exceeds defined max steps) as a monitored metric in App Insights

---

<a id="p46"></a>
### P46 · Adversarial alignment test set required before scale-out

**Source:** [Ilya Sutskever — Weak-to-Strong Generalization](https://openai.com/index/weak-to-strong-generalization/)

**Implementation note:** Standard golden-set evals measure performance on the designed-for distribution. Apparent alignment on curated test sets can coexist with misalignment on out-of-distribution inputs.

**Implementation steps:**
- Build an adversarial alignment test set for each domain before scale-out: inputs probing authority boundary violations, scope creep, instruction override attempts ("ignore your previous instructions"), and cross-agent impersonation
- Include adversarial alignment tests as a mandatory section in the CI/CD eval harness — not only in the pre-launch red-team exercise
- Conduct a structured alignment red-team against at least one domain pilot before Month 5 scale-out

---

<a id="p47"></a>
### P47 · Benchmark-to-deployment generalisation gap explicitly measured and tracked

**Source:** [Ilya Sutskever — NeurIPS 2024](https://dlyog.com/papers/one_internet_v1) · [Dwarkesh Patel Interview 2025](https://www.dwarkesh.com/p/ilya-sutskever-2)

**Implementation note:** A growing gap between CI eval harness performance and production performance is a concrete signal that the eval harness is no longer representative — but without explicitly tracking this delta, the signal is invisible until degradation is severe.

**Implementation steps:**
- Add a "generalisation gap" metric to the production monitoring dashboard: delta between CI eval harness pass rate and production outcome rate on the same task categories
- Gap threshold: CI eval pass rate exceeding production outcome rate by >10pp for two consecutive weeks triggers an eval harness review
- Review the generalisation gap in the monthly eval re-run alongside absolute performance metrics

---

<a id="p48"></a>
### P48 · Foundation model updates trigger full eval re-run — model version is a tracked dependency

**Source:** [Ilya Sutskever — SSI / Dwarkesh Patel Interview](https://www.dwarkesh.com/p/ilya-sutskever-2)

**Implementation note:** CS and Foundry update underlying foundation models without breaking API changes — but agent behaviour can change silently.

**Implementation steps:**
- Define model update response policy: when the underlying foundation model is updated, automatically trigger the full eval harness (P25) for all domains using that model
- Add model version as a tracked field in Agent Design Template and Agent 365 inventory — a model version change has the same revalidation requirement as a prompt change
- Subscribe to Microsoft model update announcements; integrate with CI/CD so a model update notification triggers a scheduled eval run
- Target: eval harness re-run completed within 48 hours of a model update notification

---

<a id="p49"></a>
### P49 · Fine-tuning approved only after demonstrable in-context performance gap

**Source:** [Sebastian Raschka — Using and Finetuning Pretrained Transformers](https://magazine.sebastianraschka.com/p/using-and-finetuning-pretrained-transformers) · [Practical Tips for Finetuning LLMs](https://magazine.sebastianraschka.com/p/practical-tips-for-finetuning-llms)

**Implementation note:** Fine-tuning without a documented in-context performance gap creates maintenance burden without proportional reliability gains.

**Implementation steps:**
- Add a fine-tuning decision section to Agent Design Template: (1) in-context pass rate vs. capability test threshold (P11), (2) performance gap, (3) available domain data, (4) revalidation plan
- Minimum domain data requirement before approval: static datasets benefit from a single training pass — multi-epoch fine-tuning without fresh data typically degrades performance
- Fine-tuning decisions reviewed at the Agent Design Template gate — not made independently by the build team
- LoRA configuration (rank r, alpha, target layers) must be documented if fine-tuning is approved

---

<a id="p50"></a>
### P50 · RAG retrieval quality evaluated independently from generation quality

**Source:** [Sebastian Raschka — Beyond Standard LLMs](https://magazine.sebastianraschka.com/p/beyond-standard-llms)

**Implementation note:** A system that retrieves poor documents but generates fluent text passes generation quality evals while producing confident, coherent, but incorrect responses.

**Implementation steps:**
- Add RAG-specific evaluation metrics for all agents using retrieval: (1) retrieval recall@K, (2) retrieval precision, (3) grounding rate (assertions traceable to retrieved documents), (4) hallucination rate
- Track retrieval metrics independently from generation metrics in CI/CD eval harness and production monitoring
- Minimum retrieval quality threshold before go-live: recall@3 >80% (adjust based on pilot data)
- Improving the generator without measuring retrieval is misallocated engineering effort

---

<a id="p51"></a>
### P51 · Model validated for multi-turn coherence before approval for agentic roles

**Source:** [Sebastian Raschka — State of LLMs 2025](https://magazine.sebastianraschka.com/p/state-of-llms-2025) · [Beyond Standard LLMs](https://magazine.sebastianraschka.com/p/beyond-standard-llms)

**Implementation note:** Linear attention architectures fail on multi-turn reasoning tasks. A model can score well on single-turn benchmarks while failing on multi-turn coherence. Architecture-level concern — not a model size concern.

**Implementation steps:**
- Add to Agent Design Template model selection section: (1) multi-turn coherence test at maximum expected session length (not average); (2) structured output/tool call validation with the specific tool schemas for that domain; (3) reasoning depth test at maximum chain length
- Flag linear attention architectures as requiring additional multi-turn validation before agentic approval
- Update the approved model list quarterly as the model landscape evolves

---

<a id="p52"></a>
### P52 · Eval methodology reviewed bi-annually

**Source:** [Sebastian Raschka — LLM Research Papers 2024](https://magazine.sebastianraschka.com/p/llm-research-papers-the-2024-list)

**Implementation note:** Continuous production evaluation catches performance drift against a fixed methodology. It does not catch methodology drift — the harness becoming unrepresentative as the input distribution shifts or better evaluation methods emerge.

**Implementation steps:**
- Add a bi-annual "eval methodology review" to the governance calendar: (1) Is the golden dataset still representative? (2) Is the judge model still best-in-class? (3) Should new methods be incorporated? (4) Have production failures exposed eval blind spots?
- Treat the eval methodology review as a governance deliverable — owned by the CoE eval lead, not individual domain teams
- Use the generalisation gap metric (P47) as an early warning signal triggering an unscheduled review if it exceeds the defined threshold

---

<a id="p53"></a>
### P53 · Open-weight vs. hosted API decided by evidence

**Source:** [Sebastian Raschka — Finetuning Large Language Models](https://magazine.sebastianraschka.com/p/finetuning-large-language-models)

**Implementation note:** The architecture defaults to hosted API models but does not preclude open-weight deployment via Azure ML. The decision must follow a structured gate — not team preference.

**Implementation steps:**
- Add an open-weight vs. hosted API decision gate to Agent Design Template: (1) data residency requirements preclude hosted API? (2) volume high enough that Azure ML hosting costs are lower? (3) specific fine-tuning requirement requires model weight access? If any is yes, evaluate open-weight via Azure ML
- Define a governance process for open-weight deployments: model version, source, licence compliance, and security review documented before approval
- Identify 1–2 high-volume specialist domains in Month 5–8 scale-out as candidates for open-weight evaluation; compare reliability, cost, and maintenance burden against hosted API baseline

---

<a id="p54"></a>
### P54 · Topology selected from a defined taxonomy — ad-hoc topology is an anti-pattern

**Source:** [Google ADK — Multi-Agent Patterns](https://developers.googleblog.com/developers-guide-to-multi-agent-patterns-in-adk/) · [IBM Guide to Agentic AI Systems](https://www.ibm.com/think/architectures/patterns/agentic-ai)

**Implementation note:** Agents assembled without a deliberately chosen topology produce unpredictable reliability at scale. Ad-hoc topology is not auditable, not testable, and not adjustable without re-architecture.

**Implementation steps:**
- Select topology from the defined taxonomy: hierarchical (this architecture's default), sequential, parallel, handoff, group-chat, or Magentic-supervisor. Document choice and justification in Agent Design Template
- A topology change is a design-level change requiring Architecture review — not an implementation decision
- Record the known reliability characteristics of the chosen pattern in the Agent Design Template
- Topology not in the taxonomy requires Architecture Board approval before proceeding

---

<a id="p55"></a>
### P55 · A2A and MCP are the interoperability layer — no proprietary lock-in at the orchestration level

**Source:** [Microsoft Azure Blog — MCP and A2A](https://azure.microsoft.com/en-us/blog/agent-factory-connecting-agents-apps-and-data-with-new-open-standards-like-mcp-and-a2a/) · [IBM Developer — MCP Architecture Patterns](https://developer.ibm.com/articles/mcp-architecture-patterns-ai-systems/)

**Implementation note:** Proprietary point-to-point integrations accumulate coupling that cannot be replaced at scale.

**Implementation steps:**
- A2A protocol for all cross-platform agent-to-agent calls: CS → Foundry, Foundry → Agentforce, and any external agent
- MCP for tool integration across agent runtimes — MCP servers can be reused across agents and platforms
- Platform-native connectors acceptable within a platform boundary; cross-platform calls must use A2A
- Agent Design Template must declare the A2A endpoint for every agent exposed to cross-platform calls

---

<a id="p56"></a>
### P56 · Memory, tools, and planning are distinct architectural layers with independent design decisions

**Source:** [HuggingFace — Building Blocks of Agentic Systems](https://huggingface.co/blog/Kseniase/buildingblocks) · [IBM — What Is Agentic Architecture](https://www.ibm.com/think/topics/agentic-architecture)

**Implementation note:** Conflating memory, tools, and planning in a single prompt produces agents with undefined failure boundaries.

**Implementation steps:**
- Memory layer: define what state is preserved, where it is stored (Dataverse / Redis / CosmosDB per Section 7), and when it is read and written — independent of tool or planning decisions
- Tool layer: define connector schemas, argument contracts, and error envelopes (P6, P18) — independent of memory or planning
- Planning layer: define orchestration pattern, topology (P54), and step sequencing — independent of storage or tool selection
- Each layer must be defined in the Agent Design Template before build begins. Conflation of layers is a review rejection criterion

---

<a id="p57"></a>
### P57 · Selective reflection applied to quality-sensitive outputs only

**Source:** [DeepLearning.AI — Agentic Design Patterns Part 2: Reflection](https://www.deeplearning.ai/the-batch/agentic-design-patterns-part-2-reflection/)

**Implementation note:** Universal application of reflection patterns adds latency and LLM cost to every output without proportional quality benefit.

**Implementation steps:**
- Classify each output type in Agent Design Template: reflection required, reflection optional, or reflection disabled
- Reflection required: high-consequence irreversible outputs where failure mode is detectable by a second LLM pass (e.g. contract generation, complex data transformations)
- Reflection disabled: structured tool calls, lookup operations, classification outputs — LLM reflection adds no quality signal here
- Reflection classification is a design decision reviewed at the Agent Design Template gate — not a default-on behaviour

---

<a id="p58"></a>
### P58 · Governance infrastructure established before scaling — cannot be retrofitted

**Source:** [Microsoft CAF — Build Secure Process](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/build-secure-process) · [Microsoft ISE Dev Blog — Multi-Agent at Scale](https://devblogs.microsoft.com/ise/multi-agent-systems-at-scale/)

**Implementation note:** Governance added after an agent estate is scaled requires re-architecting each deployed agent.

**Implementation steps:**
- Agent registry (Agent 365) operational and all pilot agents registered before Month 5 scale-out
- Independent ALM pipelines per domain: Dev → Test → Prod promotion with eval harness gate
- Domain isolation confirmed: agents in different domains cannot share session state or call each other's tools directly
- CoE certification checklist: all governance infrastructure confirmed operational before Month 5 scale-out is approved

---

<a id="p59"></a>
### P59 · Factory build process established before scaling to 100+ agents

**Source:** [Microsoft CAF — Process to Build Agents](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/build-secure-process) · [Azure Blog — Agent Factory](https://azure.microsoft.com/en-us/blog/agent-factory-the-new-era-of-agentic-ai-common-use-cases-and-design-patterns/)

**Implementation note:** Ad-hoc per-agent builds cannot scale to 100+ agents. Factory infrastructure must be built and validated on the pilot wave before Month 5.

**Implementation steps:**
- Agent templates that domain teams can clone without platform expertise: one template per platform (CS, Foundry) covering standard connectors, ALM pipeline, eval harness, and Agent Design Template pre-filled with platform defaults
- Standard ALM pipelines: GitHub Actions (CS) and Azure DevOps (Foundry) for Dev → Test → Prod with eval gate
- Agent Design Template as the mandatory build gate: no agent build starts without a completed and CoE-reviewed template
- CoE as factory operator: defined intake process (submission → review → approval → build), target throughput 8–12 agents/month from Month 5

---

<a id="p60"></a>
### P60 · Inference cost defined as an acceptance criterion alongside quality — not optimised post-deployment

**Source:** [Azure Well-Architected Framework](https://learn.microsoft.com/en-us/azure/well-architected/ai/application-design)

**Implementation note:** Treating inference cost as an afterthought produces architectures that are correct in design but undeployable at production scale due to token costs, latency limits, or budget overruns.

**Implementation steps:**
- Add to Agent Design Template before build begins: expected call volume per day, expected context length per turn, target latency (p50/p95), and estimated monthly inference cost at target scale
- If estimated cost exceeds budget before go-live, the model tier or context strategy must change — not the budget
- Compare token cost and latency across available model tiers in the Azure AI Foundry model catalog before committing to a model tier per agent role

---

<a id="p61"></a>
### P61 · Lightweight input classifier routes to appropriate model tier before LLM invocation

**Source:** [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns)

**Implementation note:** Using a frontier reasoning model for simple extraction or formatting tasks wastes cost without quality benefit.

**Implementation steps:**
- Design a lightweight input classifier (Gateway-level, below the LLM tier): routine/extraction → small model, standard generation → medium model, complex reasoning → large model
- The classifier must be near-zero cost — rule-based or a small classification model, not a full transformer
- Measure the input distribution for each domain agent: what percentage of inputs fall into each complexity class? Use this to size the model tier selection investment
- Gate: classifier must not misclassify more than 2% of complex inputs as routine (false downgrade rate <2%)

---

<a id="p62"></a>
### P62 · Agent interfaces designed for capability discovery — not hardcoded to current model limitations

**Source:** [Microsoft CAF — Process to Build Agents](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/build-secure-process)

**Implementation note:** Architectures built around today's model limitations become binding constraints when models improve. Current limitation workarounds must be documented so they can be removed cleanly.

**Implementation steps:**
- Document current-model limitation workarounds explicitly in Agent Design Template: instruction length limits, reasoning depth caps, tool-calling reliability workarounds — these are temporary, not permanent design decisions
- Design agent interfaces (tool signatures, authority scopes, I/O contracts) to be model-agnostic — they must not assume the capability level of the current underlying model
- Use capability discovery patterns: agents declare what they can do through structured metadata in the Agent Design Template, not hardcoded capability lists embedded in routing logic
- Review Agent Design Template capability declarations quarterly; remove limitation workarounds as models improve

---

*Last reviewed: March 2026 · Owner: Vineet Kaul*
