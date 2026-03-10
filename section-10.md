---
layout: default
title: "⚠️ 9. Design Considerations"
parent: Enterprise Multi-Agent Architecture
nav_order: 15
---

## 9. ⚠️ Design Considerations

> Open items identified during architecture review. To be resolved before production scale-out.

---

### DC-1 · Gateway Routing Capacity at Scale

**Source:** [Microsoft Copilot Studio — Agent Instructions and Best Practices](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-best-practices) · [MAST — Failure Taxonomy for Multi-Agent Systems](https://huggingface.co/blog/ibm-research/itbenchandmast)

**Gap:** The CS instruction limit is 8,000 characters. At 8–12 domain agents, each requiring a precise routing description plus system safety instructions, the gateway prompt approaches this ceiling. The star topology anti-pattern section correctly warns that routing accuracy collapses as scope grows — but the gateway itself faces this exact problem.

**Options to evaluate:**
- Route by intent taxonomy using a structured CS topic with a bounded decision tree — not open-ended LLM prompt matching
- Externalise routing descriptions to a Dataverse lookup table; inject only the matched description at runtime
- Measure token consumption of the gateway prompt at 6, 9, and 12 domain agents before committing to a pattern

---

### DC-2 · Dependent Cross-Domain Workflows

**Source:** [Microsoft Copilot Studio — Quotas, Limits, and Configuration](https://learn.microsoft.com/en-us/microsoft-copilot-studio/requirements-quotas) · [Microsoft Multi-Agent Reference Architecture](https://microsoft.github.io/multi-agent-reference-architecture/docs/reference-architecture/Reference-Architecture.html)

**Gap:** The architecture handles independent multi-domain calls correctly (leave + Salesforce: gateway calls HR domain, calls Agentforce, composes — no issue). The gap is specifically *dependent* cross-domain workflows: where steps have ordering constraints, shared state, compensation requirements, or duration exceeding the CS session limit (60 min / 100 turns).

**Example:** Employee offboarding — IT access revocation must complete before HR records close; Finance final payroll depends on HR confirmation; if Finance fails, HR records must be reopened across an already-closed CS session.

**Options to evaluate:**
- Classify intents at the gateway as "independent multi-domain" (current pattern) vs. "dependent multi-domain" (needs orchestration)
- Power Automate workflow as orchestrator for dependent processes — no session limits, native compensation branches, PA approval flows for human gates
- Foundry Workflow (state machine) for compliance-grade processes requiring CMK or full audit trail
- Define a Workflow Registry in Dataverse (parallel to Agent Registry): named workflows with ordered steps, compensating actions, timeout policies, and correlationId prefix per workflow

---

### DC-3 · Indirect Prompt Injection

**Source:** [OWASP LLM Top 10 — LLM02: Indirect Prompt Injection](https://owasp.org/www-project-top-10-for-large-language-model-applications/) · [Azure AI Content Safety — Prompt Shield](https://learn.microsoft.com/en-us/azure/ai-services/content-safety/concepts/jailbreak-detection) · [Karpathy — Prompt injection (X / various)](https://x.com/karpathy)

**Gap:** Content safety is applied at user input and agent output, but not specifically at tool *response* ingestion. At 100 agents making tool calls against enterprise systems (SharePoint, HRIS, CRM, Jira), a malicious document or record can embed adversarial instructions that hijack the agent's next action — without triggering output-layer content safety.

**Options to evaluate:**
- Add a response sanitisation step between tool response and LLM context re-entry — distinct from output content safety
- Add to the Agent Design Template tool checklist: "Is the tool response from a user-writable system? If yes, sanitisation required."
- Evaluate Azure Content Safety prompt shield for indirect injection detection on tool responses

---

### DC-4 · Preview Dependency on Foundry Memory Feature

**Source:** [Azure AI Foundry — Memory Feature (Preview)](https://learn.microsoft.com/en-us/azure/ai-services/agents/how-to/tools/azure-ai-memory) · [Azure AI Foundry — What's New](https://learn.microsoft.com/en-us/azure/ai-foundry/whats-new)

**Gap:** Cross-session user profile storage for Foundry agents is built on Memory Feature (preview). A 100-agent factory deployment targeting 6–12 months ships before GA is confirmed. API or behaviour changes in preview would require rebuilding the user profile layer across all Foundry agents.

**Options to evaluate:**
- Build with Redis + Cosmos DB as the durable profile store now; add Memory Feature as a retrieval layer when GA
- Gate Foundry agents on Memory Feature only after it reaches GA or a committed preview SLA is confirmed
- Define a profile store abstraction so the underlying store can be swapped without rewriting agent logic

---

### DC-5 · Model Selection Locked in Copilot Studio

**Source:** [Azure Architecture Center — AI Agent Design Patterns](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) · [Microsoft Copilot Studio — Generative AI Features](https://learn.microsoft.com/en-us/microsoft-copilot-studio/nlu-gpt-overview)

**Gap:** Architectural Principle 16 recommends right-sizing the model per agent role (smaller models for classification, extraction, formatting). CS — recommended for 80–90% of agents — locks to Microsoft-managed models with no selection control. The principle is correct; the platform does not support it for the majority of agents.

**Options to evaluate:**
- Explicitly document this as a CS tradeoff in the platform selection section
- Route classification and extraction agents that would benefit from smaller models to Foundry instead of CS
- Monitor whether CS adds model selection capability; treat Principle 16 as Foundry-only for now

---

### DC-6 · LLM-as-Judge Calibration

**Source:** [Sebastian Raschka — Understanding the 4 Main Approaches to LLM Evaluation](https://magazine.sebastianraschka.com/p/llm-evaluation-4-approaches) · [MAST — Failure Taxonomy for Multi-Agent Systems](https://huggingface.co/blog/ibm-research/itbenchandmast)

**Gap:** LLM-as-Judge is recommended as the evaluation gate for routing accuracy and response quality. Known biases (positional, verbosity, self-enhancement) mean the judge can produce systematically wrong quality signals — giving false confidence before production promotion — unless calibrated against human annotations first.

**Options to evaluate:**
- Establish human annotation agreement threshold (e.g., >80% agreement) as a prerequisite before LLM-as-Judge is used as an automated gate
- Calibrate the judge during the Month 3–4 pilot wave, not after Month 5 scale-out begins
- Use ensemble judging (multiple model judges + human spot-check) for high-stakes routing decisions

---

### DC-7 · No Circuit Breaker or Tool Degradation Pattern

**Source:** [Azure Architecture Center — AI Agent Design Patterns](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) · [Microsoft CAF — Governance and Security for AI Agents](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/governance-security-across-organization)

**Gap:** A 90% tool accuracy threshold exists as a deployment gate but there is no defined behaviour for tools that degrade below threshold after they are in production. A failing tool will continue to be called, accumulating errors and potentially corrupting downstream state across multiple agents.

**Options to evaluate:**
- Define a degradation mode per agent: fallback to read-only / FAQ-only if agentic tools fail above a configurable error rate threshold
- Add error rate monitoring in App Insights per tool per agent with an alert threshold and automatic tool disablement trigger
- Leverage Principle 20 (kill switch) explicitly: document the per-tool disablement procedure as part of every Agent Design Template entry

---

### DC-8 · Error Propagation Through the Agent Hierarchy

**Source:** [MAST — Failure Taxonomy for Multi-Agent Systems](https://huggingface.co/blog/ibm-research/itbenchandmast) · [Azure Architecture Center — AI Agent Design Patterns](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns)

**Gap:** In a 4-hop chain (Gateway → Domain Agent → Specialist Agent → Tool), there is no defined contract for how a tool failure propagates back to the user with enough context to be meaningful. Intermediate agents typically either swallow the error (wrong) or re-interpret it (introduces hallucination risk).

**Options to evaluate:**
- Define a typed error envelope passed upstream untouched: `{ errorCode, errorMessage, retryable, agentId, stepId, correlationId }`
- Only the gateway translates the error envelope into user-facing language — intermediate agents forward, never interpret
- Add error envelope to the Agent Design Template I/O contract section as a mandatory output field

---

### DC-9 · correlationId Enforcement

**Source:** [A2A Protocol](https://a2a-protocol.org/latest/) · [Microsoft Multi-Agent Reference Architecture](https://microsoft.github.io/multi-agent-reference-architecture/docs/reference-architecture/Reference-Architecture.html) · [MAST — Failure Taxonomy](https://huggingface.co/blog/ibm-research/itbenchandmast)

**Gap:** The correlationId pattern (`{domainCode}-{agentId}-{sessionTimestamp}`) is a naming convention, not an enforced contract. In heterogeneous systems (CS + Foundry + Agentforce), propagation breaks silently — there is no validation step that rejects responses missing the expected correlationId.

**Options to evaluate:**
- Add gateway-level validation: any agent response without a matching correlationId is treated as an error, not passed to the user
- Define correlationId as a required field in the A2A payload schema, not an optional header
- Add correlationId propagation test to the Agent Design Template Definition of Done checklist (already partially there — make it a gate, not a checkbox)

---

---

### DC-10 · Capability Audit Per Task — Not Just Model Tier

**Source:** Andrej Karpathy — [Intro to Large Language Models](https://www.youtube.com/watch?v=zjkBMFhNj_g) · [State of GPT (Microsoft Build 2023)](https://www.youtube.com/watch?v=bZQun8Y4L2A)

**Gap:** Architectural Principle 16 recommends right-sizing the model per agent role (Gateway mid-tier, specialist smallest-that-passes). This is necessary but insufficient. Karpathy's **jagged intelligence** observation: LLMs have uneven capability profiles — a model that scores well on general benchmarks may fail on the specific subtask you need. Declaring a model tier in the Agent Design Template is not the same as validating that the selected model can reliably perform the specific operation required.

**Options to evaluate:**
- Add a mandatory pass/fail capability test for the specific subtask to the Agent Design Template model selection section — not just a model tier declaration
- Define minimum capability test: N=20 examples of the actual task type, scored against the acceptance criteria for that domain
- "Seems capable" is not an acceptable model selection justification — capability test result must be recorded in the Agent Design Template before build begins

---

### DC-11 · Test Prompt Distributions, Not Single Runs

**Source:** Andrej Karpathy — [Intro to Large Language Models](https://www.youtube.com/watch?v=zjkBMFhNj_g) · [X / Twitter (2024–25)](https://x.com/karpathy)

**Gap:** Evals are defined (Principle 25: 50-sample harness) but the framing may not be explicit enough. Karpathy: "The same prompt can produce different outputs depending on context, model version, and temperature — you must characterise the full output distribution." A single successful run, or even a single pass of the eval harness, is not evidence of reliability — it is evidence of one sample from the distribution.

**Options to evaluate:**
- Require eval harness results to report distribution statistics — pass rate, variance, failure mode distribution — not just overall pass/fail
- Flag any domain where variance across 50 samples is high (>10% variance in pass rate) as not ready for production regardless of median outcome
- Use the same 50-sample harness across multiple model versions and prompt variants to measure distribution stability, not just absolute performance

---

### DC-12 · HITL Must Be Engineered as UX, Not Just Inserted as a Step

**Source:** Andrej Karpathy — [Autonomy sliders and AI agents (X / various 2024)](https://x.com/karpathy) · [DeepLearning.AI — Agentic Design Patterns](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/)

**Gap:** Three-tier action classification (Advisory / Supervised / Autonomous) is defined with PA Adaptive Card approval flows. The gap is in UX design quality: Karpathy's "autonomy sliders" framing argues that human oversight is only real if the review interface makes meaningful review possible in the available time. A HITL step with bad UX — too much information, no visible undo path, time pressure — is not a safety control; it is a rubber-stamp with liability.

**Options to evaluate:**
- Define explicit UX requirements for all Adaptive Card approval flows: action statement, reasoning summary, source data reference, and undo path must each be visible without scrolling
- Add to Agent Design Template Definition of Done: measure actual review time (target: <30 sec) and approval-without-review rate (target: <5%) per domain in the first 30 days post-launch
- For high-stakes domains (Finance, HR exceptions), require usability testing of the approval flow with at least 5 reviewers before go-live

---

### DC-13 · The Orchestration Layer Is the Primary Engineering Surface

**Source:** Andrej Karpathy — [LLM OS framing — Lex Fridman Podcast](https://www.youtube.com/watch?v=cdiD-9MMpb0) · [Context Engineering (X / various 2024–25)](https://x.com/karpathy)

**Gap:** Engineering effort may be misallocated toward model selection and prompt prose quality rather than orchestration quality. Karpathy's combined "context engineering" and "LLM OS" thesis: the model is largely a commodity; the orchestration layer — context construction, topology design, tool argument contracts, compaction strategy, and boundary justification — is where system performance is determined. Effort invested in model selection beyond the capability test gate is subject to diminishing returns.

**Options to evaluate:**
- Explicitly calibrate sprint allocation: orchestration layer (Agent Design Template, context hygiene, tool argument standards, boundary criteria) should receive the majority of design effort; model selection and prompt prose should receive the remainder
- Treat Agent Design Template quality as the primary technical review artifact — not the agent's demo or the system prompt's readability
- Create an explicit engineering checklist: context scope, tool argument standards, boundary justification, and termination logic must all be signed off before any model or prompt work begins

---

### DC-14 · Evals Must Be Built Before or Alongside the Agent — Not After

**Source:** Andrej Karpathy — [Software 3.0 — YC AI Startup School](https://www.youtube.com/watch?v=LCEmiRjPEtQ) · [Evals (X / various 2024–25)](https://x.com/karpathy)

**Gap:** Principle 25 (automated evaluation in CI/CD) assumes an eval harness exists. The gap is in sequencing: if the eval harness is built after the agent is deployed, the first deployment has no gate, the early iterations have no regression signal, and the harness quality is lower because it is designed to match the built agent rather than to test it objectively. Karpathy: "The eval harness is a Sprint 0 deliverable, not a post-build retrofit."

**Options to evaluate:**
- Add to Agent Design Template Definition of Done: eval harness sign-off is required before build begins, not before go-live
- Define minimum eval harness for Sprint 0: (1) golden dataset of 50 representative user inputs with expected outputs, (2) LLM-as-Judge scoring rubric, (3) routing accuracy test for every defined domain intent
- Gate Sprint 1 build start on eval harness completion — not on design approval

---

### DC-15 · Prompt Injection Is the Primary Agentic Security Surface

**Source:** Andrej Karpathy — [Prompt injection (X / various)](https://x.com/karpathy) · [OWASP LLM Top 10 — LLM02: Prompt Injection](https://owasp.org/www-project-top-10-for-large-language-model-applications/)

**Gap:** DC-3 (Indirect Prompt Injection) identifies the risk and proposes response sanitisation. The gap is in priority and scope: Karpathy identifies prompt injection as the *primary* security surface for agentic systems, not a secondary design consideration. LLMs have no reliable separation between instruction and content; every external data source in the context window is a potential injection vector. At 100 agents calling SharePoint, HRIS, CRM, Jira, and email systems, the injection attack surface is enterprise-wide.

**Options to evaluate:**
- Elevate DC-3 from a design consideration to a production blocker: no domain goes live without a documented response sanitisation layer for all tool responses from user-writable systems
- Evaluate Azure Content Safety prompt shield specifically for tool response sanitisation — not just for user input and agent output
- Add to Agent Design Template tool checklist a mandatory field: "Is the tool response from a user-writable system?" If yes, sanitisation is required before response enters LLM context — this must be in the Definition of Done, not a recommendation
- Conduct a prompt injection red-team exercise against at least one domain pilot before scale-out begins

---

### DC-16 · Characterise the Jagged Frontier Per Agent Before Deployment

**Source:** Ilya Sutskever — NeurIPS 2024 / Dwarkesh Patel Interview 2025

**Gap:** The 90% tool accuracy gate (Principle 11) catches average performance but not the shape of the failure surface. Sutskever's "jagged frontier" thesis: LLMs are superhuman on benchmarks yet fail in elementary ways in deployment — the capability gap is not uniform, it is jagged. Every agent has a unique jagged profile that differs by task type, input distribution, and context structure. A pass-rate metric tells you how many inputs fail; it does not tell you which input patterns consistently trigger failure.

**Options to evaluate:**
- Add a "failure mode characterisation" section to the Agent Design Template: beyond overall pass rate, document the input patterns most likely to produce failures for each domain (ambiguous intent, unusual date formats, edge-case entity names, maximum argument complexity)
- Require failure mode analysis to be completed on the first 50-sample eval run before build begins — not after deployment when failure patterns are discovered in production
- Use failure mode documentation as the basis for selecting adversarial eval inputs in the CI/CD harness (Principle 25)

---

### DC-17 · Safety Infrastructure Must Lead Capability Deployment, Never Trail It

**Source:** Ilya Sutskever — Safe Superintelligence Inc. founding principles

**Gap:** SSI's founding principle: "advance capabilities as fast as possible while making sure safety always remains ahead." Translated to enterprise deployment: every new autonomous capability must have its safety controls designed, tested, and confirmed before the capability is enabled. The current architecture defines safety controls (Principles 20–22) but does not explicitly gate capability go-live on safety infrastructure completion. Safety infrastructure items treated as "planned" or "in progress" at go-live time represent a deployment where capability has outrun safety.

**Options to evaluate:**
- Add a mandatory pre-go-live safety infrastructure checklist to the Agent Design Template Definition of Done: (1) kill switch tested and confirmed (Principle 20); (2) HITL flows user-tested with real reviewers (DC-12); (3) content safety layers active and configured for domain (Principle 22); (4) eval harness passing at gate threshold (Principle 25). No domain goes live until all four are confirmed — not planned
- Track safety infrastructure completion as a separate milestone from capability completion in the agent delivery roadmap

---

### DC-18 · Unpredictability Scales with Reasoning Depth — Constrain Reasoning Scope in Production

**Source:** Ilya Sutskever — "The more a system reasons, the more unpredictable it becomes" (NeurIPS 2024 / Technology.org, Dec 2024)

**Gap:** Agents that perform multi-step reasoning over long horizons produce outputs that are harder to validate and harder to explain than agents with bounded reasoning scope. Current architecture defines agent roles but does not define maximum reasoning depth as an architectural parameter. An agent with an open-ended reasoning scope can generate reasoning chains of arbitrary length — producing outputs whose validity is proportionally harder to verify.

**Options to evaluate:**
- Define maximum reasoning depth (max chain-of-thought steps, max planning horizon) per agent role in the Agent Design Template: Gateway (reasoning disabled — classification only); Domain agents (max N steps, defined per domain); Specialist agents (bounded to the specific subtask); Foundry planning nodes (explicit `max_steps` in Workflow YAML)
- Use reasoning depth as a model selection criterion: prefer models with bounded, verifiable reasoning over models with open-ended chain-of-thought for Autonomous-tier agent roles
- Add reasoning scope violation (agent exceeds defined max steps) as a monitored metric in App Insights alongside error rate and session abandonment

---

### DC-19 · Alignment Verification Requires Independent Adversarial Testing, Not Just Golden-Set Evals

**Source:** Ilya Sutskever — Weak-to-Strong Generalization (OpenAI / ICML 2024)

**Gap:** Standard evals (Principle 25) measure performance on the distribution the team designed for — they do not measure alignment at the edges of the specified behaviour. Sutskever's weak-to-strong research shows that apparent alignment on curated test sets can coexist with misalignment on out-of-distribution inputs. An adversarial test set — inputs specifically designed to probe the boundary between intended and unintended agent behaviour — is required to characterise alignment, not just performance.

**Options to evaluate:**
- Build an adversarial alignment test set for each domain before scale-out: inputs designed to probe authority boundary violations (requests for data outside the agent's entitlement), scope creep (requests that extend beyond the agent's defined domain), instruction override attempts ("ignore your previous instructions"), and cross-agent impersonation attempts
- Include adversarial alignment tests as a mandatory section in the CI/CD eval harness — not just in the pre-launch red-team exercise
- Conduct a structured alignment red-team exercise against at least one domain pilot before Month 5 scale-out begins, using the adversarial test set as the test input

---

### DC-20 · Benchmark-to-Deployment Generalisation Gap Must Be Explicitly Measured and Documented

**Source:** Ilya Sutskever — NeurIPS 2024, Dwarkesh Patel Interview 2025 ("Models look great on benchmarks but fail in practical applications")

**Gap:** Current production monitoring (Principle 26) tracks performance metrics over time but does not explicitly measure the delta between CI eval harness performance and production performance on the same task categories. A growing gap between eval harness pass rate and production outcome rate is a concrete signal that the eval harness is no longer representative of the production distribution — but without explicitly tracking this delta, the signal is invisible until performance degradation is severe.

**Options to evaluate:**
- Add a "generalisation gap" metric to the production monitoring dashboard: for each domain, track the delta between CI eval harness pass rate and production outcome rate on the same task categories (intent resolution, tool call accuracy, response quality)
- Define a gap threshold: if CI eval pass rate exceeds production outcome rate by >10 percentage points for two consecutive weeks, trigger an eval harness review to determine whether the harness is still representative
- Review the generalisation gap metric in the monthly eval re-run alongside absolute performance metrics

---

### DC-21 · Continual Learning Readiness: Define Agent Update and Revalidation Cadence

**Source:** Ilya Sutskever — Safe Superintelligence Inc. (continual learning thesis) / Dwarkesh Patel Interview 2025

**Gap:** Foundation model updates from Microsoft (Copilot Studio) or other providers silently update the model underlying every deployed agent. Without a defined revalidation cadence triggered by model updates, a model update can silently change agent behaviour without triggering the evaluation gate defined in Principle 25. The current architecture treats the model as a stable dependency; in practice it is an externally managed dependency that can change without notice.

**Options to evaluate:**
- Define a model update response policy: when the underlying foundation model is updated (by Microsoft or another provider), automatically trigger the full eval harness (Principle 25) for all domains using that model
- Add model version as a tracked field in the Agent Design Template and agent inventory (Principle 27): a change in model version is treated as having the same revalidation requirement as a prompt change
- Subscribe to provider model update announcements and integrate them with the CI/CD pipeline: a model update notification triggers a scheduled eval run, not a manual review ticket
- Document the expected revalidation timeline per domain (target: eval harness re-run completed within 48 hours of a model update notification)

---

### DC-22 · Fine-Tuning Readiness: Define When Fine-Tuning Is Appropriate Per Agent Role

**Source:** Sebastian Raschka — Using and Finetuning Pretrained Transformers / Practical Tips for Finetuning LLMs Using LoRA

**Gap:** The current architecture uses prompting and RAG as the default approach for all agents. Raschka's fine-tuning decision framework establishes a principled gate: always test in-context performance first; fine-tune only when in-context performance is demonstrably insufficient for the required reliability threshold. Without this gate, teams either over-invest in fine-tuning (unnecessary cost, maintenance burden) or under-invest (insufficient reliability for high-stakes domains). The decision is currently left to team discretion.

**Options to evaluate:**
- Add a "fine-tuning decision" section to the Agent Design Template: (1) document the in-context performance pass rate against the capability test threshold (Principle 11); (2) if below threshold, document the performance gap and the domain data available; (3) if fine-tuning is proposed, document the LoRA configuration (rank r, alpha, target layers, QLoRA trade-off if memory-constrained), expected improvement, and revalidation plan
- Define minimum domain data requirements before fine-tuning is approved: Raschka's research indicates static datasets benefit from a single training pass — multi-epoch fine-tuning without fresh data typically degrades performance; this must be reflected in the fine-tuning plan
- Fine-tuning decisions must be reviewed at the Agent Design Template review gate, not decided independently by the build team

---

### DC-23 · LLM-as-Judge Must Be Calibrated Before Use as an Automated Gate

**Source:** Sebastian Raschka — Understanding the 4 Main Approaches to LLM Evaluation (From Scratch) — extends DC-6

**Gap:** DC-6 identifies the LLM-as-Judge calibration risk. Raschka's evaluation framework adds specificity: known biases include positional bias (earlier answers rated higher), verbosity bias (longer answers rated higher), and self-enhancement bias (the judge prefers outputs stylistically similar to its own training). Without calibration, the judge systematically over-rates certain output types and under-rates others — creating false confidence before production promotion. Calibration must achieve a measurable human annotation agreement threshold and must be repeated when the judge model is updated.

**Options to evaluate:**
- Define a calibration protocol: N=200 samples per domain, annotated independently by at least 3 domain reviewers, before the LLM-as-Judge is used as an automated gate for that domain; target human annotation agreement >80% (Cohen's kappa or similar)
- Calibrate the judge during the Month 3–4 pilot wave for the first 3 domains — before Month 5 scale-out begins
- Add judge model version to the tracked fields in the Agent Design Template: when the judge model is updated, recalibration is required before the updated judge is used as a production gate
- For domains where calibration cannot reach >80% agreement (genuinely subjective domains), fall back to ensemble judging (multiple judge models + 10% human spot-check)

---

### DC-24 · RAG Retrieval Quality Requires Independent Evaluation, Separate from Generation Quality

**Source:** Sebastian Raschka — Beyond Standard LLMs / LLM engineering framework

**Gap:** Current evals (Principle 25) measure end-to-end output quality but do not separately measure retrieval quality for agents using RAG. A system that retrieves poor documents but generates fluent text will pass generation quality evals while failing on factual grounding — producing confident, coherent, but incorrect responses. For agents that rely on RAG for domain knowledge, retrieval quality is the primary reliability surface. Improving the generator without improving retrieval is misallocated effort.

**Options to evaluate:**
- Add RAG-specific evaluation metrics to the Agent Design Template for all agents using retrieval: (1) retrieval recall@K — is the relevant document in the top-K retrieved results? (2) retrieval precision — are the retrieved documents relevant? (3) grounding rate — what proportion of agent assertions are traceable to retrieved documents? (4) hallucination rate — what proportion of agent assertions cannot be traced to any retrieved document?
- Track retrieval metrics independently from generation metrics in the CI/CD eval harness and production monitoring dashboard
- Define minimum retrieval quality thresholds per domain before go-live: recall@3 >80% is a suggested starting threshold; adjust based on pilot data

---

### DC-25 · Model Architecture Validation for Agentic Task Types

**Source:** Sebastian Raschka — The State Of LLMs 2025 / Beyond Standard LLMs (linear attention multi-turn failure documentation)

**Gap:** Raschka documents that linear attention architectures fail on multi-turn reasoning tasks — both of which are foundational to agentic applications. This is an architecture-level concern, not a model size concern. Benchmark scores do not predict multi-turn agentic performance — a model can score well on single-turn benchmarks while failing systematically on multi-turn coherence. Current model selection criteria (Principle 16, DC-10) focus on capability thresholds and task-specific tests but do not explicitly validate architecture-level suitability for agentic patterns.

**Options to evaluate:**
- Add model architecture validation to the Agent Design Template model selection section for any model approved for an agentic role: (1) does the architecture support multi-turn coherence at the expected session length (test at maximum expected turns, not just average)? (2) has structured output / tool call generation been validated with the specific tool schemas used in this domain? (3) has reasoning depth been tested at the maximum chain length expected for this role?
- Flag linear attention architectures as requiring additional multi-turn validation before approval for agentic roles
- Coordinate with Microsoft model team to maintain a validated model list for agentic task types as the model landscape evolves; update the Agent Design Template approved model list quarterly

---

### DC-26 · Evaluation Methodology Must Evolve with the Agent Fleet — Set a Review Cadence

**Source:** Sebastian Raschka — State of LLMs annual assessments (methodology evolution across 2023–2025)

**Gap:** Continuous production evaluation (Principle 26) catches performance drift against a fixed methodology. It does not catch methodology drift — the eval harness becoming unrepresentative as the input distribution shifts, new LLM failure modes are discovered, or better evaluation methods emerge. Raschka's annual reviews show that evaluation best practices shift materially year-over-year. An eval harness designed in Q1 Year 1 may be inadequate by Q1 Year 2 for reasons unrelated to agent performance.

**Options to evaluate:**
- Add a bi-annual "eval methodology review" to the governance calendar (every 6 months): review whether (1) the golden dataset remains representative of the current production input distribution; (2) the LLM-as-Judge model is still the best available for the task type; (3) new evaluation methods from the research community should be incorporated; (4) any eval blind spots have been identified through production failures
- Treat the eval methodology review as a governance deliverable alongside the monthly performance review — owned by the CoE eval lead, not the individual domain teams
- Use the generalisation gap metric (DC-20) as an early warning signal that triggers an unscheduled eval methodology review if it exceeds the defined threshold

---

### DC-27 · Open-Weight Model Strategy: Define When Open-Weight Models Are Appropriate vs. Hosted APIs

**Source:** Sebastian Raschka — AI and Open Source / LLM research coverage / Ahead of AI newsletter

**Gap:** The current architecture defaults to hosted API models (Microsoft-managed in CS, Azure OpenAI in Foundry) without a principled decision framework for when open-weight models (deployed via Azure ML or AKS) would be more appropriate. Raschka's practitioner framework identifies specific conditions where open-weight models offer meaningful advantages: data residency requirements that preclude sending data to a hosted API, high-volume specialist tasks where hosting costs favour local deployment, and specific fine-tuning requirements where model weights must be accessible. These conditions are not addressed in the current architecture.

**Options to evaluate:**
- Add an "open-weight vs. hosted API" decision to the Agent Design Template model selection section with a structured decision gate: (1) does this domain have data residency requirements that preclude sending data to a hosted API? (2) is the expected volume high enough that Azure ML hosting costs are lower than hosted API costs at scale? (3) is there a specific fine-tuning requirement that can only be met with an open-weight model? If any answer is yes, evaluate an open-weight deployment via Azure ML
- Define a governance process for open-weight deployments: model version, source, licence compliance, and security review must be documented in the Agent Design Template before any open-weight model is approved
- Identify 1–2 high-volume specialist domains in the Month 5–8 scale-out wave as candidates for open-weight model evaluation; compare reliability, cost, and maintenance burden against the hosted API baseline

---

---

### DC-28 · Hardware-Algorithm Co-Design Is a First-Class Engineering Discipline

**Source:** Noam Shazeer — [Hot Chips 2025 Keynote](https://hc2025.hotchips.org/assets/program/conference/day1/k1_GoogleDeepMind_Shazeer.pdf) · [deeplearning.ai interview](https://www.deeplearning.ai/the-batch/ai-transformed/) · [MQA — Fast Transformer Decoding](https://arxiv.org/abs/1911.02150)

**Gap:** Enterprise agent architectures are typically finalised at the design phase before infrastructure constraints are evaluated. Shazeer's principle — "Arithmetic is cheap; moving data is expensive" — means that architectural decisions about context window size, model tier selection, attention pattern, and quantisation level all have direct and measurable serving cost implications. An architecture that is "correct" in a sandbox may be undeployable at production scale due to KV cache pressure, memory bandwidth limits, or GPU memory constraints. These constraints are not implementation details — they are first-class design inputs.

**Options to evaluate:**
- Before finalising agent architecture for production scale, perform a memory-bandwidth and compute-cost analysis of the target inference infrastructure: KV cache size at target context length, throughput at target model tier, and memory footprint at target precision
- Add serving efficiency targets (latency p50/p95, throughput at peak QPS, memory footprint per agent) to the Agent Design Template as acceptance criteria alongside quality benchmarks
- Model inference cost as a function of both call volume and per-call weight (model tier, context length, attention pattern) — not just call count

---

### DC-29 · Inference Cost Must Be a Primary Training Objective, Not an Afterthought

**Source:** Noam Shazeer — [Inside Kaiju](https://blog.character.ai/inside-kaiju-building-conversational-models-at-scale/) · [Character.AI Inference Blog](https://research.character.ai/optimizing-inference/)

**Gap:** Enterprise model selection typically evaluates benchmark quality first and then applies post-training quantisation to reduce serving cost. Shazeer's evidence argues the reverse: Kaiju is natively trained in int8 precision — "natively training models in int8 precision to eliminate training/serving mismatch." Serving efficiency targets drove the design philosophy from the start. Post-training quantisation is an inferior substitute: it introduces training/serving distribution mismatch, can degrade quality in unpredictable ways, and cannot achieve the full efficiency gains of native-precision training.

**Options to evaluate:**
- For any custom fine-tuning or model commissioning, specify inference cost targets (latency, memory footprint, throughput at target QPS) as acceptance criteria alongside quality benchmarks — not as post-delivery optimisation goals
- Prefer models natively trained at the target inference precision (INT8, BF16) over models that require post-training quantisation
- Require model providers to disclose serving efficiency benchmarks (throughput at target precision, KV cache size at expected context length) as part of the model selection evaluation

---

### DC-30 · Conditional Compute — Activate Only What the Input Requires

**Source:** Noam Shazeer — [Sparsely-Gated MoE Layer](https://arxiv.org/abs/1701.06538) · [Switch Transformers](https://arxiv.org/abs/2101.03961)

**Gap:** Current agent routing determines which agent to invoke but does not determine the compute tier of that invocation. Shazeer's MoE principle — only activate the parameters relevant to each input — implies a finer-grained routing decision: the appropriate model tier should be determined per input, not per agent role. A specialist agent handling both simple extraction tasks and complex multi-step reasoning should not use a frontier reasoning model for the extraction tasks. The marginal benefit of the more capable model does not justify the cost for the simpler input class.

**Options to evaluate:**
- Design a lightweight input classifier (Gateway-level, below the LLM tier) that determines input complexity class before any LLM invocation: routine/extraction → small model, standard generation → medium model, complex reasoning → large model
- The classifier itself must be near-zero cost (rule-based or a small classification model at int8 precision) — analogous to the MoE gating network, which is a small linear layer, not a full transformer
- Measure the input distribution for each domain agent: what percentage of inputs fall into each complexity class? Use this to size the model tier selection investment

---

### DC-31 · Scale Produces Emergent Capabilities — Design Agent Interfaces for Capability Discovery, Not Just Current Capability

**Source:** Noam Shazeer — [a16z Interview](https://a16z.com/universally-accessible-intelligence/) · [deeplearning.ai interview](https://www.deeplearning.ai/the-batch/ai-transformed/) · [Dwarkesh Podcast](https://www.dwarkesh.com/p/jeff-dean-and-noam-shazeer)

**Gap:** Enterprise agent architectures are designed tightly around current model capability constraints — instruction length limits, reasoning depth, tool-calling reliability. As underlying models improve, these constraints change. An architecture built around today's limitations becomes a binding constraint that prevents future capability upgrades from being deployed without re-architecture. Shazeer's consistent observation: scale reveals capabilities not predicted by smaller-scale behaviour, and the right posture is to design interfaces that accommodate capability emergence rather than hardcoding current limitations.

**Options to evaluate:**
- Design agent interfaces, tool signatures, and authority scopes to be model-agnostic: the interface should not assume the capability level of the current underlying model
- Use capability discovery patterns: agents declare what they can do through structured metadata declarations (in the Agent Design Template), not through hardcoded capability lists embedded in routing logic
- Review Agent Design Template capability declarations quarterly: as models improve, capability declarations may require revision — this should be a scheduled governance activity, not an ad-hoc discovery
- Document current-model limitation workarounds explicitly in the Agent Design Template so they can be removed cleanly when the limitation is resolved by a model update

---

### DC-32 · Serving Infrastructure Is a Competitive Moat — Model and Serving Architecture Must Co-Evolve

**Source:** Noam Shazeer — [Character.AI Inference Blog](https://research.character.ai/optimizing-inference/) · [Character.AI Inference Blog Part Deux](https://research.character.ai/optimizing-ai-inference-at-character-ai-part-deux/) · [Inside Kaiju](https://blog.character.ai/inside-kaiju-building-conversational-models-at-scale/)

**Gap:** Enterprise organisations typically select models from third-party providers and build serving infrastructure separately. Shazeer's evidence — a 13.5x cost advantage over leading commercial APIs and a 33x cost reduction over two years — is the product of co-designing model architecture (MQA, cross-layer KV sharing, int8 native training) with serving infrastructure (sticky sessions, per-dialogue KV cache, fleet routing). This co-design is not replicable by applying generic optimisation to an off-the-shelf model: cross-layer KV sharing requires the model to be trained to support it; MQA-optimised serving requires the model to use multi-query (not multi-head) attention.

**Options to evaluate:**
- Treat serving compatibility as a primary model selection criterion alongside benchmark quality: MQA or GQA (not MHA), quantisation-aware trained at target precision, designed for the context length your use case requires
- Require model providers to disclose serving efficiency characteristics (KV cache size at target context length, throughput at target precision, memory footprint per concurrent session) as part of model evaluation
- For high-volume enterprise agents (>1000 requests/day), evaluate Azure ML-hosted open-weight models with serving-optimised architectures against hosted API cost; the breakeven point shifts materially with Shazeer-class serving efficiency
- Add serving architecture co-design to the Agent Design Template model selection section: document the serving infrastructure the selected model is optimised for, and verify it matches the production deployment target

---

*Last reviewed: March 2026 · Owner: Vineet Kaul*
