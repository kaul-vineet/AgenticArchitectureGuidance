---
layout: default
title: "⚠️ 9. Design Considerations"
parent: Enterprise Multi-Agent Architecture
nav_order: 10
---

## 9. ⚠️ Design Considerations

> Open items identified during architecture review. To be resolved before production scale-out.

---

### DC-1 · Gateway Routing Capacity at Scale

**Gap:** The CS instruction limit is 8,000 characters. At 8–12 domain agents, each requiring a precise routing description plus system safety instructions, the gateway prompt approaches this ceiling. The star topology anti-pattern section correctly warns that routing accuracy collapses as scope grows — but the gateway itself faces this exact problem.

**Options to evaluate:**
- Route by intent taxonomy using a structured CS topic with a bounded decision tree — not open-ended LLM prompt matching
- Externalise routing descriptions to a Dataverse lookup table; inject only the matched description at runtime
- Measure token consumption of the gateway prompt at 6, 9, and 12 domain agents before committing to a pattern

---

### DC-2 · Dependent Cross-Domain Workflows

**Gap:** The architecture handles independent multi-domain calls correctly (leave + Salesforce: gateway calls HR domain, calls Agentforce, composes — no issue). The gap is specifically *dependent* cross-domain workflows: where steps have ordering constraints, shared state, compensation requirements, or duration exceeding the CS session limit (60 min / 100 turns).

**Example:** Employee offboarding — IT access revocation must complete before HR records close; Finance final payroll depends on HR confirmation; if Finance fails, HR records must be reopened across an already-closed CS session.

**Options to evaluate:**
- Classify intents at the gateway as "independent multi-domain" (current pattern) vs. "dependent multi-domain" (needs orchestration)
- Power Automate workflow as orchestrator for dependent processes — no session limits, native compensation branches, PA approval flows for human gates
- Foundry Workflow (state machine) for compliance-grade processes requiring CMK or full audit trail
- Define a Workflow Registry in Dataverse (parallel to Agent Registry): named workflows with ordered steps, compensating actions, timeout policies, and correlationId prefix per workflow

---

### DC-3 · Indirect Prompt Injection

**Gap:** Content safety is applied at user input and agent output, but not specifically at tool *response* ingestion. At 100 agents making tool calls against enterprise systems (SharePoint, HRIS, CRM, Jira), a malicious document or record can embed adversarial instructions that hijack the agent's next action — without triggering output-layer content safety.

**Options to evaluate:**
- Add a response sanitisation step between tool response and LLM context re-entry — distinct from output content safety
- Add to the Agent Design Template tool checklist: "Is the tool response from a user-writable system? If yes, sanitisation required."
- Evaluate Azure Content Safety prompt shield for indirect injection detection on tool responses

---

### DC-4 · Preview Dependency on Foundry Memory Feature

**Gap:** Cross-session user profile storage for Foundry agents is built on Memory Feature (preview). A 100-agent factory deployment targeting 6–12 months ships before GA is confirmed. API or behaviour changes in preview would require rebuilding the user profile layer across all Foundry agents.

**Options to evaluate:**
- Build with Redis + Cosmos DB as the durable profile store now; add Memory Feature as a retrieval layer when GA
- Gate Foundry agents on Memory Feature only after it reaches GA or a committed preview SLA is confirmed
- Define a profile store abstraction so the underlying store can be swapped without rewriting agent logic

---

### DC-5 · Model Selection Locked in Copilot Studio

**Gap:** Architectural Principle 16 recommends right-sizing the model per agent role (smaller models for classification, extraction, formatting). CS — recommended for 80–90% of agents — locks to Microsoft-managed models with no selection control. The principle is correct; the platform does not support it for the majority of agents.

**Options to evaluate:**
- Explicitly document this as a CS tradeoff in the platform selection section
- Route classification and extraction agents that would benefit from smaller models to Foundry instead of CS
- Monitor whether CS adds model selection capability; treat Principle 16 as Foundry-only for now

---

### DC-6 · LLM-as-Judge Calibration

**Gap:** LLM-as-Judge is recommended as the evaluation gate for routing accuracy and response quality. Known biases (positional, verbosity, self-enhancement) mean the judge can produce systematically wrong quality signals — giving false confidence before production promotion — unless calibrated against human annotations first.

**Options to evaluate:**
- Establish human annotation agreement threshold (e.g., >80% agreement) as a prerequisite before LLM-as-Judge is used as an automated gate
- Calibrate the judge during the Month 3–4 pilot wave, not after Month 5 scale-out begins
- Use ensemble judging (multiple model judges + human spot-check) for high-stakes routing decisions

---

### DC-7 · No Circuit Breaker or Tool Degradation Pattern

**Gap:** A 90% tool accuracy threshold exists as a deployment gate but there is no defined behaviour for tools that degrade below threshold after they are in production. A failing tool will continue to be called, accumulating errors and potentially corrupting downstream state across multiple agents.

**Options to evaluate:**
- Define a degradation mode per agent: fallback to read-only / FAQ-only if agentic tools fail above a configurable error rate threshold
- Add error rate monitoring in App Insights per tool per agent with an alert threshold and automatic tool disablement trigger
- Leverage Principle 20 (kill switch) explicitly: document the per-tool disablement procedure as part of every Agent Design Template entry

---

### DC-8 · Error Propagation Through the Agent Hierarchy

**Gap:** In a 4-hop chain (Gateway → Domain Agent → Specialist Agent → Tool), there is no defined contract for how a tool failure propagates back to the user with enough context to be meaningful. Intermediate agents typically either swallow the error (wrong) or re-interpret it (introduces hallucination risk).

**Options to evaluate:**
- Define a typed error envelope passed upstream untouched: `{ errorCode, errorMessage, retryable, agentId, stepId, correlationId }`
- Only the gateway translates the error envelope into user-facing language — intermediate agents forward, never interpret
- Add error envelope to the Agent Design Template I/O contract section as a mandatory output field

---

### DC-9 · correlationId Enforcement

**Gap:** The correlationId pattern (`{domainCode}-{agentId}-{sessionTimestamp}`) is a naming convention, not an enforced contract. In heterogeneous systems (CS + Foundry + Agentforce), propagation breaks silently — there is no validation step that rejects responses missing the expected correlationId.

**Options to evaluate:**
- Add gateway-level validation: any agent response without a matching correlationId is treated as an error, not passed to the user
- Define correlationId as a required field in the A2A payload schema, not an optional header
- Add correlationId propagation test to the Agent Design Template Definition of Done checklist (already partially there — make it a gate, not a checkbox)

---

*Last reviewed: March 2026 · Owner: Vineet Kaul*
