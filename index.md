---
layout: default
title: Enterprise Multi-Agent Architecture
nav_order: 1
has_children: true
nav_fold: true
has_toc: false
permalink: /
page_classes: home-xl
---

# 🤖 Enterprise Multi-Agent Architecture
> ✍️ **Author**: Vineet Kaul · Principal PM Architect · vineetkaul@microsoft.com

<div class="system-descriptor">
<strong>🎯 System Purpose</strong><br><br>
This document evaluates an enterprise agent architecture built around a <strong>single point of entry</strong> — a gateway agent that receives every user request, determines intent, and routes it to the right specialist agent while preserving full session context across all hops.<br><br>
Each agent in the hierarchy is independently equipped with <strong>tools, workflows, connectors, and MCP servers</strong> that it can invoke to retrieve information, execute business logic, or interact with line-of-business systems. No agent is a passive router — every agent at every tier can act.<br><br>
The architecture is <strong>platform-agnostic by design</strong>. Any agent in the hierarchy — at the gateway tier, domain tier, or specialist tier — may be a <strong>Copilot Studio agent, an Azure AI Foundry agent, or any agent built on non-Microsoft technology</strong>. Platform choice per agent does not break the orchestration hierarchy; it is governed by what that agent needs to do, not by which platform built it.<br><br>
The evaluation criteria, principles, and recommendations in this document are all anchored to this system model: one entry point, context-preserving routing, platform-agnostic agents, and enterprise-grade governance from day one.
</div>

---

## 📋 Document Structure

| Section | Title | Purpose |
|---|---|---|
| [📐 Section 1](./section-criteria) | **Recommendation Criteria** | The 18-criterion evaluation framework applied to every platform comparison and architectural decision. Criteria are classified P0 (foundational), P1 (must-have), and P2 (important). |
| [🧠 Section 2](./section-principles) | **Key Architectural Principles** | 62 research-backed principles that govern every design decision — from minimising LLM calls and externalizing termination logic to idempotency, security trimming, continuous evaluation, interoperability, and factory build process. Incorporates all Design Considerations. |
| [🏗️ Section 3](./section-1) | **Representative Architecture** | The full 3-tier platform stack diagram (Gateway → Domain → Specialist), the orchestrator-workers research evidence, and the star topology anti-pattern analysis. |
| [🔗 Section 3a](./section-align) | **Alignment to Key Architectural Principles** | Maps the 62 Key Architectural Principles to this architecture — showing how each principle is implemented in CS and Foundry. |
| [🔄 Section 4](./section-2) | **How a User Instruction is Processed** | End-to-end trace of a single user message — from channel entry through gateway routing, domain agent invocation, tool execution, and final response — with correlationId propagation at every hop. |
| [👑 Section 5](./section-3) | **CS vs. Foundry — Parent / Master Agent** | Direct comparison of Copilot Studio and Azure AI Foundry as the top-level orchestrator. Covers routing capability, instruction complexity, A2A support, ALM maturity, and governance. |
| [🔗 Section 6](./section-3b) | **CS vs. Foundry — Child / Connected Agent** | Direct comparison of CS and Foundry in the sub-agent role — inline child, connected agent, and external Foundry agent patterns with practical guidance on when to use each. |
| [💾 Section 7](./section-5) | **Session Management & Persistent Storage** | Context architecture across three tiers (active session, session store, long-term memory), storage decisions per platform per requirement, and the infrastructure matrix driven by architecture lean. |
| [🔌 Section 8](./section-6) | **Infrastructure Requirements** | The definitive answer on what infrastructure to build — driven by architecture lean (CS-heavy vs Foundry-heavy vs Preview acceptable). Rules out SQL Server; resolves CosmosDB, Redis, and Dataverse placement. |
| [🔧 Section 9](./section-7) | **Agentic Framework and Agent 365** | Recommendation on agentic frameworks (Semantic Kernel, AutoGen, smolagents), the role of Agent 365 in governance, CoE Toolkit deployment, and Entra Agent ID. |
| [📌 Section 10](./section-8) | **Final Summary** | What to build, what to avoid, and the month-by-month 100-agent delivery timeline. One-page decision guide for platform leads and architects. |
| [📋 Section 11](./section-9) | **Agent Design Template** | The factory input card — 13 fields that must be completed before any agent is built. Covers identity, scope, I/O contract, tools, security, observability, ALM, and definition of done. |
| [📖 Section 12](./section-10) | **Implementation Guide** | Concise implementation guidance for all 62 Key Architectural Principles — how each principle is applied in Copilot Studio and Azure AI Foundry, with platform-specific steps, thresholds, and checklists. Navigate here from any principle via its [→ PN] link. |
| [🎓 Karpathy](./section-karpathy) | **Academic Analysis — Andrej Karpathy** | Alignment of all 62 Key Principles against Karpathy's published positions on LLM OS, context engineering, autonomy sliders, Software 3.0, and evals-first development. |
| [🎓 Sutskever](./section-sutskever) | **Academic Analysis — Ilya Sutskever** | Alignment against Sutskever's frameworks: jagged frontier, weak-to-strong generalization, SSI safety-first, and unpredictability-scales-with-reasoning. |
| [🎓 Raschka](./section-raschka) | **Academic Analysis — Sebastian Raschka** | Alignment against Raschka's work on LLM evaluation, fine-tuning decision theory, model internals, and state-of-LLMs assessments. |
| [🎓 Shazeer](./section-shazeer) | **Academic Analysis — Noam Shazeer** | Alignment against Shazeer's hardware-algorithm co-design philosophy: MQA, MoE, SwiGLU, CharacterFlywheel, and the Kaiju serving architecture. |

---

---
