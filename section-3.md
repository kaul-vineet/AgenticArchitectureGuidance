---
layout: default
title: "👑 3. As Parent / Master Agent"
parent: Enterprise Multi-Agent Architecture
nav_order: 5
---

## 3. 👑 As Parent / Master Agent

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

<h3 style="color:#107c10;font-weight:700;border-left:4px solid #107c10;padding-left:0.6rem;margin-top:1.5rem;">🏆 Recommendation</h3>

| Criterion | 🟦 Copilot Studio as Parent | 🟧 Foundry as Parent |
|---|---|---|
| ⚡ Speed | ✅ **Wins** — hours to first agent vs. days | ⚠️ Slower — Workflow YAML + infra provisioning required |
| 🧩 No-code authoring | ✅ **Wins** — no-code native; domain teams self-serve | ⚠️ Portal templates for 3 patterns; Concurrent and Magentic require SDK code |
| 📈 Scale to 100+ agents | ✅ **Wins** — agent templates, Component Collections, PP Pipelines built for factory-scale | ⚠️ Scalable but requires custom ALM pipelines; no out-of-box factory model |
| 🔵 Reduced complexity | ✅ **Wins** — Dataverse only; no extra infra | ❌ Cosmos DB + Redis + Azure Functions + Azure AI Search required |
| 🔗 A2A / Agent interoperability | ✅ CS supports A2A connected agents — CS, Foundry, Agentforce, ServiceNow via A2A | ✅ Foundry A2A Tool (GA) — calls any A2A-compliant endpoint across clouds and orgs |
| 🌐 MSFT + non-MSFT interoperability | ✅ **Wins** — A2A connects to Agentforce, ServiceNow, any A2A endpoint; 1,400+ PA connectors for LOB | ✅ A2A Tool GA — fewer native connectors than CS; OpenAPI covers remaining integrations |
| 🛡️ Governance | ✅ **Wins** — Agent 365 + CoE Toolkit + Entra Agent ID natively in M365 Admin Center; no custom infra | ⚠️ Azure Policy + Entra + App Insights; no unified agent registry; governance requires custom build |
| 📋 Logging & observability | ⚠️ CS transcript + Dataverse; limited distributed tracing across hops | ✅ **Wins** — full OpenTelemetry + App Insights; every agent hop, tool call, and handoff traceable |
| 🔀 Orchestration at scale (100+ agents) | ⚠️ Sequential only; domain hierarchy required to avoid star-topology bottleneck | ✅ **Wins** — Concurrent fan-out, Magentic supervisor, Group Chat, Handoff all GA |
| 🏢 LOB system integration | ✅ **Wins** — 1,400+ PA connectors; SAP, Dynamics 365, Oracle, Salesforce, ServiceNow with no custom code | ⚠️ Foundry connectors available; LOB typically requires Azure Functions or OpenAPI tool definitions |
| 📱 Multi-channel exposure | ✅ **Wins** — Teams, SharePoint, M365 Copilot, web, mobile, Direct Line zero-config | ⚠️ API endpoint only; channel routing requires custom front-end or CS wrapper |
| 🥇 Complex agent instructions | ⚠️ 8,000 char limit; complex domain prompts require compression | ✅ **Wins** — no system prompt size limit; full parameter control (temperature, top-p, max tokens) |
| 🥇 Supervisor pattern | ✅ CS Topics act as generative supervisor routing to specialist agents | ✅ Magentic pattern — dynamic specialist selection, stall detection, replanning |
| 🥇 Supervisor-as-tool | ✅ CS published as A2A endpoint; callable by Foundry or non-MSFT orchestrators | ✅ Foundry agent callable as A2A tool; parent retains full control across delegation |
| 🥇 Hierarchical orchestration | ✅ Gateway → Domain → Specialist fully supported; each tier is independent CS agent with own ALM | ✅ Workflow nodes chain into multi-tier hierarchies; no depth limit in Workflows (unlike deprecated Classic) |
| 🥈 Custom orchestration | ❌ No SDK access; Topics only | ✅ **Wins** — full Python, C#, JS, Java SDK; custom state machines and conditional branching |
| 🥈 Network / mesh pattern | ⚠️ Possible via A2A connected agents but no peer-to-peer routing; always orchestrator-controlled | ⚠️ Group Chat pattern supports multi-agent lateral collaboration; pure peer-to-peer not recommended (36% hallucination rate in unstructured ReAct per AgentArch benchmark) |

> **Use Copilot Studio as the parent/master agent** for the majority of your 100+ agent estate. CS wins on speed, no-code authoring, governance, LOB integration, multi-channel exposure, and factory-scale ALM — all critical requirements for enterprise delivery at pace. All P1 orchestration patterns (Supervisor, Supervisor-as-tool, Hierarchical) are fully supported in CS through generative Topics and A2A connected agents. Agent 365 + CoE Toolkit provide enterprise-grade governance without custom infrastructure. 1,400+ Power Automate connectors eliminate custom LOB integration work. The factory model — agent templates, Component Collections, Power Platform Pipelines — scales from 1 to 100+ agents without re-architecting. Avoid unstructured peer-to-peer (network/mesh) patterns on any platform — AgentArch benchmark shows 36% hallucination rate in unstructured multi-agent ReAct vs. 0% in orchestrator-led configurations.
{: .recommendation}

> **Introduce Foundry as parent only** for domains that explicitly require capabilities unavailable in CS: parallel fan-out to multiple agents simultaneously (Concurrent pattern), dynamic supervisor orchestration with stall detection and replanning (Magentic pattern), complex system prompts beyond the 8,000 character limit, full OpenTelemetry distributed tracing across deep agent chains, or compliance-grade BYO Cosmos DB storage with CMK. Accept the additional infrastructure overhead (Cosmos DB, Redis, Azure Functions), custom ALM pipelines, and the absence of an out-of-box agent registry. Target fewer than 3 Foundry-parent domains out of your total agent estate — and plan Foundry governance as a deliberate engineering investment, not an assumption.
{: .recommendation}

---

<h3 style="color:#323130;font-weight:700;border-left:4px solid #323130;padding-left:0.6rem;margin-top:1.5rem;">📝 Design Notes — Alignment to Key Architectural Principles</h3>

Both parent scenarios are evaluated against the 28 Key Architectural Principles. The table below shows how each principle is satisfied — or where trade-offs apply — for CS as Parent and Foundry as Parent.

<p><span class="adh-full">✅ Full</span> Fully met — no workarounds required &nbsp;&nbsp; <span class="adh-partial">🟡 Partial</span> Met with minor gaps or config required &nbsp;&nbsp; <span class="adh-maybe">🔶 Maybe</span> Possible but requires significant custom work &nbsp;&nbsp; <span class="adh-no">❌ No</span> Not met — known platform limitation</p>

| # | Principle | 🟦 CS as Parent | 🟧 Foundry as Parent |
|---|---|---|---|
| 1 | Minimise LLM calls | <span class="adh-full">✅ Full</span> CS Topics are deterministic — no LLM for routing; PA flows handle all tool calls | <span class="adh-partial">🟡 Partial</span> Workflow nodes can be deterministic; planning requires LLM — use Workflows, not Crews, as default |
| 2 | Default to Flows, escalate to Crews | <span class="adh-full">✅ Full</span> Topics + PA flows are the native default; Foundry added only for open-ended tasks | <span class="adh-full">✅ Full</span> Sequential / HITL Workflows for 80%; Magentic / Group Chat only for genuinely exploratory tasks |
| 3 | Clarification before action | <span class="adh-full">✅ Full</span> Disambiguation Topics built-in before every agentic branch | <span class="adh-maybe">🔶 Maybe</span> Custom tool or Workflow clarification node required; not a native no-code feature |
| 4 | External verification — never self-check | <span class="adh-full">✅ Full</span> PA flow read-back after every write; separate tool call from the generating agent | <span class="adh-full">✅ Full</span> Verification tool call in SDK orchestration code; structured JSON enables deterministic checks |
| 5 | Externalize termination logic | <span class="adh-full">✅ Full</span> Topic conditions + session turn limits (100 turns, 60 min) enforced at platform level | <span class="adh-full">✅ Full</span> Workflow termination nodes + SDK `max_steps`; never in LLM prompt |
| 6 | Tool argument quality | <span class="adh-full">✅ Full</span> PA connector schema + typed YAML I/O contracts for connected agents; structured error responses | <span class="adh-full">✅ Full</span> Structured JSON output schemas per Workflow node; argument validation at every hop |
| 7 | Consolidate tools | <span class="adh-full">✅ Full</span> PA flows batch multiple operations into one connector call | <span class="adh-partial">🟡 Partial</span> Azure Functions or OpenAPI tool definitions can consolidate; no native merge primitive |
| 8 | Multi-agent is a reliability tax | <span class="adh-full">✅ Full</span> Child agents only at genuine domain seams; inline child adds zero extra hop | <span class="adh-full">✅ Full</span> Each Workflow hop adds one LLM inference cost; accept only where parallel or specialist value is proven |
| 9 | Context hygiene at scale | <span class="adh-partial">🟡 Partial</span> Inline child: parent context shared; connected: context reset at boundary; correlationId links | <span class="adh-full">✅ Full</span> Per-thread context per sub-agent; explicit state passed; compaction in SDK code |
| 10 | Sensitive state in side channel | <span class="adh-full">✅ Full</span> Key Vault via PA connector; Dataverse column-level security; credentials never in Topics | <span class="adh-full">✅ Full</span> Managed Identity + Key Vault; credentials never in Workflow YAML or agent system prompt |
| 11 | Tool accuracy > 90% as deployment gate | <span class="adh-full">✅ Full</span> Deployment gate via CoE Toolkit quality metrics before each PP Pipelines promotion | <span class="adh-full">✅ Full</span> App Insights evaluation threshold enforced before production promotion |
| 12 | Human-in-the-loop for irreversible actions | <span class="adh-full">✅ Full</span> PA approval flows as deterministic gates before write-backs to HR, CRM, financial systems | <span class="adh-full">✅ Full</span> HITL Workflow node; approval gate before irreversible downstream writes |
| 13 | Use lowest complexity | <span class="adh-full">✅ Full</span> CS Topics + PA flows; Dataverse only; no extra infra for majority of agents | <span class="adh-maybe">🔶 Maybe</span> Only when CS cannot meet requirements (parallel fan-out, Magentic, deep traces, prompt > 8,000 chars) |
| 14 | Justify every agent boundary | <span class="adh-full">✅ Full</span> Domain hierarchy enforces boundaries; Gateway → Domain → Specialist; no lateral calls | <span class="adh-full">✅ Full</span> Same hierarchy; each Workflow sub-agent boundary justified by domain isolation or compliance need |
| 15 | Single responsibility per agent | <span class="adh-full">✅ Full</span> Each CS agent has a narrow domain; CoE Toolkit detects agent overlap | <span class="adh-full">✅ Full</span> Each Foundry agent scoped to one specialist task; App Insights flags redundant calls |
| 16 | Right-size the model per agent role | <span class="adh-no">❌ No</span> Model locked to Microsoft-managed — consistent but no per-agent tuning | <span class="adh-full">✅ Full</span> Per-child model selection; smaller models for extraction, classification, formatting |
| 17 | Validate output before passing downstream | <span class="adh-partial">🟡 Partial</span> Typed YAML I/O for connected agents; Topic conditions check quality; inline relies on LLM judgment | <span class="adh-full">✅ Full</span> Structured JSON schema validation; orchestrator halts or retries on malformed output |
| 18 | Surface errors explicitly | <span class="adh-full">✅ Full</span> Connector structured error responses; Topic error branches; never silent failure | <span class="adh-full">✅ Full</span> App Insights alerts; SDK try/except; errors propagated upward to orchestrator |
| 19 | Idempotency for all writes | <span class="adh-full">✅ Full</span> Idempotency key in PA flows; safe to re-trigger | <span class="adh-full">✅ Full</span> Idempotency key in Azure Functions or OpenAPI tool definitions |
| 20 | Kill switch | <span class="adh-partial">🟡 Partial</span> Agent disabled in CS portal; PA flow disabled independently; no redeployment — but no fleet-level kill primitive | <span class="adh-partial">🟡 Partial</span> Foundry project or Workflow disabled via Azure Portal; no fleet-level kill-switch primitive |
| 21 | Security trimming in every agent | <span class="adh-full">✅ Full</span> Dataverse RLS per CS agent; agent cannot return data above user entitlement | <span class="adh-full">✅ Full</span> Managed Identity RBAC per child agent; trimming enforced in each agent's response logic |
| 22 | Content safety at every layer | <span class="adh-full">✅ Full</span> AI Builder content safety filter per CS agent; applied at input and output | <span class="adh-full">✅ Full</span> Azure AI Content Safety filter per Foundry agent; applied at every agent boundary |
| 23 | Scope agent authority to operations | <span class="adh-partial">🟡 Partial</span> Topic-level permissions; PA connector scope limited but no blast radius caps | <span class="adh-full">✅ Full</span> RBAC per Workflow node; rate limits and blast radius caps enforced in SDK code |
| 24 | Instrument all handoffs | <span class="adh-partial">🟡 Partial</span> correlationId + Dataverse log per hop; limited distributed tracing across deep chains | <span class="adh-full">✅ Full</span> Full OpenTelemetry via App Insights; every agent hop, tool call, and handoff traceable |
| 25 | Automated evaluation in CI/CD | <span class="adh-full">✅ Full</span> Power Platform Pipelines with solution validation and quality checks | <span class="adh-full">✅ Full</span> Azure DevOps / GitHub Actions with automated evaluation step per agent |
| 26 | Evaluate continuously in production | <span class="adh-full">✅ Full</span> CoE Toolkit dashboards; intent resolution and tool accuracy monitoring | <span class="adh-full">✅ Full</span> App Insights dashboards; custom evaluation queries; continuous production monitoring |
| 27 | Maintain agent inventory | <span class="adh-full">✅ Full</span> Agent 365 unified registry; every CS agent registered, owned, and versioned | <span class="adh-maybe">🔶 Maybe</span> No native registry; custom Azure Policy + Entra tracking required to prevent shadow agents |
| 28 | Externalize and version-control prompts | <span class="adh-full">✅ Full</span> Agent instructions in CS solution; promoted via PP Pipelines; controlled rollout | <span class="adh-full">✅ Full</span> Agent prompts in Azure DevOps / GitHub Actions; versioned separately from Workflow YAML |

---

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
