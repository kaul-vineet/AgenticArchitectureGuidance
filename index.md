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

---

## 📋 Document Structure

| Section | Title | Purpose |
|---|---|---|
| [📐 Section 1](./section-criteria) | **Recommendation Criteria** | The 21-criterion evaluation framework applied to every platform comparison and architectural decision. Criteria are classified P0 (foundational), P1 (must-have), and P2 (important). |
| [🧠 Section 2](./section-principles) | **Key Architectural Principles** | 62 research-backed principles that govern every design decision — from minimising LLM calls and externalizing termination logic to idempotency, security trimming, continuous evaluation, interoperability, and factory build process. |
| [🏗️ Section 3](./section-1) | **Representative Architecture** | The full 3-tier platform stack diagram (Gateway → Domain → Specialist), the orchestrator-workers research evidence, and the star topology anti-pattern analysis. |
| [🔗 Section 4](./section-align) | **Alignment to Key Architectural Principles** | Maps the 62 Key Architectural Principles to this architecture — showing how each principle is implemented in CS and Foundry. |
| [🔄 Section 5](./section-2) | **How a User Instruction is Processed** | End-to-end trace of a single user message — from channel entry through gateway routing, domain agent invocation, tool execution, and final response — with correlationId propagation at every hop. |
| [👑 Section 6](./section-3) | **CS vs. Foundry — Parent / Master Agent** | Direct comparison of Copilot Studio and Azure AI Foundry as the top-level orchestrator. Covers routing capability, instruction complexity, A2A support, ALM maturity, and governance. |
| [🔗 Section 7](./section-3b) | **CS vs. Foundry — Child / Connected Agent** | Direct comparison of CS and Foundry in the sub-agent role — inline child, connected agent, and external Foundry agent patterns with practical guidance on when to use each. |
| [💾 Section 8](./section-5) | **Session Management & Persistent Storage** | Context architecture across three tiers (active session, session store, long-term memory), storage decisions per platform per requirement, and the infrastructure matrix driven by architecture lean. |
| [🔌 Section 9](./section-6) | **Infrastructure Requirements** | The definitive answer on what infrastructure to build — driven by architecture lean (CS-heavy vs Foundry-heavy vs Preview acceptable). Rules out SQL Server; resolves CosmosDB, Redis, and Dataverse placement. |
| [🔧 Section 10](./section-7) | **Agentic Framework and Agent 365** | Recommendation on agentic frameworks (Semantic Kernel, AutoGen, smolagents), the role of Agent 365 in governance, CoE Toolkit deployment, and Entra Agent ID. |
| [📋 Section 11](./section-9) | **Agent Design Template** | The factory input card — 13 fields that must be completed before any agent is built. Covers identity, scope, I/O contract, tools, security, observability, ALM, and definition of done. |
| [📖 Section 12](./section-10) | **Implementation Guide** | Concise implementation guidance for all 62 Key Architectural Principles — how each principle is applied in Copilot Studio and Azure AI Foundry, with platform-specific steps, thresholds, and checklists. Navigate here from any principle via its [→ PN] link. |
| [📊 Section 13](./section-scoring) | **Platform Scoring** | All 21 recommendation criteria scored across three deployment scenarios — CS as Parent, Foundry as Parent, CS as Child, Foundry as Child — using the Asymmetric Priority-Weighted method. Includes session management and infrastructure impact on every score. |
| [🔎 Prompt](./prompt-review-architecture) | **Review Architecture (Prompt)** | Reusable Claude prompt for reviewing a proposed agentic architecture. Interviews the architect across 52 questions, checks compliance against all platform rules (CS, Foundry, SK/AutoGen), detects violations and anti-patterns, and produces an ASCII diagram with a structured verdict. |
| [🔍 Prompt](./prompt-discover-principles) | **Discover New Principles (Prompt)** | Reusable Claude prompt for discovering new architectural principles via web search — includes scope filter, source landscape, deduplication guard against P1–P62, and output formats for all three artefacts (References, Principles, Implementation Guide). |

---

---
