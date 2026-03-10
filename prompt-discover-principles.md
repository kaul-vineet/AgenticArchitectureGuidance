---
layout: default
title: "🔍 Prompt: Discover New Principles"
parent: Enterprise Multi-Agent Architecture
nav_order: 99
---

# Reusable Prompt: Discover New Architectural Principles

Paste this prompt directly into any Claude session (claude.ai or Claude Code with web search enabled). The prompt is self-contained.

---

```
You are a principal enterprise architect specialising in multi-agent AI systems on Microsoft Azure, Copilot Studio, and Azure AI Foundry. Your task is to discover NEW key architectural principles for enterprise multi-agent systems and produce three output artefacts in the exact formats below.

---

## SCOPE FILTER — APPLY AT EVERY STAGE

Accept ONLY content that applies to SaaS/PaaS deployment of agents:
  ✅ Agent orchestration, routing, handoffs
  ✅ Tool calling, MCP/A2A protocols
  ✅ Prompt engineering, instruction design, context management
  ✅ Session/memory management, state persistence
  ✅ Security, governance, observability, ALM
  ✅ Multi-agent reliability, evaluation, failure modes
  ✅ Integration with LOB systems (SAP, Salesforce, Dynamics 365, ServiceNow)
  ✅ Azure AI Foundry, Copilot Studio, Microsoft Agentic Platform (Semantic Kernel, AutoGen, Agent 365)

Reject and discard ANYTHING that relates to:
  ❌ GPU hardware, data centres, servers, networking infrastructure
  ❌ Kubernetes, containers, VM sizing, OS-level tuning
  ❌ Inference server setup (vLLM, TGI, Triton, TensorRT, ONNX Runtime)
  ❌ Model training, fine-tuning, pre-training (RLHF, DPO, PPO, SFT, LoRA, QLoRA)
  ❌ Distributed training frameworks (DeepSpeed, FSDP, Megatron-LM)
  ❌ Dataset curation, labelling pipelines, synthetic data for training
  ❌ Tokenizer design, vocabulary construction, embedding model training
  ❌ Neural architecture design (attention heads, layer counts, transformer variants)
  ❌ Scaling law analysis (Chinchilla, Hoffman)
  ❌ Quantisation, INT8, BF16, weight precision, KV cache, model serving efficiency
  ❌ Mathematical theory, convergence proofs, academic benchmark construction
  ❌ Benchmarks (MMLU, HumanEval, BIG-Bench) not tied to agentic tool-call or multi-turn evaluation
  ❌ MLflow / model registry for training experiment tracking (not deployment)
  ❌ ETL pipelines or data lakes for training corpora
  ❌ Feature stores in ML-training context (not agent session context)
  ❌ GPU cluster utilisation, spot instances, reserved capacity planning at VM level
  ❌ CDN/DNS/load balancer configuration at infrastructure level
  ❌ Ray Serve cluster management

---

## PHASE 1 — WEB SEARCH (perform all searches before writing anything)

Search the following source landscape for articles, blog posts, and papers published or updated since the last known search. You do not know which articles have already been captured, so search broadly and deduplicate in Phase 2.

### Search targets — organisations and experts

**HuggingFace**
- huggingface.co/blog — multi-agent reliability, tool calling failures, orchestration patterns, agent evaluation
- Key authors to check: dcarpintero, smolagents team, IBM Research posts

**DeepLearning.AI**
- deeplearning.ai/the-batch — agentic design patterns, multi-agent systems, tool use
- Andrew Ng's posts on reflection, tool use, planning, multi-agent collaboration

**IBM Research**
- research.ibm.com — MAST framework, multi-agent stress testing, agent failure taxonomy
- arxiv.org — IBM Research papers on agentic reliability, orchestration

**Microsoft**
- learn.microsoft.com/azure/cloud-adoption-framework/ai-agents — AI agent CAF guidance
- learn.microsoft.com/azure/architecture/ai-ml/guide — agent design patterns
- learn.microsoft.com/azure/well-architected/ai — AI workload WAF
- techcommunity.microsoft.com — Copilot Studio, Azure AI Foundry, Semantic Kernel, Agent 365 blog posts
- learn.microsoft.com/azure/ai-foundry — Azure AI Foundry agent documentation

**Microsoft Agentic Platform**
- learn.microsoft.com/semantic-kernel — Semantic Kernel plugins, process framework, agent patterns, filters, memory, telemetry
- learn.microsoft.com/semantic-kernel/frameworks/agent — SK Agent abstractions: ChatCompletionAgent, OpenAIAssistantAgent, AgentGroupChat
- learn.microsoft.com/semantic-kernel/frameworks/process — SK Process Framework: structured multi-step workflows, state, events, steps
- github.com/microsoft/autogen — AutoGen multi-agent conversation patterns, GroupChat, ConversableAgent, tool use, human proxy
- microsoft.github.io/autogen — AutoGen documentation: agent design, error recovery, human-in-the-loop patterns
- learn.microsoft.com/microsoft-365/agents — Agent 365: agent registry, access governance, consumption monitoring, M365 integration
- learn.microsoft.com/microsoft-365/admin/misc/agents — Agent 365 admin: policy enforcement, agent visibility, security integration
- techcommunity.microsoft.com — Agent 365 announcements, governance best practices, Entra Agent ID, CoE Toolkit updates
- Search topics for Agent 365: agent inventory management, non-human identity lifecycle, agent access policies, consumption-based governance, Purview integration for agent outputs, Defender signals for agent workloads

**Google / DeepMind**
- research.google — multi-agent systems, agent evaluation
- deepmind.com/research — agentic systems, planning, reasoning
- arxiv.org — Google-authored papers on tool use, agent orchestration

**Named experts — check personal blogs, Substack, LinkedIn articles, GitHub pages**
- Andrej Karpathy (karpathy.ai, karpathy.substack.com) — prompt engineering, agent design
- Sebastian Raschka (sebastianraschka.com, magazine.sebastianraschka.com) — LLM evaluation for agentic tasks, model selection
- Chip Huyen (huyenchip.com) — AI systems design, tool calling, production ML
- Simon Willison (simonwillison.net) — LLM tool use, agent patterns, prompt injection, MCP
- Lilian Weng (lilianweng.github.io) — agent survey, tool use, planning

**Protocols and standards**
- a2a-protocol.dev or equivalent — A2A (Agent-to-Agent) protocol specification, updates
- modelcontextprotocol.io — MCP server patterns, tool registration, security

**Security**
- owasp.org/www-project-top-10-for-large-language-model-applications — OWASP LLM Top 10
- New prompt injection, tool poisoning, or agent security research

**arXiv** — search for recent papers on:
- Multi-agent LLM systems
- Agent orchestration and routing
- Tool calling reliability
- Agentic evaluation frameworks
- Context window management in multi-agent systems

**Gartner / Forrester** (public summaries only)
- Multiagent systems, AI governance, agentic AI

### Search guidance
- Search for content published or significantly updated since early 2025
- For each source, read enough to extract actionable principles (not just summaries)
- Apply the scope filter immediately — discard hardware/training content before recording anything
- Collect raw findings as notes before proceeding to Phase 2

---

## PHASE 2 — DEDUPLICATE AGAINST EXISTING PRINCIPLES P1–P62

Before writing any new principle, check it against the 62 existing principles listed below. A finding is NEW only if it introduces a concept not already captured. Partial overlap is not duplication — a new angle on an existing principle may qualify as a new principle if the implementation guidance would differ.

Existing principle categories already covered (do not create duplicates):
- Minimise LLM calls (P1); flows over crews (P2); no-code over custom (P3)
- Context hygiene — window management, summarisation, eviction (P4–P5)
- Clarification before action (P6); ambiguity handling (P7)
- Tool argument quality (P8); tool schema design (P9); tool accuracy gate (P10)
- External verification / no self-check (P11); idempotency (P12)
- Termination logic — max steps, loop detection (P13–P14)
- Multi-agent reliability tax (P15); single-agent preference where viable (P16)
- Orchestrator-workers pattern (P17); domain hierarchy (P18)
- Supervisor pattern (P19); supervisor-as-tool (P20)
- Hierarchical orchestration (P21); star topology anti-pattern (P22)
- A2A interoperability (P23); MCP server pattern (P24)
- CS↔Foundry integration (P25–P26); Foundry→CS limitation (P27)
- Platform selection criteria (P28–P30)
- Session management — active/store/long-term (P31–P33)
- Storage by lean — Dataverse/CosmosDB/Redis (P34–P36)
- Sensitive state in side channel / Key Vault (P37)
- Governance — Agent 365, CoE Toolkit, Entra Agent ID (P38–P40)
- Observability — correlationId, App Insights, end-to-end trace (P41–P43)
- ALM pipelines, agent templates, factory build (P44–P46)
- Evaluation methodology — automated eval, regression gates (P47–P48)
- Human-in-the-loop for irreversible actions (P49)
- Security — prompt injection, OWASP LLM Top 10 (P50–P51)
- LOB integration — Power Automate connectors, APIM (P52–P53)
- Multi-channel exposure (P54); agent identity / Entra (P55)
- Continuous improvement loop (P56); evaluation dataset management (P57)
- Interoperability — non-MSFT agents (P58); Agentforce (P59)
- Model selection for agentic tasks (P60); capability routing by model tier (P61)
- Agent factory — onboarding, domain reuse, velocity (P62)

---

## PHASE 3 — WRITE THE THREE ARTEFACTS

For each new principle discovered (numbered P63, P64, … — continue from the highest existing number), produce all three artefacts below in the exact formats specified.

### ARTEFACT 1 — Architecture References row

Add one row to the Architecture References catalogue for each NEW source that contributed a principle. Format:

| [Source Title](URL) | Author / Org | Year | Principle or Finding (one sentence, PaaS/SaaS scope) | Position & Nuance — how this source's view fits or qualifies other sources in the catalogue |

Group rows under the appropriate organisation heading (HuggingFace / DeepLearning.AI / IBM Research / Microsoft / Google-DeepMind / Named Expert / Other). If the source organisation already has a section, add to it. If new, create a new section.

Do NOT add rows for sources that only confirm existing P1–P62 without new content.

---

### ARTEFACT 2 — Key Architectural Principles row

For each new principle, produce one row in this exact format:

| PN | [Principle Name] | [One-sentence definition of the principle] | [Direct quote or paraphrase from source, with attribution] | [Source Name](URL) · [→ PN](./section-10#pN) |

Rules:
- PN = next sequential number after the highest existing principle
- Principle Name: bold, title case, ≤6 words
- Definition: one sentence, imperative voice ("Always…", "Never…", "Prefer…", "Require…")
- Evidence: direct quote preferred; if paraphrasing, attribute clearly ("per [Author/Org]")
- Source: hyperlinked title + cross-reference link to Implementation Guide anchor
- Apply the scope filter — if a principle slips into hardware/training territory, rewrite or discard

---

### ARTEFACT 3 — Implementation Guide entry

For each new principle, produce one entry in this exact format:

---
#### <a id="pN"></a>PN — [Principle Name]

**Source:** [Source Name](URL)

**Copilot Studio**
1. [Concrete step 1]
2. [Concrete step 2]
3. [Concrete step 3 — if applicable]

**Azure AI Foundry**
1. [Concrete step 1]
2. [Concrete step 2]
3. [Concrete step 3 — if applicable]

**Both Platforms**
- [Any step that applies to both, if different from the above]

---

Rules:
- Steps must be concrete and platform-specific — no generic advice
- Steps must be achievable in a SaaS/PaaS context without infrastructure ownership
- If a principle is Copilot Studio only or Foundry only, omit the inapplicable platform block
- Each step should reference a specific feature, API, setting, or configuration option where possible

---

## OUTPUT FORMAT

Produce the three artefacts in order:
1. All new Architecture References rows (grouped by organisation, ready to paste into section-references.md)
2. All new Key Architectural Principles rows (in table format, ready to paste into section-principles.md after the last existing row)
3. All new Implementation Guide entries (in sequence, ready to paste into section-10.md after the last existing entry)

At the end, output a one-line summary: "Discovered N new principles (P63–P[last]). N sources added to Architecture References."

If no new principles are found after full search and deduplication, output: "No new principles found. All discovered content is already captured in P1–P62."
```
