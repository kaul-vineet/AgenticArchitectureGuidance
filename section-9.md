---
layout: default
title: "📋 8. Agent Design Template"
parent: Enterprise Multi-Agent Architecture
nav_order: 10
---

## 8. 📋 Agent Design Template

> Complete one card per agent before building. This is the factory input document.

---

### 🪪 1. Identity

| Field | Value |
|---|---|
| **Agent Name** | *e.g., HR Leave Management Agent* |
| **Agent ID** | *Unique ID — used as correlationId prefix* |
| **Version** | *e.g., 1.0.0* |
| **Owner / Sponsor** | *Named individual accountable for this agent* |
| **Domain** | *e.g., Human Resources* |
| **Business Justification** | *What problem does this agent solve? What workflow does it replace?* |

---

### 🧩 2. Agent Classification

| Field | Options | Selected |
|---|---|---|
| **Agent Type** | Gateway · Domain Agent · Specialist Child · Specialist Connected · Inline Topic | |
| **Platform** | 🟦 Copilot Studio · 🟧 Foundry Agent Service | |
| **Agentic Pattern** | 🔵 Flow (deterministic) · 🟠 Crew (autonomous) | |
| **Deployment Tier** | Inline (shared with parent) · Connected (independent) · External (A2A) | |

> ⚠️ Start with Flow. Choose Crew only if the task is genuinely open-ended, dynamic adaptation is required, and errors are recoverable.

---

### 🎯 3. Scope and Responsibilities

```
Single Responsibility Statement:
"This agent is responsible for _________________________________
 and nothing else."

In Scope:
  •
  •

Out of Scope (explicitly):
  •
  •

Orchestrator routing description (used by parent for routing):
"Call this agent when the user wants to ________________________"
(Write this carefully — it IS the routing signal)

Ambiguity handling:
  What inputs require clarification before the agent acts?
  •
```

---

### 📥 4. Input / Output Contract

#### Inputs

| Parameter | Type | Required? | Format / Example |
|---|---|---|---|
| *e.g., userId* | String | ✅ Yes | AAD object ID |
| *e.g., leaveType* | Enum | ✅ Yes | Annual \| Sick \| Unpaid |
| *e.g., startDate* | Date | ✅ Yes | RFC3339: 2026-03-10 |

#### Outputs

| Parameter | Type | Always Returned? | Description |
|---|---|---|---|
| *e.g., confirmationRef* | String | ✅ Yes | Leave request reference |
| *e.g., status* | Enum | ✅ Yes | Approved \| Pending \| Rejected |
| *e.g., errorCode* | String | On failure only | Structured error for parent to handle |

---

### 🔌 5. Tools and Integrations

| Tool Name | Type | System | Operation | Auth | Structured Error Response? |
|---|---|---|---|---|---|
| *e.g., GetLeaveBalance* | Power Automate | HRIS | Read | Managed Identity | ✅ Yes |
| *e.g., SubmitLeaveRequest* | Power Automate | HRIS | Write | Managed Identity | ✅ Yes |

**Tool quality checklist (HuggingFace: >50% of agent failures from malformed tool args):**
- [ ] Explicit argument formats with concrete examples in tool description
- [ ] Datetime fields specify RFC3339 format with timezone offset
- [ ] All error responses are structured and actionable (not silent failures or raw HTTP codes)
- [ ] Tool tested independently before wiring into agent
- [ ] Tool accuracy validated > 90% in test harness before production promotion

**Verification gates (MAST FM-3.3 — never self-verify):**
- For every write operation: define the deterministic read-back step that confirms state change
- Human-in-the-loop PA approval flow required before: ______________________

**Side-channel state (Neuro SAN pattern):**
- Sensitive values that must NOT appear in LLM prompt: ______________________
- Resolved at tool execution time via Key Vault reference: ____________________

---

### 📚 6. Knowledge Sources

| Source | Type | Access | Size | If >500 docs → Azure AI Search |
|---|---|---|---|---|
| *e.g., HR Policy SharePoint site* | SharePoint | Read | ~50 docs | No |

---

### 💾 7. Session and Memory

| Concern | CS Approach | Foundry Approach |
|---|---|---|
| **Within-session state** | Topic/global variables | Thread messages (managed) |
| **Context compaction** | Summarisation PA flow at N turns | Memory feature + compaction step |
| **Cross-session user profile** | Dataverse custom table (PA flow reads on start) | Memory feature (preview) |
| **Inactivity handling** | Inactivity topic at __ minutes → clear variables | Thread retained; new thread on next session |
| **Long-running tasks** | Async: trigger PA → notify via Teams card | Async: Durable Function → push notification |
| **Max session turns** | Design to complete in < 80 turns | No limit but implement compaction at N turns |

---

### 🔁 8. Agentic Behaviour

| Pattern | Enabled? | Configuration |
|---|---|---|
| **Tool Use** | ✅ Always | List tools in section 5; merge tools where possible |
| **Clarification** | ✅ Always | Define ambiguous input conditions; enter clarification topic before any agentic branch |
| **Termination Logic** | ✅ Always | Max steps: __ · Loop detector: same tool + same args = break · Success state: deterministic (not LLM-reasoned) |
| **External Verification** | ✅ For writes | Read-back step after every state-changing tool call |
| **Reflection** | ☐ Optional | Trigger: quality-sensitive outputs (reports, code, plans). Critic prompt: "Review for accuracy, completeness, and policy compliance." |
| **Planning** | ☐ Crew only | Enable only if task is genuinely open-ended |
| **Multi-agent delegation** | ☐ Domain agents only | List connected specialist agents with routing descriptions |

---

### 🛡️ 9. Security and Governance

| Field | Value |
|---|---|
| **Entra Agent Identity** | Agent ID registered in Entra: __________________ |
| **Credential Type** | Managed Identity (preferred) · Federated · OAuth2 |
| **Permitted Tool Scopes** | List APIs/connectors this agent is allowed to call |
| **Prohibited Scopes** | What this agent must NOT have access to |
| **Data Classification** | Public · Internal · Confidential · Restricted |
| **PII Handled?** | Yes / No → If Yes: Foundry Standard + CMK required |
| **Sensitive state in side channel?** | Yes / No → If Yes: Key Vault references, never in prompt |
| **Content Safety** | Input filter: ✅ / Output filter: ✅ |

---

### 🔗 10. Connected Agents (Domain Agents Only)

| Specialist Agent | Platform | Routing Description | Calls Per Session |
|---|---|---|---|
| *e.g., Leave Management Agent* | CS | "Call when user requests leave application or balance" | 1 |
| *e.g., HR Policy Agent* | CS | "Call when user asks about HR policies or entitlements" | 3 |

> ⚠️ Do not exceed 8–10 specialist agents per domain agent. Keep total tools + agents bounded — routing accuracy degrades as scope grows.

---

### 📊 11. Observability and Evaluation

| Metric | Target | Measurement |
|---|---|---|
| **Task completion rate** | > 90% | CS Analytics / Foundry traces |
| **Tool call accuracy** | > 90% | Test harness pre-production (HuggingFace deployment gate) |
| **Routing accuracy** | > 95% | LLM-as-a-Judge on sample sessions |
| **Average turns to completion** | < 5 | CS Analytics |
| **Session escalation rate** | < 10% | CS Analytics |
| **Loop detection events** | 0 in production | Foundry orchestration logs |
| **FM-3.3 (self-verification) events** | 0 | Verification gate audit log |

**Evaluation approach:**
- [ ] Deterministic checks for structured outputs
- [ ] LLM-as-a-Judge for response quality
- [ ] Human annotation sample (monthly calibration)

**correlationId pattern:** `{domainCode}-{agentId}-{sessionTimestamp}`

---

### 🏗️ 12. ALM and Deployment

| Field | Value |
|---|---|
| **Solution Name** | *e.g., HR.LeaveManagement.Agent* |
| **Publisher Prefix** | *e.g., contoso* |
| **Base Template Used** | *e.g., Specialist-Task-Agent-Template-v1* |
| **Dev Environment** | |
| **Test Environment** | |
| **Prod Environment** | |
| **ALM Pipeline** | Power Platform Pipelines · GitHub Actions · Azure DevOps |
| **Environment Variables** | All config values externalised (endpoints, IDs, feature flags) |
| **Dependencies** | Parent agents, shared PA flows, MCP servers, Dataverse tables |

---

### ✅ 13. Definition of Done

- [ ] Single responsibility statement is clear and agreed with business owner
- [ ] All ambiguous input conditions have a clarification topic
- [ ] Input/output contract defined and tested
- [ ] All tool calls tested independently at > 90% accuracy
- [ ] Structured error responses on every tool connector
- [ ] External verification gate built for every write operation
- [ ] Termination conditions defined in code (max steps, loop detector)
- [ ] Sensitive state moved to side channel (Key Vault) — not in prompt
- [ ] Inactivity/compaction topic built (CS agents)
- [ ] Entra Agent Identity created, scoped with least privilege, sponsor assigned
- [ ] correlationId propagation tested end-to-end
- [ ] Deployed through ALM pipeline (never manual publish)
- [ ] Registered in Agent 365 / CoE Toolkit inventory

### 📚 References
- [Copilot Studio — ALM Strategy](https://learn.microsoft.com/en-us/microsoft-copilot-studio/guidance/alm)
- [Copilot Studio — Agent Templates](https://learn.microsoft.com/en-us/microsoft-copilot-studio/faqs-templates)
- [Microsoft Entra Agent ID](https://learn.microsoft.com/en-us/entra/agent-id/identity-professional/microsoft-entra-agent-identities-for-ai-agents)
- [DeepLearning.AI — Evaluating AI Agents](https://www.deeplearning.ai/short-courses/evaluating-ai-agents/)
- [HuggingFace — Agent Leaderboard](https://huggingface.co/blog/pratikbhavsar/agent-leaderboard)
- [Power Platform — Component Collections](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-export-import-copilot-components)
{: .ref-grid}

---
