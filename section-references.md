---
layout: default
title: "📚 Architecture References"
parent: Enterprise Multi-Agent Architecture
nav_order: 20
---

## 📚 Architecture References

A curated repository of architecture-specific articles, papers, and guidance on multi-agent systems. Only sources that provide architectural principles, design patterns, or structural guidance are included — not general AI/LLM introductions.

---

### **HuggingFace**

| Title | Author / Source | Date | Link | Position & Nuance |
|---|---|---|---|---|
| Design Patterns for Building Agentic Workflows | Diego Carpintero | 2025 | [↗](https://huggingface.co/blog/dcarpintero/design-patterns-for-building-agentic-workflows) | Pro-orchestrator; advocates structured agent topologies over mesh; strong emphasis on context hygiene and tool argument quality as the primary failure surface |
| The Agentic Shift — Design Patterns for Building AI Systems | Diego Carpintero | 2025 | [↗](https://huggingface.co/blog/dcarpintero/design-patterns-for-building-ai-systems) | Treats agentic AI as a paradigm shift in software architecture, not just a prompt engineering exercise; orchestration layer is the engineering surface |
| Building Good Agents (smolagents docs) | HuggingFace | 2025 | [↗](https://huggingface.co/docs/smolagents/tutorials/building_good_agents) | Pragmatic and opinionated: minimize LLM calls, consolidate tools, prefer code actions over JSON; explicitly against unnecessary agent complexity |
| Introducing smolagents: simple agents that write actions in code | HuggingFace | Jan 2025 | [↗](https://huggingface.co/blog/smolagents) | Code-first agent actions (Python vs. JSON tool calls); favours minimal agent frameworks; simplicity as the primary design principle |
| What Are Agents? (smolagents conceptual guide) | HuggingFace | 2025 | [↗](https://huggingface.co/docs/smolagents/conceptual_guides/intro_agents) | Foundational framing: agents as programs where the LLM drives the control flow; distinguishes tool-calling agents from code agents |
| IBM and UC Berkeley Diagnose Why Enterprise Agents Fail — IT-Bench + MAST | IBM Research | Feb 2025 | [↗](https://huggingface.co/blog/ibm-research/itbenchandmast) | Empirical failure taxonomy; focused vs. overloaded systems accumulate 2.6 vs. 5.3 failure modes per failed trace; contextual loss and ambiguity are the top root causes |
| AssetOpsBench: Bridging the Gap Between AI Agent Benchmarks and Industrial Reality | IBM Research | 2025 | [↗](https://huggingface.co/blog/ibm-research/assetopsbench-playground-on-hugging-face) | Industrial reality benchmark; 33pp tool accuracy gap between top and low performers; tool argument quality (not model capability) is the primary performance determinant |
| Neuro SAN Is All You Need — A Data-Driven Multi-Agent Orchestration Framework | Cognizant AI Lab | 2025 | [↗](https://huggingface.co/blog/danyoung/neuro-san-is-all-you-need) | Decentralised routing; agents as nodes in a network; side-channel credential pattern; explicitly opposes star topology single-orchestrator designs |
| OpenEnv in Practice: Evaluating Tool-Using Agents in Real-World Environments | OpenEnv / Turing | 2025 | [↗](https://huggingface.co/blog/openenv-turing) | Tool evaluation in real environments; 50pp accuracy drop with ambiguous vs. explicit inputs; ambiguity resolution before action is a hard architectural requirement |
| WorkflowDrivenAgent: A Novel Paradigm for Deterministic Multi-Agent AI Systems | Dariel Noel | 2025 | [↗](https://huggingface.co/blog/darielnoel/workflow-driven-agent) | Advocates deterministic workflow-driven agents over autonomous reasoning agents; flows over crews as the default; autonomous planning reserved for genuinely open-ended tasks |
| Building Blocks of Agentic Systems | Kseniase | 2024 | [↗](https://huggingface.co/blog/Kseniase/buildingblocks) | Neutral taxonomy of memory, tools, and planning components; survey framing rather than prescriptive; useful for classifying agent design decisions |

---

### **DeepLearning.AI**

| Title | Author / Source | Date | Link | Position & Nuance |
|---|---|---|---|---|
| Four AI Agent Strategies That Improve GPT-4 and GPT-3.5 Performance | Andrew Ng | Mar 2024 | [↗](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/) | Structured agentic patterns (reflection, tool use, planning, multi-agent) consistently outperform single-prompt approaches; pattern architecture beats brute-force model upgrades |
| Agentic Design Patterns Part 2: Reflection | Andrew Ng | Mar 2024 | [↗](https://www.deeplearning.ai/the-batch/agentic-design-patterns-part-2-reflection/) | Iterative self-review improves output quality; most valuable for code and writing tasks; adds latency — use only where quality benefit justifies cost |
| Agentic Design Patterns Part 3: Tool Use | Andrew Ng | Apr 2024 | [↗](https://www.deeplearning.ai/the-batch/agentic-design-patterns-part-3-tool-use/) | Tool use is the most reliable and deployable agentic pattern; best ROI among the four; tool quality and argument clarity are the bottleneck |
| Agentic Design Patterns Part 4: Planning | Andrew Ng | Apr 2024 | [↗](https://www.deeplearning.ai/the-batch/agentic-design-patterns-part-4-planning/) | LLM-driven planning enables complex multi-step tasks; explicit caution on reliability — not suitable for deterministic workflows where sequencing must be guaranteed |
| Agentic Design Patterns Part 5: Multi-Agent Collaboration | Andrew Ng | Apr 2024 | [↗](https://www.deeplearning.ai/the-batch/agentic-design-patterns-part-5-multi-agent-collaboration/) | Multiple specialised agents outperform a single generalist; domain decomposition is the key design principle; ablation studies confirm hierarchical multi-agent advantage |
| LLMs Evolve with Agentic Workflows, Enabling Autonomous Reasoning and Collaboration | Andrew Ng | 2024 | [↗](https://www.deeplearning.ai/the-batch/llms-evolve-with-agentic-workflows-enabling-autonomous-reasoning-and-collaboration/) | Agentic workflows enable qualitatively different task completion; human-in-the-loop is essential during early deployment; autonomy should be earned through demonstrated reliability |

---

### **IBM Research**

| Title | Author / Source | Date | Link | Position & Nuance |
|---|---|---|---|---|
| Why Do Multi-Agent LLM Systems Fail? (MAST — arXiv 2503.13657) | Cemri, Pan, Yang et al. (IBM / UC Berkeley) | Mar 2025 | [↗](https://arxiv.org/abs/2503.13657) | Empirical taxonomy of 14 failure modes; FM-2.2 (ambiguity), FM-3.3 (self-verification), FM-1.5/3.1 (termination) are the most critical; overloaded orchestrators are the dominant failure class |
| IBM and UC Berkeley Diagnose Why Enterprise Agents Fail — IT-Bench + MAST | IBM Research (HuggingFace blog) | 2025 | [↗](https://huggingface.co/blog/ibm-research/itbenchandmast) | Accessible summary of MAST findings; enterprise context; 2.6 vs. 5.3 failure modes per trace distinguishes focused from overloaded systems |
| AssetOpsBench: Bridging the Gap Between AI Agent Benchmarks and Industrial Reality | IBM Research (HuggingFace blog) | 2025 | [↗](https://huggingface.co/blog/ibm-research/assetopsbench-playground-on-hugging-face) | Industrial benchmark; 94% vs. 61% tool accuracy range; narrowing the gap requires better argument quality, not better models |
| An IBM Guide to Agentic AI Systems (architecture patterns) | IBM Think | 2025 | [↗](https://www.ibm.com/think/architectures/patterns/agentic-ai) | Architecture patterns catalog; IBM enterprise perspective; broad taxonomy including orchestrator, reactive, and hybrid patterns |
| What Is Agentic Architecture? | IBM Think | 2025 | [↗](https://www.ibm.com/think/topics/agentic-architecture) | Definitional; IBM's taxonomy of agentic system components; clear separation between agent roles, tools, and memory layers |
| Model Context Protocol Architecture Patterns for Multi-Agent AI Systems | IBM Developer | 2025 | [↗](https://developer.ibm.com/articles/mcp-architecture-patterns-ai-systems/) | Protocol-level guidance; MCP as the integration standard for tool-calling agents; positions MCP as the connector layer, not just a chat protocol |
| Beginner's Guide to Multi-Agent Orchestration with watsonx Orchestrate | IBM Developer | 2025 | [↗](https://developer.ibm.com/articles/multi-agent-orchestration-watsonx-orchestrate/) | watsonx-specific but orchestration topology patterns are generalizable; hierarchical routing and domain isolation as the default enterprise approach |

---

### **Microsoft**

| Title | Author / Source | Date | Link | Position & Nuance |
|---|---|---|---|---|
| AI Agent Orchestration Patterns — Azure Architecture Center | Microsoft Learn | 2025 | [↗](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) | Canonical Microsoft reference; single responsibility, context hygiene, specialisation, and scalability as the four core architectural properties; star topology as an explicit anti-pattern |
| AI Agent Adoption Guidance for Organizations — Cloud Adoption Framework | Microsoft CAF | 2025 | [↗](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/) | Governance-first enterprise adoption guidance; manage security and compliance infrastructure before scaling agent estate |
| Governance and Security for AI Agents across the Organization — CAF | Microsoft CAF | 2025 | [↗](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/governance-security-across-organization) | Security trimming, kill switch, and agent inventory as first-class architectural requirements; safety infrastructure must lead capability deployment |
| Process to Build Agents across Your Organization with Foundry and Copilot Studio | Microsoft CAF | 2025 | [↗](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/build-secure-process) | Factory build process; Copilot Studio vs. Foundry selection criteria; template-first approach to agent estate scaling |
| Agent Factory: The New Era of Agentic AI — Use Cases and Design Patterns | Azure Blog | 2025 | [↗](https://azure.microsoft.com/en-us/blog/agent-factory-the-new-era-of-agentic-ai-common-use-cases-and-design-patterns/) | Evidence-based outcomes: Fujitsu 67%, ContraForce 80%, JM Family 60% improvement; hierarchical architecture as the validated production pattern |
| Agent Factory: Top 5 Agent Observability Best Practices for Reliable AI | Azure Blog | 2025 | [↗](https://azure.microsoft.com/en-us/blog/agent-factory-top-5-agent-observability-best-practices-for-reliable-ai/) | correlationId, distributed tracing, and continuous evaluation are non-negotiable; eval gates in CI/CD are treated as equivalent to code quality gates |
| Agent Factory: Connecting Agents, Apps, and Data with MCP and A2A | Azure Blog | 2025 | [↗](https://azure.microsoft.com/en-us/blog/agent-factory-connecting-agents-apps-and-data-with-new-open-standards-like-mcp-and-a2a/) | Open standards position; A2A and MCP as the interoperability layer; platform-agnostic connectivity by design |
| Agent Factory: Designing the Open Agentic Web Stack | Azure Blog | 2025 | [↗](https://azure.microsoft.com/en-us/blog/agent-factory-designing-the-open-agentic-web-stack/) | Long-term architecture vision; open web stack for agent-to-agent collaboration; positions Microsoft as infrastructure rather than walled garden |
| AI Didn't Break Your Production — Your Architecture Did | Microsoft Tech Community | Jan 2026 | [↗](https://techcommunity.microsoft.com/blog/educatordeveloperblog/ai-didn%E2%80%99t-break-your-production-%E2%80%94-your-architecture-did/4482848) | Production incident analysis; idempotency, authority scoping, and blast radius caps are architectural requirements not implementation details; failures traced to architecture not model behaviour |
| AI Agents: Key Principles and Guidelines — Part 3 | Microsoft Tech Community | 2025 | [↗](https://techcommunity.microsoft.com/blog/educatordeveloperblog/ai-agents-key-principles-and-guidelines---part-3/4390677) | Microsoft's principled guidance on agent design; governance and safety as first-class design inputs |
| AI Agents: The Multi-Agent Design Pattern — Part 8 | Microsoft Tech Community | 2025 | [↗](https://techcommunity.microsoft.com/blog/educatordeveloperblog/ai-agents-the-multi-agent-design-pattern---part-8/4402246) | Multi-agent pattern specifics; when single-agent is sufficient vs. when domain decomposition is required |
| Azure AI: Single Agent or Multi-Agent — How Should I Choose? | Microsoft Tech Community | 2024 | [↗](https://techcommunity.microsoft.com/blog/azure-ai-foundry-blog/the-future-of-ai-single-agent-or-multi-agent---how-should-i-choose/4257104) | Decision framework for single vs. multi-agent; complexity vs. reliability trade-off; multi-agent as a deliberate choice not a default |
| Patterns for Building a Scalable Multi-Agent System | Microsoft ISE Dev Blog | 2025 | [↗](https://devblogs.microsoft.com/ise/multi-agent-systems-at-scale/) | Engineering-focused; production scaling patterns; domain isolation, bounded context, and ALM pipeline design at 100+ agent scale |
| Designing Multi-Agent Intelligence | Microsoft Dev Blog | 2025 | [↗](https://devblogs.microsoft.com/blog/designing-multi-agent-intelligence) | Agent hierarchy design; orchestrator-worker pattern as the recommended topology; sub-agent boundaries as deliberate architectural decisions |
| Microsoft Multi-Agent Reference Architecture | Microsoft GitHub | 2025 | [↗](https://microsoft.github.io/multi-agent-reference-architecture/docs/reference-architecture/Reference-Architecture.html) | Reference implementation; topology guidance for enterprise deployments; prescriptive on Gateway → Domain → Specialist hierarchy |
| Azure Well-Architected Framework — AI Application Design | Microsoft Learn | 2025 | [↗](https://learn.microsoft.com/en-us/azure/well-architected/ai/application-design) | Quality pillars applied to AI workloads; prompt versioning and externalisation as code-equivalent governance requirements |

---

### **Google / DeepMind**

| Title | Author / Source | Date | Link | Position & Nuance |
|---|---|---|---|---|
| Towards a Science of Scaling Agent Systems (arXiv 2512.08296) | Yubin Kim et al. — Google Research / DeepMind / MIT | Dec 2025 | [↗](https://arxiv.org/abs/2512.08296) | Scientific analysis of when and why agent systems work; scaling laws for agents; empirical conditions under which adding agents improves vs. degrades performance |
| Towards a Science of Scaling Agent Systems — When and Why Agent Systems Work | Google Research Blog | Dec 2025 | [↗](https://research.google/blog/towards-a-science-of-scaling-agent-systems-when-and-why-agent-systems-work/) | Accessible summary of arXiv paper; key finding: specialisation value must outweigh coordination overhead for multi-agent to be beneficial |
| Developer's Guide to Multi-Agent Patterns in ADK | Google Developers Blog | Dec 2025 | [↗](https://developers.googleblog.com/developers-guide-to-multi-agent-patterns-in-adk/) | ADK-specific but patterns are generalizable; hierarchical, sequential, and parallel patterns with practical implementation guidance |
| Architecting Efficient Context-Aware Multi-Agent Framework for Production | Google Developers Blog | Dec 2025 | [↗](https://developers.googleblog.com/architecting-efficient-context-aware-multi-agent-framework-for-production/) | Efficiency-first production framing; context management at scale; argues that architecture efficiency is more important than model capability for production reliability |
| Agent Development Kit: Making it Easy to Build Multi-Agent Applications | Google Developers Blog | Apr 2025 | [↗](https://developers.googleblog.com/en/agent-development-kit-easy-to-build-multi-agent-applications/) | ADK introduction; Google's multi-agent framework; opinionated toward hierarchical patterns and explicit inter-agent contracts |
| Building Collaborative AI: A Developer's Guide to Multi-Agent Systems with ADK | Google Cloud Blog | 2025 | [↗](https://cloud.google.com/blog/topics/developers-practitioners/building-collaborative-ai-a-developers-guide-to-multi-agent-systems-with-adk) | Multi-agent collaboration patterns from a developer perspective; structured handoff protocols between agents as a reliability requirement |

---

### **Andrej Karpathy**

| Title | Date | Link | Position & Nuance |
|---|---|---|---|
| Software Is Changing (Again) — Software 3.0 (YC AI Startup School keynote) | Apr 2025 | [↗](https://www.youtube.com/watch?v=LCEmiRjPEtQ) | LLMs as a new programming paradigm; prompts as programs; orchestration layer is where engineering effort should concentrate; evals-first development as the defining discipline of Software 3.0 |
| LLM OS — What It Means for AI to Be an Operating System | 2025 | [↗](https://karpathy.ai/blog/2025-llm-os.html) | LLMs as operating system kernels; context window as RAM; tools as peripherals; prompt injection as the primary security surface — equivalent to OS-level exploits |
| Deep Dive into LLMs like ChatGPT (YouTube lecture) | Feb 2025 | [↗](https://www.youtube.com/watch?v=7xTGNNLPyMI) | Technical deep dive; jagged intelligence (uneven capability profiles — benchmark ≠ task reliability); test-time compute; context engineering as the primary performance lever |
| 2025 LLM Year in Review | Dec 2025 | [↗](https://karpathy.bearblog.dev/year-in-review-2025/) | Annual assessment; autonomy sliders over binary on/off for agentic control; evals-first is the defining practitioner discipline; caution on over-automating before reliability is demonstrated |

---

### **Ilya Sutskever**

| Title | Date | Link | Position & Nuance |
|---|---|---|---|
| Sequence to Sequence Learning: What a Decade (NeurIPS 2024 keynote) | Dec 2024 | [↗](https://www.youtube.com/watch?v=1yvBqasHLZs) | Reflection on a decade of scaling; weak-to-strong generalisation thesis; moving from pattern matching to genuine reasoning; alignment verification cannot rely solely on curated test sets |
| Moving from the Age of Scaling to the Age of Research — Dwarkesh Patel interview | Nov 2025 | [↗](https://www.dwarkeshpatel.com/p/ilya-sutskever) | Scaling laws not ended but low-hanging fruit is gone; next capability gains come from research not compute; unpredictability scales with reasoning depth — constrain reasoning scope in production |

---

### **Sebastian Raschka**

| Title | Date | Link | Position & Nuance |
|---|---|---|---|
| State of LLMs 2025 | Dec 2025 | [↗](https://magazine.sebastianraschka.com/p/state-of-llms-2025) | Annual practitioner review; linear attention architectures fail on multi-turn reasoning tasks; model selection must include architecture-level validation for agentic roles |
| Understanding Reasoning LLMs | Feb 2025 | [↗](https://magazine.sebastianraschka.com/p/understanding-reasoning-llms) | Chain-of-thought and extended thinking; reasoning models require different evaluation methodology; boundary conditions for when reasoning helps vs. when it introduces unpredictability |
| Understanding the 4 Main Approaches to LLM Evaluation | 2024 | [↗](https://magazine.sebastianraschka.com/p/understanding-the-4-main-approaches) | LLM-as-Judge bias documented: positional, verbosity, and self-enhancement biases require calibration against human annotations before use as an automated gate |
| The Big LLM Architecture Comparison | 2025 | [↗](https://magazine.sebastianraschka.com/p/the-big-llm-architecture-comparison) | Architecture-level comparison across major model families; MQA vs. MHA vs. GQA serving efficiency implications; SwiGLU variants; hardware-aware design as a first-class concern |
| Beyond Standard LLMs: Architecture Variants and Design Decisions | 2024 | [↗](https://magazine.sebastianraschka.com/p/beyond-standard-llms) | RAG vs. fine-tuning vs. prompting decision tree; retrieval quality is the primary failure surface for RAG agents — evaluate retrieval independently from generation |
| Noteworthy LLM Research Papers of 2024 | Jan 2025 | [↗](https://magazine.sebastianraschka.com/p/noteworthy-llm-research-papers-of) | Curated practitioner review of research; identifies papers most relevant to production deployment; evaluation methodology evolution as a recurring theme |

---

### **Noam Shazeer**

| Title | Date | Link | Position & Nuance |
|---|---|---|---|
| Fast Transformer Decoding: One Write-Head is All You Need — Multi-Query Attention (arXiv 1911.02150) | Nov 2019 | [↗](https://arxiv.org/abs/1911.02150) | MQA reduces KV cache by 10–100x with minimal quality loss; enables larger context windows at the same memory budget; serving efficiency as a primary design motivation, not an afterthought |
| Outrageously Large Neural Networks: The Sparsely-Gated Mixture-of-Experts Layer (arXiv 1701.06538) | Jan 2017 | [↗](https://arxiv.org/abs/1701.06538) | Conditional compute: only activate parameters relevant to each input; scale model capacity without proportional serving cost; lightweight gating network routes inputs — analogous to agent routing |
| GLU Variants Improve Transformers — SwiGLU (arXiv 2002.05202) | Feb 2020 | [↗](https://arxiv.org/abs/2002.05202) | Activation function improvement; SwiGLU outperforms ReLU/GELU in transformers; hardware-aware design — arithmetic efficiency drives architecture choices as much as quality benchmarks |

---
