---
layout: default
title: "👑 6. Parent / Master Agent"
parent: Enterprise Multi-Agent Architecture
nav_order: 8
---

## 6. 👑 Parent / Master Agent

---

<h3 style="color:#0078d4;font-weight:700;border-left:4px solid #0078d4;padding-left:0.6rem;margin-top:1.5rem;">🟦 Copilot Studio as Parent</h3>

| ✅ Pros | ❌ Cons |
|---|---|
| · **No-code authoring** — domain teams build and own agents independently; no developer required | · **Sequential orchestration** — child and connected agents called one-at-a-time; no native parallel fan-out |
| · **Native M365 channels** — Teams, SharePoint, M365 Copilot, web chat, mobile app zero-setup | · **Session limits** — 30 min idle, 60 min total, 100 turns applies to full parent + child chain |
| · **Shared container for CS children** — variables, auth, Dataverse, PA connectors natively shared with zero config | · **Instructions size limit** — hard cap of 8,000 characters per agent; complex domain prompts require careful compression |
| · **1,400+ Power Automate connectors** — SAP, Salesforce, ServiceNow, Oracle, Dynamics 365 out of box | · **Model selection locked** — bound to Microsoft-managed models; no BYO model or parameter tuning |
| · **Agent templates + Component Collections** — repeatable factory model; clone and configure per domain | · **No built-in parallel execution** — unlike Foundry Workflows Concurrent pattern; parallel tasks require PA + Dataverse workarounds |
| · **Power Platform Pipelines** — built-in Dev→Test→Prod ALM; no custom CI/CD required | · **CS → Foundry connected agent in preview** — not production-ready; citation gaps and black-box orchestration |
| · **Fastest time-to-first-agent** — hours to first live agent vs. days in Foundry | · **No deep orchestration patterns** — Magentic, Group Chat, Concurrent patterns not available; routing is generative/sequential only |
| · **Agent 365 + CoE Toolkit** — unified agent registry, governance, and audit natively in M365 Admin Center | |
| · **Supervisor pattern** — CS parent acts as supervisor routing to CS specialist child/connected agents; routing logic in generative Topics or explicit Topic conditions | |
| · **Supervisor-as-tool** — CS agent can be published as an A2A endpoint and called as a tool by higher-level Foundry or non-MSFT orchestrators | |
| · **Hierarchical pattern** — Gateway → Domain → Specialist hierarchy fully supported; each tier is an independent CS agent with own ALM | |

<h4 style="color:#5c2d91;font-weight:700;border-left:3px solid #5c2d91;padding-left:0.5rem;margin-top:1.2rem;">🤝 Agent Connectivity — What CS Parent Supports</h4>

Copilot Studio as parent supports two tiers of agent connectivity: **any Copilot Studio agent as a child agent** and **any A2A-compliant agent as a connected agent**.

<h5 style="color:#5c2d91;font-weight:600;margin-top:1rem;">👶 Child Agents — Any Copilot Studio Agent</h5>

A CS parent can embed any other Copilot Studio agent as an inline child agent directly within its topic flow.

| ✅ Pros | ❌ Cons |
|---|---|
| · **Any CS agent is eligible** — no special configuration required | · **CS agents only** — Foundry and non-MSFT agents cannot be child agents; connected tier only |
| · **Shared container** — child inherits parent's variables, auth, Dataverse, PA connectors with zero extra config | · **Sequential invocation** — child agents called one at a time; no parallel fan-out |
| · **Zero-latency inline execution** — same session and transcript as parent; no orchestration hop | · **Session chain limits apply** — 30 min idle / 60 min total / 100 turns covers full chain |
| · **Native identity inheritance** — same Entra connection, same Dataverse row-level security | · **Instructions cap (8,000 chars)** — child agent system prompts must stay within limit |
| · **Unified ALM** — PP Pipelines promotes parent and child together through Dev → Test → Prod | · **Model selection locked** — both parent and child bound to Microsoft-managed models |

<h5 style="color:#5c2d91;font-weight:600;margin-top:1rem;">🔗 Connected Agents — Any A2A-Compliant Agent</h5>

A CS parent can call any agent that exposes an A2A-compliant endpoint — including CS, Foundry, Agentforce, and ServiceNow Virtual Agent.

| ✅ Pros | ❌ Cons |
|---|---|
| · **Cross-platform reach** — CS, Foundry, Agentforce, ServiceNow all callable via A2A | · **Separate session context** — connected agent runs its own thread; correlationId required |
| · **Independent ALM** — connected agents versioned and deployed without touching parent | · **Text-only return (non-CS agents)** — Foundry and non-MSFT return plain text; no typed variable contract |
| · **Reusable** — one connected agent serves multiple CS parents simultaneously | · **Preview limitations** — CS → Foundry and non-MSFT A2A connections in preview |
| · **Team separation** — domain teams own connected agents; platform team owns gateway | · **Citation propagation gap** — citations from connected agent knowledge sources may not surface in parent |
| · **No rebuild required** — existing A2A-compliant agents onboarded without rewriting | · **Latency overhead** — A2A hop adds 200–500 ms per invocation vs inline child |

---

<h3 style="color:#e07a10;font-weight:700;border-left:4px solid #e07a10;padding-left:0.6rem;margin-top:1.5rem;">🟧 Foundry Agent Service as Parent</h3>

| ✅ Pros | ❌ Cons |
|---|---|
| · **Five orchestration patterns (GA)** — Sequential, Concurrent (fan-out/fan-in), Handoff, Group Chat, Magentic (supervisor) | · **Slower time-to-first-agent** — portal setup, infra provisioning, and Workflow YAML required |
| · **Concurrent pattern** — all sub-agents execute simultaneously on same input; results auto-aggregated; configurable timeouts | · **Classic Connected Agents deprecated** — `ConnectedAgentToolDefinition` retires March 31, 2027; must migrate to Workflows |
| · **Magentic pattern (Supervisor)** — manager agent dynamically selects which specialist executes next; stall detection; replanning; optional human-in-the-loop | · **Magentic pattern Python-only** — C# SDK does not yet support Magentic orchestration |
| · **Supervisor-as-tool** — Foundry agent callable as A2A tool by any higher-level orchestrator; parent retains control across delegation | · **Session management overhead** — threadId registry pattern required; per-agent thread context must be explicitly designed |
| · **Hierarchical pattern** — Workflow nodes chain into Gateway → Domain → Specialist tiers; no hard depth limit in Workflows (unlike deprecated Classic limit of 2) | · **PaaS licensing cost** — Foundry Standard requires Cosmos DB, Azure AI Search, Azure Functions on top of token costs |
| · **A2A Tool (GA)** — calls cross-project Foundry agents, CS agents, Agentforce, ServiceNow, any A2A-compliant endpoint; enterprise auth (Managed Identity, OAuth, API key) | · **Infrastructure overhead** — Cosmos DB (BYO for Standard), Redis (session state), Azure Functions (tools) required |
| · **Full OpenTelemetry + App Insights** — distributed tracing across all agent hops, tool calls, and handoffs; parent-child messages traceable end-to-end | · **No native CS child agents** — Foundry cannot call CS as a native child; requires CS to publish as A2A endpoint (preview) |
| · **Deterministic Workflows** — YAML/Power Fx state machines; Human-in-the-loop approval nodes built-in | · **No unified agent registry** — no equivalent of Agent 365; governance requires custom Azure Policy + Entra + App Insights setup |
| · **Custom orchestration** — full SDK access (Python, C#, JS, Java) for custom orchestration logic, state machines, conditional branching | |
| · **Code Interpreter + File Search** — built-in tools; no custom integration required | |
| · **Native Memory (preview)** — automatic cross-session user profiles without custom code | |
| · **BYO Cosmos DB** — data sovereignty, CMK, compliance-grade audit trail per agent | |

> 💡 **Foundry is not code-only.** The Foundry portal exposes three built-in Workflow templates (Sequential, Human-in-the-loop, Group Chat) with a visual node editor. Concurrent and Magentic patterns require SDK code. The distinction from CS is orchestration richness and infrastructure ownership, not code vs. no-code.

<h4 style="color:#5c2d91;font-weight:700;border-left:3px solid #5c2d91;padding-left:0.5rem;margin-top:1.2rem;">🔗 Agent Connectivity — What Foundry Parent Supports</h4>

Foundry as parent supports four connectivity types and five orchestration patterns. Unlike CS, Foundry's connectivity model is designed for engineering-owned domains requiring parallel execution, structured outputs, and full observability.

<h5 style="color:#5c2d91;font-weight:600;margin-top:1rem;">⚡ Orchestration Patterns (Foundry Workflows)</h5>

| Pattern | Description | Availability |
|---|---|---|
| **Sequential** | Agents execute one after another; output of each becomes input to next | ✅ GA — Portal + SDK |
| **Concurrent** | All listed agents receive same input simultaneously; results auto-aggregated or via custom aggregator | ✅ GA — SDK (`BuildConcurrent()`) |
| **Handoff** | Agent transfers control based on context; escalation and fallback supported | ✅ GA — Portal + SDK |
| **Group Chat** | Agents collaborate in shared conversation; dynamic routing between participants | ✅ GA — Portal template + SDK |
| **Magentic (Supervisor)** | Manager agent dynamically selects which specialist executes next; stall detection; replanning; optional HITL | ✅ GA — Python SDK only (C# pending) |
| **Human-in-the-loop** | Workflow node pauses for human approval or input before continuing | ✅ GA — Portal + SDK |

<h5 style="color:#5c2d91;font-weight:600;margin-top:1rem;">🤖 Sub-Agent Connection Types</h5>

| Connection Type | Mechanism | Status | Pros | Cons |
|---|---|---|---|---|
| **Same-project Foundry Agents** | Workflow nodes | ✅ GA (preferred) | Native; full observability; all 5 patterns including parallel fan-out; YAML/Power Fx; versioning | Limited to same Foundry project; Workflow YAML must be maintained |
| **A2A Tool** (cross-project Foundry, CS, non-MSFT) | `A2APreviewTool` — any A2A-protocol endpoint | ✅ GA (Python/JS); ⚠️ Preview (C#/Java) | Open standard; crosses org/cloud boundaries; enterprise auth (Managed Identity, OAuth, API key); works with CS, Agentforce, ServiceNow, custom | Parent retains control (sub-agent answer returns to parent; parent synthesizes); network latency per hop; CS A2A in preview; third-party responsibility is caller's |
| **Classic Connected Agents** | `ConnectedAgentToolDefinition` | ❌ Deprecated (retire Mar 2027) | Simple SDK setup; natural language routing | Max depth = 2; sequential only; no local function calling in sub-agents; no guarantees on citations |
| **OpenAPI Tool** | REST endpoint definition | ✅ GA | Calls any REST/agent-like service; no A2A protocol required; works with legacy systems | Not a true agent-to-agent channel; no A2A discovery or capability negotiation |

<h5 style="color:#5c2d91;font-weight:600;margin-top:1rem;">📊 Foundry Connectivity Matrix</h5>

| Sub-Agent Type | Supported? | Mechanism | Notes |
|---|---|---|---|
| Same-project Foundry agents | ✅ GA | Workflow nodes | Preferred path — full observability and pattern support |
| Cross-project Foundry agents | ✅ GA | A2A Tool | Requires A2A connection configured in Foundry portal |
| Copilot Studio agents | ⚠️ Preview | A2A Tool (CS publishes as A2A endpoint) | CS A2A support in preview; indirect pathway |
| Non-MSFT agents (Agentforce, ServiceNow) | ✅ GA | A2A Tool | Caller assumes responsibility for third-party data/security |
| Any OpenAPI endpoint | ✅ GA | OpenAPI Tool | Broad compatibility; not full agent-to-agent protocol |
| Classic Connected Agents | ❌ Deprecated | `ConnectedAgentToolDefinition` | Retire March 31, 2027 — migrate to Workflows now |

---

---

> 📎 How each of the 62 Key Architectural Principles applies to CS and Foundry in the parent agent role is mapped in [1a. Alignment to Key Architectural Principles](../section-align).

<h3 style="color:#323130;font-weight:700;margin-top:1.5rem;">📚 References</h3>

- [Microsoft — Copilot Studio vs Foundry](https://techcommunity.microsoft.com/blog/microsoft-security-blog/microsoft-copilot-studio-vs-microsoft-foundry-building-ai-agents-and-apps/4483160)
- [Microsoft CAF — Platform Selection](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/technology-solutions-plan-strategy)
- [Foundry — Workflow Orchestration Patterns](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/workflow)
- [Foundry — A2A Tool (Agent-to-Agent)](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/tools/agent-to-agent)
- [Agent Framework — Concurrent Orchestration](https://learn.microsoft.com/en-us/agent-framework/workflows/orchestrations/concurrent)
- [Agent Framework — Magentic Orchestration](https://learn.microsoft.com/en-us/agent-framework/workflows/orchestrations/magentic)
- [Microsoft Inside Track — CS or Foundry](https://www.microsoft.com/insidetrack/blog/customer-questions-answered-should-i-use-copilot-studio-or-azure-ai-foundry-to-build-my-agent/)
- [Copilot Studio — Add Other Agents](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-add-other-agents)
- [Copilot Studio — A2A Protocol](https://learn.microsoft.com/en-us/microsoft-copilot-studio/add-agent-agent-to-agent)
- [AgentArch Benchmark — Multi-Agent Architectures (arXiv 2509.10769)](https://arxiv.org/abs/2509.10769)
- [AutoGen — Hierarchical Multi-Agent (arXiv 2308.08155)](https://arxiv.org/abs/2308.08155)
- [Agent Factory — Use Cases & Design Patterns](https://azure.microsoft.com/en-us/blog/agent-factory-the-new-era-of-agentic-ai-common-use-cases-and-design-patterns/)
- [Microsoft Multi-Agent Reference Architecture](https://microsoft.github.io/multi-agent-reference-architecture/docs/reference-architecture/Reference-Architecture.html)
{: .ref-grid}

---
