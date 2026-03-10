---
layout: default
title: "📚 Architecture References"
parent: Enterprise Multi-Agent Architecture
nav_order: 20
---

## 📚 Architecture References

A curated repository of architecture-specific articles, papers, and guidance on multi-agent systems. Only sources that provide architectural principles, design patterns, or structural guidance are included — not general AI/LLM introductions.

*The **Position & Nuance** column states the design position advocated by the source — framed so that the question "Does the current architecture adhere to this?" can be answered Yes / Partially / No.*

---

### **HuggingFace**

| Title | Author / Source | Date | Link | Position & Nuance |
|---|---|---|---|---|
| Design Patterns for Building Agentic Workflows | Diego Carpintero | 2025 | [↗](https://huggingface.co/blog/dcarpintero/design-patterns-for-building-agentic-workflows) | Agent topology must be hierarchical and orchestrator-controlled; context hygiene and tool argument quality must be managed as explicit architectural requirements, not implementation details |
| The Agentic Shift — Design Patterns for Building AI Systems | Diego Carpintero | 2025 | [↗](https://huggingface.co/blog/dcarpintero/design-patterns-for-building-ai-systems) | Agentic AI demands a fundamental shift in software architecture; the orchestration layer is the primary engineering surface — not prompt wording or model selection |
| Building Good Agents (smolagents docs) | HuggingFace | 2025 | [↗](https://huggingface.co/docs/smolagents/tutorials/building_good_agents) | LLM calls must be minimised by defaulting to deterministic code; tools must be consolidated where possible; every additional agent hop must be explicitly justified before it is added |
| Introducing smolagents: simple agents that write actions in code | HuggingFace | Jan 2025 | [↗](https://huggingface.co/blog/smolagents) | Agent actions should be expressed as executable code rather than JSON tool calls; minimal agent frameworks must be preferred over complex orchestration frameworks |
| What Are Agents? (smolagents conceptual guide) | HuggingFace | 2025 | [↗](https://huggingface.co/docs/smolagents/conceptual_guides/intro_agents) | The control flow of an agent must be explicitly designed; relying on emergent LLM behaviour to determine task sequencing is not an acceptable architectural approach |
| IBM and UC Berkeley Diagnose Why Enterprise Agents Fail — IT-Bench + MAST | IBM Research | Feb 2025 | [↗](https://huggingface.co/blog/ibm-research/itbenchandmast) | Focused single-domain agents accumulate half the failure modes of overloaded orchestrators (2.6 vs. 5.3 per trace); agent scope must be bounded as the primary reliability control |
| AssetOpsBench: Bridging the Gap Between AI Agent Benchmarks and Industrial Reality | IBM Research | 2025 | [↗](https://huggingface.co/blog/ibm-research/assetopsbench-playground-on-hugging-face) | Tool argument quality — not model capability — is the primary determinant of agent performance; improving tool definitions delivers more value than upgrading the underlying model |
| Neuro SAN Is All You Need — A Data-Driven Multi-Agent Orchestration Framework | Cognizant AI Lab | 2025 | [↗](https://huggingface.co/blog/danyoung/neuro-san-is-all-you-need) | Sensitive state and credentials must travel in a side channel, never through the conversation context; star topology single-orchestrators must be avoided |
| OpenEnv in Practice: Evaluating Tool-Using Agents in Real-World Environments | OpenEnv / Turing | 2025 | [↗](https://huggingface.co/blog/openenv-turing) | Ambiguity in user input must be resolved before routing; acting on ambiguous intent causes a 50pp accuracy collapse and is architecturally the most dangerous failure class |
| WorkflowDrivenAgent: A Novel Paradigm for Deterministic Multi-Agent AI Systems | Dariel Noel | 2025 | [↗](https://huggingface.co/blog/darielnoel/workflow-driven-agent) | Deterministic workflow-driven execution must be the default; autonomous planning agents must be reserved for tasks that are genuinely open-ended and cannot be deterministically specified |
| Building Blocks of Agentic Systems | Kseniase | 2024 | [↗](https://huggingface.co/blog/Kseniase/buildingblocks) | Memory, tools, and planning must be treated as distinct architectural layers with explicit, independent design decisions — they must not be conflated in a single agent prompt |

---

### **DeepLearning.AI**

| Title | Author / Source | Date | Link | Position & Nuance |
|---|---|---|---|---|
| Four AI Agent Strategies That Improve GPT-4 and GPT-3.5 Performance | Andrew Ng | Mar 2024 | [↗](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/) | Structured agentic patterns must be applied systematically; pattern architecture (reflection, tool use, planning, multi-agent) consistently outperforms brute-force model upgrades |
| Agentic Design Patterns Part 2: Reflection | Andrew Ng | Mar 2024 | [↗](https://www.deeplearning.ai/the-batch/agentic-design-patterns-part-2-reflection/) | Reflection loops must be applied selectively to quality-sensitive outputs only; applying reflection universally adds latency without proportional quality benefit |
| Agentic Design Patterns Part 3: Tool Use | Andrew Ng | Apr 2024 | [↗](https://www.deeplearning.ai/the-batch/agentic-design-patterns-part-3-tool-use/) | Tool use must be the default starting point for every agentic build; tool quality and argument clarity are the bottleneck — not model capability |
| Agentic Design Patterns Part 4: Planning | Andrew Ng | Apr 2024 | [↗](https://www.deeplearning.ai/the-batch/agentic-design-patterns-part-4-planning/) | LLM-driven planning must be reserved for genuinely complex multi-step tasks; it must not be applied to workflows where task sequencing can be guaranteed deterministically |
| Agentic Design Patterns Part 5: Multi-Agent Collaboration | Andrew Ng | Apr 2024 | [↗](https://www.deeplearning.ai/the-batch/agentic-design-patterns-part-5-multi-agent-collaboration/) | Specialised multi-agent systems must be preferred over generalist single agents when the task can be decomposed by domain; each agent must have a focused scope and bounded tool set |
| LLMs Evolve with Agentic Workflows, Enabling Autonomous Reasoning and Collaboration | Andrew Ng | 2024 | [↗](https://www.deeplearning.ai/the-batch/llms-evolve-with-agentic-workflows-enabling-autonomous-reasoning-and-collaboration/) | Human-in-the-loop must be maintained during early deployment; autonomy must be earned through demonstrated reliability — it must not be assumed by default at launch |

---

### **IBM Research**

| Title | Author / Source | Date | Link | Position & Nuance |
|---|---|---|---|---|
| Why Do Multi-Agent LLM Systems Fail? (MAST — arXiv 2503.13657) | Cemri, Pan, Yang et al. (IBM / UC Berkeley) | Mar 2025 | [↗](https://arxiv.org/abs/2503.13657) | Ambiguity resolution, external verification, and externalised termination logic are non-negotiable architectural requirements; agents that lack any one of these will fail systematically in enterprise deployment |
| IBM and UC Berkeley Diagnose Why Enterprise Agents Fail — IT-Bench + MAST | IBM Research (HuggingFace blog) | 2025 | [↗](https://huggingface.co/blog/ibm-research/itbenchandmast) | Domain isolation must be the primary reliability control; overloaded orchestrators accumulate double the failure modes of focused single-domain agents |
| AssetOpsBench: Bridging the Gap Between AI Agent Benchmarks and Industrial Reality | IBM Research (HuggingFace blog) | 2025 | [↗](https://huggingface.co/blog/ibm-research/assetopsbench-playground-on-hugging-face) | Tool argument quality must be explicitly measured and gated at deployment; the 33pp accuracy gap between architectures is driven by tool quality, not model selection |
| An IBM Guide to Agentic AI Systems (architecture patterns) | IBM Think | 2025 | [↗](https://www.ibm.com/think/architectures/patterns/agentic-ai) | The orchestration topology (orchestrator, reactive, hybrid) must be selected deliberately and documented; organic or unplanned topology choices produce unpredictable outcomes at scale |
| What Is Agentic Architecture? | IBM Think | 2025 | [↗](https://www.ibm.com/think/topics/agentic-architecture) | Agent roles, tools, and memory layers must be treated as distinct architectural concerns with independent design decisions; conflating them in a single agent increases failure risk |
| Model Context Protocol Architecture Patterns for Multi-Agent AI Systems | IBM Developer | 2025 | [↗](https://developer.ibm.com/articles/mcp-architecture-patterns-ai-systems/) | MCP must be used as the tool-integration standard for agent-calling patterns; it must not be conflated with a general communication or chat protocol |
| Beginner's Guide to Multi-Agent Orchestration with watsonx Orchestrate | IBM Developer | 2025 | [↗](https://developer.ibm.com/articles/multi-agent-orchestration-watsonx-orchestrate/) | Hierarchical routing with domain isolation must be the default enterprise multi-agent topology; flat peer-to-peer routing patterns must be avoided |

---

### **Microsoft**

| Title | Author / Source | Date | Link | Position & Nuance |
|---|---|---|---|---|
| AI Agent Orchestration Patterns — Azure Architecture Center | Microsoft Learn | 2025 | [↗](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) | Every agent must have a single responsibility; context must be explicitly managed at every boundary; specialisation and domain isolation are the two properties that deliver reliability at scale |
| AI Agent Adoption Guidance for Organizations — Cloud Adoption Framework | Microsoft CAF | 2025 | [↗](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/) | Governance and security infrastructure must be in place before the agent estate is scaled; agents must not be deployed without prior governance controls |
| Governance and Security for AI Agents across the Organization — CAF | Microsoft CAF | 2025 | [↗](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/governance-security-across-organization) | Security trimming, kill switches, and agent inventory must be first-class architectural requirements built before capability deployment — not retrofitted after an incident |
| Process to Build Agents across Your Organization with Foundry and Copilot Studio | Microsoft CAF | 2025 | [↗](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/build-secure-process) | A factory build process with standard templates and ALM pipelines must be established before scaling begins; ad-hoc per-agent builds cannot scale to 100+ agents |
| Agent Factory: The New Era of Agentic AI — Use Cases and Design Patterns | Azure Blog | 2025 | [↗](https://azure.microsoft.com/en-us/blog/agent-factory-the-new-era-of-agentic-ai-common-use-cases-and-design-patterns/) | Hierarchical multi-agent architecture consistently delivers >60% improvement in enterprise deployments; the pattern is validated by evidence and must not be treated as theoretical |
| Agent Factory: Top 5 Agent Observability Best Practices for Reliable AI | Azure Blog | 2025 | [↗](https://azure.microsoft.com/en-us/blog/agent-factory-top-5-agent-observability-best-practices-for-reliable-ai/) | correlationId, distributed tracing, and automated evaluation in CI/CD are non-negotiable production requirements; observability must be designed in before deployment, not added afterwards |
| Agent Factory: Connecting Agents, Apps, and Data with MCP and A2A | Azure Blog | 2025 | [↗](https://azure.microsoft.com/en-us/blog/agent-factory-connecting-agents-apps-and-data-with-new-open-standards-like-mcp-and-a2a/) | A2A and MCP must be the interoperability layer; the architecture must support platform-agnostic agent connectivity by design — not through proprietary point-to-point integrations |
| Agent Factory: Designing the Open Agentic Web Stack | Azure Blog | 2025 | [↗](https://azure.microsoft.com/en-us/blog/agent-factory-designing-the-open-agentic-web-stack/) | The connectivity layer must be built on open standards; vendor lock-in at the orchestration level must be avoided by design |
| AI Didn't Break Your Production — Your Architecture Did | Microsoft Tech Community | Jan 2026 | [↗](https://techcommunity.microsoft.com/blog/educatordeveloperblog/ai-didn%E2%80%99t-break-your-production-%E2%80%94-your-architecture-did/4482848) | Idempotency, authority scoping, and blast radius caps are architectural requirements; production failures in agentic systems are architecture failures, not model failures |
| AI Agents: Key Principles and Guidelines — Part 3 | Microsoft Tech Community | 2025 | [↗](https://techcommunity.microsoft.com/blog/educatordeveloperblog/ai-agents-key-principles-and-guidelines---part-3/4390677) | Governance and safety must be designed as first-class architectural inputs at inception — they cannot be added as a compliance layer after the agent estate is built |
| AI Agents: The Multi-Agent Design Pattern — Part 8 | Microsoft Tech Community | 2025 | [↗](https://techcommunity.microsoft.com/blog/educatordeveloperblog/ai-agents-the-multi-agent-design-pattern---part-8/4402246) | Multi-agent must be a deliberate choice justified by demonstrated domain decomposition need; single-agent must remain the default when it can reliably deliver the outcome |
| Azure AI: Single Agent or Multi-Agent — How Should I Choose? | Microsoft Tech Community | 2024 | [↗](https://techcommunity.microsoft.com/blog/azure-ai-foundry-blog/the-future-of-ai-single-agent-or-multi-agent---how-should-i-choose/4257104) | Every new agent boundary must be justified by a concrete reliability requirement; coordination overhead must be weighed against specialisation value before adding a boundary |
| Patterns for Building a Scalable Multi-Agent System | Microsoft ISE Dev Blog | 2025 | [↗](https://devblogs.microsoft.com/ise/multi-agent-systems-at-scale/) | Domain isolation, bounded context, and independent ALM pipelines must be designed before the first agent is built; retrofitting them at scale requires re-architecture |
| Designing Multi-Agent Intelligence | Microsoft Dev Blog | 2025 | [↗](https://devblogs.microsoft.com/blog/designing-multi-agent-intelligence) | Every sub-agent boundary must be a deliberate architectural decision with explicit written justification; organic accumulation of agent boundaries is an anti-pattern |
| Microsoft Multi-Agent Reference Architecture | Microsoft GitHub | 2025 | [↗](https://microsoft.github.io/multi-agent-reference-architecture/docs/reference-architecture/Reference-Architecture.html) | Gateway → Domain → Specialist is the validated enterprise topology; flat orchestrator-to-all patterns must not be used at any scale |
| Azure Well-Architected Framework — AI Application Design | Microsoft Learn | 2025 | [↗](https://learn.microsoft.com/en-us/azure/well-architected/ai/application-design) | Prompts must be version-controlled and promoted through ALM pipelines; a prompt change must be subject to the same review, gate, and rollback requirements as a code change |

---

### **Google / DeepMind**

| Title | Author / Source | Date | Link | Position & Nuance |
|---|---|---|---|---|
| Towards a Science of Scaling Agent Systems (arXiv 2512.08296) | Yubin Kim et al. — Google Research / DeepMind / MIT | Dec 2025 | [↗](https://arxiv.org/abs/2512.08296) | Multi-agent must only be adopted when the specialisation value demonstrably exceeds coordination overhead; empirical validation must precede architectural scaling decisions |
| Towards a Science of Scaling Agent Systems — When and Why Agent Systems Work | Google Research Blog | Dec 2025 | [↗](https://research.google/blog/towards-a-science-of-scaling-agent-systems-when-and-why-agent-systems-work/) | Each new agent boundary must be validated to deliver net performance gain; adding agents without measuring the coordination cost is an architectural anti-pattern |
| Developer's Guide to Multi-Agent Patterns in ADK | Google Developers Blog | Dec 2025 | [↗](https://developers.googleblog.com/developers-guide-to-multi-agent-patterns-in-adk/) | Agent patterns must be selected from a defined taxonomy (hierarchical, sequential, parallel); ad-hoc topology design leads to unpredictable reliability |
| Architecting Efficient Context-Aware Multi-Agent Framework for Production | Google Developers Blog | Dec 2025 | [↗](https://developers.googleblog.com/architecting-efficient-context-aware-multi-agent-framework-for-production/) | Production efficiency must be a primary architectural design criterion; context management strategy must be explicitly defined and tested before deployment — not optimised post-launch |
| Agent Development Kit: Making it Easy to Build Multi-Agent Applications | Google Developers Blog | Apr 2025 | [↗](https://developers.googleblog.com/en/agent-development-kit-easy-to-build-multi-agent-applications/) | Multi-agent frameworks must be opinionated on topology; frameworks that leave topology unspecified produce inconsistent and unreliable results at scale |
| Building Collaborative AI: A Developer's Guide to Multi-Agent Systems with ADK | Google Cloud Blog | 2025 | [↗](https://cloud.google.com/blog/topics/developers-practitioners/building-collaborative-ai-a-developers-guide-to-multi-agent-systems-with-adk) | Structured handoff protocols between agents are a reliability requirement; unstructured agent collaboration without defined contracts produces inconsistent outcomes |

---

### **Andrej Karpathy**

| Title | Date | Link | Position & Nuance |
|---|---|---|---|
| Intro to Large Language Models (YouTube lecture) | Nov 2023 | [↗](https://www.youtube.com/watch?v=zjkBMFhNj_g) | Multi-step agent chain reliability must be calculated, not assumed; compound accuracy degrades geometrically — 5 steps at 95% each yields only 77% end-to-end ("march of nines"); back-calculate required per-step accuracy before building |
| Software 2.0 | Nov 2017 | [↗](https://karpathy.medium.com/software-2-0-a64152b37c35) | For any task with known structure, deterministic code must be preferred over LLM reasoning; LLMs earn their place only for tasks that cannot be reduced to a known algorithm |
| Software Is Changing (Again) — Software 3.0 (YC AI Startup School keynote) | Apr 2025 | [↗](https://www.youtube.com/watch?v=LCEmiRjPEtQ) | Engineering effort must be concentrated on the orchestration layer — context construction, topology design, and tool contracts; effort spent on model selection beyond the capability gate delivers diminishing returns |
| State of GPT (Microsoft Build 2023) | May 2023 | [↗](https://www.youtube.com/watch?v=bZQun8Y4L2A) | Agent capability must be validated against the specific task type before deployment; benchmark performance does not predict task-specific reliability due to LLM jagged intelligence profiles |
| LLM OS — Lex Fridman Podcast #333 | Jan 2023 | [↗](https://www.youtube.com/watch?v=cdiD-9MMpb0) | The LLM is the reasoning engine but must not govern its own execution lifecycle; termination, scheduling, and resource limits are orchestration-layer concerns — analogous to OS functions, not CPU self-governance |
| LLM OS — What It Means for AI to Be an Operating System | 2025 | [↗](https://karpathy.ai/blog/2025-llm-os.html) | Prompt injection through tool responses is the primary security threat for agentic systems; every external data source entering the context window must be treated as a potential attack vector |
| Deep Dive into LLMs like ChatGPT (YouTube lecture) | Feb 2025 | [↗](https://www.youtube.com/watch?v=7xTGNNLPyMI) | Model benchmark scores do not predict task-specific reliability; every agent must pass a task-specific capability test before deployment — "seems capable on benchmarks" is not an acceptable justification |
| 2025 LLM Year in Review | Dec 2025 | [↗](https://karpathy.bearblog.dev/year-in-review-2025/) | Agent autonomy must be governed by explicit autonomy sliders per action type, not binary on/off; eval harnesses must be built before agents are deployed, not as a post-deployment retrofit |

---

### **Ilya Sutskever**

| Title | Date | Link | Position & Nuance |
|---|---|---|---|
| Sequence to Sequence Learning with Neural Networks (arXiv 1409.3215) | Sep 2014 | [↗](https://arxiv.org/abs/1409.3215) | Encoder-decoder architecture with attention enables structured input-output contracts across domain boundaries; the principle that typed, bounded representations enable reliable handoffs applies directly to inter-agent I/O design |
| NeurIPS 2024 — Pre-Training as We Know It Will End | Dec 2024 | [↗](https://dlyog.com/papers/one_internet_v1) | Benchmark performance must not be used as a proxy for deployment reliability; LLMs have a jagged capability profile — strong on benchmarks, unreliable in specific deployment contexts; reliability gates must test the actual task, not a proxy |
| Sequence to Sequence Learning: What a Decade (NeurIPS 2024 keynote video) | Dec 2024 | [↗](https://www.youtube.com/watch?v=1yvBqasHLZs) | Alignment verification cannot rely solely on curated test sets; adversarial inputs that probe the boundary between intended and unintended behaviour are required to characterise alignment |
| Weak-to-Strong Generalization — OpenAI / ICML 2024 | Jun 2024 | [↗](https://openai.com/index/weak-to-strong-generalization/) | A model cannot reliably supervise itself; external verification must be epistemically independent of the generator — self-check is structurally invalid as a safety control |
| Moving from the Age of Scaling to the Age of Research — Dwarkesh Patel interview | Nov 2025 | [↗](https://www.dwarkesh.com/p/ilya-sutskever-2) | Maximum reasoning scope must be bounded as an explicit architectural parameter per agent role; unpredictability scales with reasoning depth and must be constrained in production |
| Ilya Sutskever: The More AI Reasons, The More Unpredictable It Becomes | Dec 2024 | [↗](https://www.technology.org/2024/12/16/ilya-sutskever-the-more-ai-reasons-the-more-unpredictable-it-becomes/) | Reasoning depth must be a defined architectural constraint per agent role; open-ended reasoning scope in production agents produces outputs that are progressively harder to validate as chain length grows |
| Safe Superintelligence Inc. — Founding Principles | 2023 | [↗](https://ssi.inc/) | Safety infrastructure must be in production before autonomous capabilities are enabled; safety always leads capability deployment — it must never trail it |

---

### **Sebastian Raschka**

*Entries applicable to SaaS/PaaS agent deployment architecture — model selection, evaluation methodology, and RAG design:*

| Title | Date | Link | Position & Nuance |
|---|---|---|---|
| State of LLMs 2025 | Dec 2025 | [↗](https://magazine.sebastianraschka.com/p/state-of-llms-2025) | Model architecture must be validated for multi-turn reasoning before approval for agentic roles; single-turn benchmark scores do not predict multi-turn agentic performance |
| Understanding Reasoning LLMs | Feb 2025 | [↗](https://magazine.sebastianraschka.com/p/understanding-reasoning-llms) | Reasoning models require evaluation methodology that tests boundary conditions and output distribution stability — not just median pass rate on a golden set |
| The State of Reinforcement Learning for LLM Reasoning | 2025 | [↗](https://magazine.sebastianraschka.com/p/the-state-of-llm-reasoning-model-training) | RL-trained reasoning models must be evaluated differently from instruction-tuned models; standard benchmarks do not capture their failure distribution or the conditions under which reasoning diverges |
| Understanding the 4 Main Approaches to LLM Evaluation | 2024 | [↗](https://magazine.sebastianraschka.com/p/llm-evaluation-4-approaches) | LLM-as-Judge must be calibrated against human annotations to a measurable agreement threshold before it can be used as an automated evaluation gate; uncalibrated LLM judges produce false confidence |
| The Big LLM Architecture Comparison | 2025 | [↗](https://magazine.sebastianraschka.com/p/the-big-llm-architecture-comparison) | Model capability characteristics must be validated for agentic task types before selection; architecture differences affect multi-turn coherence and tool-call reliability in ways benchmark scores do not capture |
| Beyond Standard LLMs: Architecture Variants and Design Decisions | 2024 | [↗](https://magazine.sebastianraschka.com/p/beyond-standard-llms) | RAG retrieval quality must be evaluated independently from generation quality; improving generation without measuring retrieval is misallocated engineering effort |
| Finetuning Large Language Models | 2024 | [↗](https://magazine.sebastianraschka.com/p/finetuning-large-language-models) | Fine-tuning is justified only when prompting and RAG demonstrably cannot reach the required reliability threshold; fine-tuning without this gate creates maintenance burden without proportional reliability gains |
| Using and Finetuning Pretrained Transformers | 2023 | [↗](https://magazine.sebastianraschka.com/p/using-and-finetuning-pretrained-transformers) | In-context learning must be attempted and measured before fine-tuning is proposed; fine-tuning decisions must be evidence-based, not driven by preference for model ownership or team capability |
| LLM Research Papers: The 2024 List | Jan 2025 | [↗](https://magazine.sebastianraschka.com/p/llm-research-papers-the-2024-list) | Evaluation methodology must be reviewed and updated on a defined cadence; an eval harness designed in Year 1 may be inadequate by Year 2 as failure modes and best practices evolve |

#### 📖 Foundation Research — Model Training & Architecture Internals (Not Applicable to SaaS/PaaS Deployment)

> ⚠️ The entries below cover model architecture internals and ground-up fine-tuning configuration. They are not applicable to this architecture, which uses managed models from the Azure AI Foundry catalog and Copilot Studio — no model training pipeline exists in scope.

| Title | Date | Link | Why Excluded |
|---|---|---|---|
| Build a Large Language Model From Scratch (Manning, 2024) | 2024 | [↗](https://sebastianraschka.com/llms-from-scratch/) | Covers model architecture internals for model builders; not applicable when using managed catalog models |
| Practical Tips for Finetuning LLMs Using LoRA | 2024 | [↗](https://magazine.sebastianraschka.com/p/practical-tips-for-finetuning-llms) | LoRA rank selection and revalidation configuration; relevant only when operating a fine-tuning pipeline |

---
