# Academic State of the Art: Memory Architectures for LLM Agents (Jan 2025 – Nov 2026)

> **Method caveat (read first):** arxiv.org could not be reached from this research session (DNS blocked), so I could not open any arXiv abstract or PDF directly. Paper details below come from search-engine summaries of arXiv pages, Hugging Face/alphaXiv/DAIR summaries, and one primary source fetched in full (Anthropic's Nov 2026 essay). Treat specific numbers as **unverified against the PDFs** unless marked otherwise. Almost everything listed is an **arXiv preprint**. Peer-review status is noted only where a source stated it. Items marked "(background)" are 2024–2025 foundational work cited from prior knowledge. Their IDs were not re-checked this session.

## 1. Major taxonomies and surveys (2025–2026)

### Takeaway
By 2026 the field has converged on a **forms × functions × dynamics** taxonomy. **Forms** are token-level, parametric and latent. **Functions** are factual, experiential and working memory. **Dynamics** cover formation, evolution and retrieval. The older episodic/semantic/procedural cognitive labels remain in use as a second vocabulary. Late-2026 surveys add two framings: memory as **execution/task state** for long-horizon agents, and memory as **governed state** with provenance, access control and forgetting.

### Cited Findings
- **"Memory in the Age of AI Agents: A Survey — Forms, Functions and Dynamics"** (arXiv:2512.13564; v2 dated 13 Jan 2026; authors Hu, Liu, Yue, Zhang et al.):
  - It organizes the field by *forms* (token, parametric, latent), *functions* (factual, experiential, working) and *dynamics* (formation, evolution, retrieval).
  - It explicitly separates agent memory from RAG and from context engineering, and argues that the terminology is fragmented and conflated.
  - The authors maintain the paper list at GitHub Shichun-Liu/Agent-Memory-Paper-List.
  - Sources: [arXiv html](https://arxiv.org/html/2512.13564v2); [paper list](https://github.com/Shichun-Liu/Agent-Memory-Paper-List); [summary](https://papers.lunadong.com/paper/4617)
- **"From Human Memory to AI Memory: A Survey on Memory Mechanisms in the Era of LLMs"** (arXiv:2504.15965, Apr 2025) is an earlier 2025 survey that maps human memory categories onto LLM memory. — [arXiv](https://arxiv.org/pdf/2504.15965)
- **"A Survey of Agent Memory in the Second Half: Towards Self-Evolving and Long-Horizon Agents"** (arXiv:2602.06052, Feb 2026). It reports that self-generated skills still underperform human-curated ones. — [arXiv](https://arxiv.org/pdf/2602.06052)
- **"Anatomy of agentic memory: Taxonomy and empirical analysis"** (arXiv:2602.19320) was seen only as a citation. Its details are unverified. — via [search on 2512.13564 citations](https://arxiv.org/html/2512.13564v2)
- **"From Storage to Experience: A Survey on the Evolution of LLM Agent Memory Mechanisms"** (arXiv:2605.06716, May 2026). It frames the trajectory as moving from storage toward experience-driven memory. — [arXiv](https://arxiv.org/pdf/2605.06716)
- **"Always-On Agents: A Survey of Persistent Memory, State, and Governance in LLM Agents"** (arXiv:2606.30306, Jun 2026):
  - It treats memory as persistent state that needs governance.
  - It discusses parametric memory designs: trainable memory layers, plug-and-play modules, memory states updated at test time, LoRA-style side memories, and weight editing.
  - It notes that a fact cannot be deleted from weights the way a row can be dropped from a store.
  - Source: [arXiv](https://arxiv.org/pdf/2606.30306)
- **"The Horizon Gap: Planning, Memory, Execution, Training, and Evaluation for Long-Horizon LLM Agents"** (arXiv:2608.06663, Aug 2026):
  - It systematically reviews 1,547 arXiv papers from 2024–2026, organized by the task lifecycle: planning, memory, execution, training, evaluation and safety.
  - It separates *long-horizon tasks* from *long-context models* and *long-term memory*.
  - Source: [arXiv abs](https://arxiv.org/abs/2608.06663)
- **"Learning to Remember: Advances in Memory Mechanisms for LLMs"** (arXiv:2610.35089, Oct 2026) groups the experience-to-workflow systems: Agent Workflow Memory, MemSkill, ReasoningBank and Memento-Skills. — [arXiv](https://arxiv.org/pdf/2610.35089)
- **"The Missing Knowledge Layer in Cognitive Architectures for AI Agents"** (arXiv:2604.11364) and **"Memory-Centric Continual Learning for AI Agents"** (arXiv:2610.29357) are further 2026 position/survey papers. They were seen only as search hits. — [2604.11364](https://arxiv.org/pdf/2604.11364); [2610.29357](https://arxiv.org/pdf/2610.29357)
- **Industry taxonomy, Anthropic, "The components of agent memory"** (Lance Martin, 6 Nov 2026; fetched in full):
  - It describes a four-layer hierarchy: (1) transcripts, a searchable event log; (2) memory notes written by the model; (3) memory representations such as linked wiki pages; (4) injected context loaded every session.
  - Offline "dreaming" connects the layers.
  - Source: [Anthropic](https://www.anthropic.com/research/components-of-agent-memory)

### Inferences
- FocalPoint's structure lines up with the dominant 2026 taxonomy:
  - Task nodes with status and narratives correspond to **working + experiential memory**.
  - Knowledge docs correspond to **factual/semantic memory**.
  - L0/L1/L2 assembly corresponds to the **"injected context" top layer** and to context engineering.
- Being purely token-level (no parametric or latent memory) is the mainstream choice for deployed systems and is not a gap.
- The 2512.13564 survey's explicit split between "agent memory" and "context engineering" means FocalPoint straddles two categories. Its attention layer is arguably context engineering, not memory.

### Gaps
- I could not verify the full category trees or author lists of the 2026 surveys beyond their snippets, because arXiv was blocked.

---

## 2. Most active architectural directions (2026 vs 2024–2025 carry-overs)

### Takeaway
- **Carry-overs from 2024–25:** OS-style paging (MemGPT/Letta), agentic note memory (A-MEM), fact extraction (Mem0) and temporal KGs (Zep/Graphiti). These remain the baselines everyone compares against.
- **What is distinctively 2025-late → 2026:**
  - RL-trained memory managers (Memory-R1, Mem-α, MEM1, MemexRL).
  - Offline consolidation, called "sleep-time compute" or "dreaming".
  - Procedural/skill memory with verification gates.
  - Governed multi-agent shared memory.
  - **Memory as execution state** for long-horizon tasks.
  - Prospective memory for proactive agents.
  - Parametric/test-time-learning memory is active but still mostly at research scale.

### Cited Findings

**OS-style / hierarchical (carry-over)**
- MemGPT (arXiv:2310.08560, 2023) introduced virtual-context paging. It became Letta (background). — [arXiv](https://arxiv.org/abs/2310.08560)
- "Hierarchical Graph Memory for LLM Agents with Path-level Localization and Rewrite" (arXiv:2608.05095, Aug 2026) continues the hierarchical line. — [arXiv](https://arxiv.org/pdf/2608.05095)
- "ZenBrain: A Neuroscience-Inspired 7-Layer Memory Architecture" (arXiv:2604.23878). It reports that its sleep-consolidation run **lowered retrieval precision** on real data even though retention rose, which is an honest negative result. — [arXiv](https://arxiv.org/pdf/2604.23878)

**Agentic / Zettelkasten memory and fact stores (carry-over)**
- A-MEM organizes memories as interconnected notes in the style of Zettelkasten (arXiv:2502.12110, Feb 2025, background). It is now a standard baseline. — [described in 2605.25869](https://arxiv.org/pdf/2605.25869)
- Mem0 (arXiv:2504.19413, background):
  - It extracts salient facts and uses an LLM to ADD/UPDATE/DELETE/NOOP them against vector-embedded records.
  - Mem0g adds an entity graph.
  - Source: [described in 2605.07692](https://arxiv.org/pdf/2605.07692); [2608.12365](https://arxiv.org/pdf/2608.12365)

**Graph / temporal KG**
- Zep/Graphiti (arXiv:2501.13956, background) is a temporal KG that stores validity periods for facts and relations. — [2605.07692](https://arxiv.org/pdf/2605.07692); [Atlan](https://atlan.com/know/zep-vs-mem0/)
- **LoCoMo numbers are disputed between vendors**, so they are not comparable across papers:
  - Zep claimed 84%.
  - Mem0 said the correct figure was 58.44%.
  - Zep countered with 75.14%.
  - Source: [Atlan](https://atlan.com/know/zep-vs-mem0/)
- Many 2026 graph-memory papers each claim state of the art on LoCoMo with differing metrics (LLM-judge vs evidence recall):
  - Cognis (arXiv:2604.19771), FluctlightDB (2608.12365), RippleMem (2608.13334), and RL-guided temporal localization (2610.23598).
  - Sources: [2604.19771](https://arxiv.org/pdf/2604.19771); [2608.12365](https://arxiv.org/pdf/2608.12365); [2608.13334](https://arxiv.org/pdf/2608.13334); [2610.23598](https://arxiv.org/pdf/2610.23598)
- "Mitigating Provenance-Role Collapse in Long-Term Agents via Typed Memory Representation" (arXiv:2605.25869) argues for **typed** memory records. — [arXiv](https://arxiv.org/pdf/2605.25869)

**RL-learned memory management (new in late 2025, booming in 2026)**
- **Memory-R1** (arXiv:2508.19828) uses two RL-trained components:
  - A Memory Manager that learns ADD/UPDATE/DELETE operations.
  - An Answer Agent that distills the retrieved memories.
  - The two are trained in separate stages.
  - Source: [arXiv](https://arxiv.org/html/2508.19828v5)
- **Mem-α** (arXiv:2509.25911) learns memory construction through RL:
  - It uses core, episodic and semantic memory components.
  - The reward is QA correctness.
  - It reportedly generalizes to much longer interactions than it was trained on.
  - Sources: [arXiv](https://arxiv.org/pdf/2509.25911); [code](https://github.com/wangyu-ustc/Mem-alpha)
- **MEM1** (arXiv:2506.15841, background ID) uses RL to keep a compact internal state for multi-turn retrieval and reasoning. It is described as optimizing the retrieval side. — [described in 2603.12631](https://arxiv.org/html/2603.12631)
- **MemexRL: "Scaling Long-Horizon LLM Agents via Indexed Experience Memory"** (arXiv:2603.04257) — [arXiv](https://arxiv.org/pdf/2603.04257)
- "Joint Optimization of Multi-agent Memory System" (arXiv:2603.12631) — [arXiv](https://arxiv.org/html/2603.12631)

**Sleep-time / offline consolidation (2025 origin, mainstreamed 2026)**
- Sleep-time compute (Letta + UC Berkeley, arXiv:2504.13171) uses idle time to reorganize context. It reportedly needs about 5× less test-time compute for equal accuracy on modified reasoning benchmarks. This is a single-paper result. — [secondary summary](https://davidamitchell.github.io/Research/research/2026-07-20-agent-memory-consolidation-episodic-semantic.html); cited in [Anthropic](https://www.anthropic.com/research/components-of-agent-memory)
- Anthropic "dreaming" (Nov 2026) reviews logs and notes offline to fix errors, build wikis and choose what gets preloaded. Reported results:
  - On a roughly 420M-token corpus, wiki-only accuracy rose from 13% to 48% over 128 dreaming sessions.
  - Wiki + search reached 61%, versus 53% for search alone, and saved about 20k tokens per question.
  - On CL-Bench, Opus 5.5 improved from 0.655 (stateless) to 0.838 with memory.
  - These are vendor-reported, not peer-reviewed.
  - Source: [Anthropic](https://www.anthropic.com/research/components-of-agent-memory)
- AutoDreamer learns memory-bank rewriting with downstream task success as the reward. It is described in "The Hitchhiker's Guide to Agentic AI" (arXiv:2606.24937). — [arXiv](https://arxiv.org/pdf/2606.24937)
- "Anticipate and Learn: Unleashing Idle-Time Compute in Proactive Agents" (arXiv:2605.25971) — [arXiv](https://arxiv.org/pdf/2605.25971)

**Procedural / skill memory**
- **ProcMEM** (arXiv:2602.01869, Feb 2026):
  - It formalizes a Skill-MDP.
  - A non-parametric PPO gate verifies skills before they enter memory.
  - Score-based maintenance keeps the store compact.
  - No weight updates are involved.
  - Source: [arXiv](https://arxiv.org/html/2602.01869v1)
- "Managing Procedural Memory in LLM Agents: Control, Adaptation, and Evaluation" (arXiv:2606.23127):
  - It covers SkillsBench, which compares no-skill, curated-skill and self-generated-skill conditions.
  - It reports that **retrieval from large, noisy skill corpora can erase much of the gain**.
  - Source: [arXiv](https://arxiv.org/pdf/2606.23127)

**Multi-agent memory (very active in 2026)**
- Collaborative Memory (arXiv:2505.18279, 2025) is the reference design:
  - Private and shared tiers.
  - Asymmetric, time-varying access control encoded as bipartite graphs.
  - Provenance on memory fragments.
  - Source: [arXiv](https://arxiv.org/pdf/2505.18279)
- Governed Shared Memory (arXiv:2606.24535):
  - It treats shared memory as governed state with scope, provenance and temporal supersession.
  - It has agent-local and team-shared tiers, with a privilege check gating cross-agent propagation.
  - Source: [arXiv](https://arxiv.org/html/2606.24535v1)
- GateMem benchmark (arXiv:2606.18829): "no method simultaneously achieves strong utility, robust access control, and reliable forgetting." — [arXiv](https://arxiv.org/abs/2606.18829)
- MAP-Graph (arXiv:2608.10509) is a provenance-aware typed graph of agents, sources, memories, claims and actions. It separates hard authorization from graded trust. — [arXiv](https://arxiv.org/abs/2608.10509v1)
- Further work in this cluster:
  - AIM (2609.12320).
  - Mesh Memory Protocol (2604.19540).
  - "Multi-Agent Memory from a Computer Architecture Perspective" (2603.10062), which names cache sharing and memory access control as protocol gaps.
  - "Beyond Corrected Memory: Execution Consistency in Multi-Agent Systems" (2610.08101).
  - Sources: [2609.12320](https://arxiv.org/abs/2609.12320); [2604.19540](https://arxiv.org/html/2604.19540v1); [2603.10062](https://arxiv.org/html/2603.10062); [2610.08101](https://arxiv.org/pdf/2610.08101)

**Parametric memory / test-time & continual learning**
- 2026 work includes:
  - "Scaling Self-Evolving Agents via Parametric Memory" (2606.04536).
  - RPMem, a recurrent parametric memory across sessions (2609.23466).
  - "Position: Modular Memory is the Key to Continual Learning Agents" (2603.01761).
  - "Recurrent Harness for Agentic Continual Learning" (2610.12668).
  - AgentOdyssey, which benchmarks test-time continual learning (2606.24893).
  - Sources: [2606.04536](https://arxiv.org/pdf/2606.04536); [2609.23466](https://arxiv.org/pdf/2609.23466); [2603.01761](https://arxiv.org/pdf/2603.01761); [2610.12668](https://arxiv.org/pdf/2610.12668); [2606.24893](https://arxiv.org/pdf/2606.24893)
- One paper reports scaling memory parameters to 128B on an 8B-class backbone. Those layers were trained during pretraining, so **sequential deployment-time writes remain untested**. — via [search summary of 2606.30306 / related](https://arxiv.org/pdf/2606.30306)
- Governance objection: weights cannot be selectively deleted or audited, and weight edits cause interference. — [2606.30306](https://arxiv.org/pdf/2606.30306)

### Inferences
- FocalPoint's deterministic-Spine design (the LLM only issues tool calls) is effectively the **opposite bet from RL-learned memory managers**. The RL line trains the LLM to decide ADD/UPDATE/DELETE. FocalPoint hard-codes state transitions.
  - The 2026 multi-agent governance papers (provenance, supersession, privilege checks) support FocalPoint's approach. Deterministic state owners are what those papers ask for.
  - The RL papers suggest the *content* decisions (what to write, what to summarize) may eventually be learnable. That is a possible future module, not a threat to the Spine.
- FocalPoint has no **offline consolidation** loop. Anthropic and Letta both now treat consolidation as a core memory component, which makes it the clearest architectural gap. Mapped onto FocalPoint, consolidation would mean periodically rewriting narratives and knowledge docs and verifying them against raw logs.
- Children inheriting knowledge docs resembles the hierarchical and typed-memory lines (2605.25869, 2608.05095). I found no paper that uses DAG-inheritance of knowledge specifically.

### Gaps
- I could not confirm the peer-review venues for Memory-R1, Mem-α or MEM1 in this session.
- I found no head-to-head benchmark of parametric memory against long-context prompting for agents.
- The "long-context vs memory" debate has no single decisive 2026 paper in my results. The Memory Tax paper (Section 3) comes closest.

---

## 3. Evidence on simple approaches (files, grep/FTS, structured stores) vs vector/graph

### Takeaway
2026 evidence is **mixed but favorable to simple approaches for agentic use**:
- Grep beats vector retrieval on conversational memory benchmarks when results are delivered inline.
- Many "dedicated memory systems" underperform naive RAG over raw history (Memory Tax).
- Anthropic's own memory stack is files, transcript search and wikis, with no mention of vectors.

Keyword search does degrade on **paraphrased queries and large corpora**, and it can be token-expensive.

### Cited Findings
- **"Is Grep All You Need? How Agent Harnesses Reshape Agentic Search"** (arXiv:2605.15184, May 2026):
  - The test set was 116 LongMemEval questions across several agent harnesses and models.
  - Inline grep beat inline vector retrieval on every harness-model pair.
  - With programmatic, file-based delivery, vector beat grep on 5 of 10 pairs. For example, Codex CLI + GPT-5.4 scored 93.1% with inline grep but only 55.2% with programmatic grep, versus 67.2% for programmatic vector.
  - The conclusion: harness design matters as much as the retrieval method.
  - Sources: [ar5iv](https://ar5iv.labs.arxiv.org/html/2605.15184); secondary: [DEV](https://dev.to/pueding/is-grep-all-you-need-grep-vs-vector-retrieval-for-agentic-search-534k), [agent-swarm blog](https://www.agent-swarm.dev/blog/is-grep-all-you-need-agent-memory)
- **LMEB: Long-horizon Memory Embedding Benchmark** (arXiv:2603.12572):
  - Agentic grep search outperformed KaLM-Embedding-V2.5 + Lychee reranker on LoCoMo.
  - The cost was about 80.3K tokens per single-hop query and 208.6K per multi-hop query.
  - Source: [arXiv](https://arxiv.org/pdf/2603.12572)
- **"Memory Tax: Dissecting the Net Value of Agent Memory"** (arXiv:2610.34073, Oct 2026; Xuan He + 10 coauthors):
  - In a large-scale evaluation across LongMemEval-s, PersonaMem-v2 and MINT-Eval and several backbones, **many dedicated memory systems underperform naive RAG over raw history**, a negative net value.
  - It splits net value into *memory benefit* (queries memory solves that RAG misses) and *memory tax* (queries RAG solves that memory loses).
  - The tax comes from faithfulness loss and accessibility loss.
  - Reported fix: augmenting memory with its source raises SimpleMem from 73.6% to 82.8% accuracy (Qwen3.6-27B, LongMemEval-s). That figure comes from a third-party summary and is unverified.
  - Sources: [arXiv abs](https://arxiv.org/abs/2610.34073); [HF](https://huggingface.co/papers/2610.34073); [DAIR](https://academy.dair.ai/papers/memory-tax-dissecting-the-net-value-of-agent-memory-2610.34073); [code](https://github.com/hexuan21/MemoryTax)
- **Oracle FS-agent vs DB-agent benchmark** (vendor-run, reported by Lightspeed):
  - On 10 queries, both scored about 82%.
  - On a larger paraphrased set, the grep-based filesystem agent scored 29.7% versus 87.1% for the vector/DB agent, because keyword matching misses synonyms.
  - Source: [Lightspeed](https://lsvp.com/stories/file-systems-for-agents/)
- **"Ontology-Grounded Project Memory for Coding Agents"** (arXiv:2608.13662) found that conditions tie on ordinary retrieval, while **structured memory has a clear advantage on structural questions**. — [arXiv](https://arxiv.org/pdf/2608.13662)
- "Beyond Top-K: Replacing Black-Box Retrieval with Interpretable Agentic Operations" (arXiv:2608.06305) argues for agentic, interpretable operations over embedding top-k. — [arXiv](https://arxiv.org/pdf/2608.06305)
- "GitOfThoughts: Version-Controlled Reasoning and Agent Memory You Can Replay, Diff, and Merge" (arXiv:2606.14470) uses a plain versioned-store design. — [arXiv](https://arxiv.org/pdf/2606.14470)
- Anthropic (Nov 2026):
  - "It's also reasonable to consider transcript search as the foundation of Claude's memory."
  - The memory stack is files, a memory directory and wikis. Vectors and embeddings are not mentioned.
  - Wiki + search (61%) beat search alone (53%).
  - The top injected layer must stay small, because large injected files reduce adherence.
  - Source: [Anthropic](https://www.anthropic.com/research/components-of-agent-memory)
- "MAGE" (arXiv:2606.06090, reportedly EMNLP 2026) argues that memory should track **execution state rather than semantic similarity**. Reported results on MemoryArena: task success +7.8 to +20.4 pp and token use −55.1%. — [arXiv abs](https://arxiv.org/abs/2606.06090)

### Inferences
- FocalPoint's **SQLite + FTS5, no vectors** choice is validated by the grep and Memory Tax results, and by Anthropic's file + search design, **for agent-driven, iterative search over task-scale corpora**.
- The main risk is **paraphrase recall** on larger knowledge bases (the Oracle 29.7% result). Two cheap mitigations:
  - Let the agent re-query with synonyms, which is the agentic-grep pattern.
  - Add optional embedding rerank later.
- Memory Tax's "source augmentation" finding argues for keeping links from FocalPoint's summarized narratives back to raw events or logs.
- MAGE and the ontology paper support **structure (typed state) over similarity**, which is FocalPoint's core thesis.

### Gaps
- I found no study that tests SQLite FTS5 specifically. The grep evidence comes from conversational QA (LongMemEval, LoCoMo), not from task/project memory.

---

## 4. Task/goal/plan memory, attention/focus management, and proactive agents (FocalPoint's niche)

### Takeaway
This niche **became an explicit research area in 2026**. It has three strands:
1. **Memory as execution state** for long-horizon agents (MAGE, Horizon Gap, DAG planners).
2. **Prospective memory**: remembering future intentions and acting at the right time (Pro-Memory, PM-Bench, TriggerBench, ProMemAgent).
3. **Proactive-agent benchmarks** (ProAgentBench, ProactBench, ATRBench, AutoBuddy, HALO).

Three findings stand out:
- Remembering an intention is not the same as acting on it at the right time.
- Proactive monitoring is a bottleneck.
- Over-intervention has to be controlled.

Academic work on "attention/focus management" exists mostly as context engineering (context rot, compaction) rather than as a formal research line.

### Cited Findings

**Task/execution-state memory**
- **MAGE, "Beyond Semantic Organization: Memory as Execution State Management for Long-Horizon Agents"** (arXiv:2606.06090, Jun 2026, reportedly EMNLP 2026). Reported results on MemoryArena: +7.8–20.4 pp task success and −55.1% tokens over baselines. — [arXiv](https://arxiv.org/abs/2606.06090)
- **"Beyond Entangled Planning: Task-Decoupled Planning for Long-Horizon Agents"** (arXiv:2601.07577, Jan 2026). A Supervisor decomposes the task into a **DAG of sub-tasks**, and a Planner and Executor work per node. — [arXiv](https://arxiv.org/html/2601.07577)
- **HoloMind** (arXiv:2605.14504) is a household-robot benchmark and agent. It pairs a DAG planner (nodes are goals, edges are dependencies) with persistent memory. — [arXiv](https://arxiv.org/html/2605.14504v1)
- **Goal2Skill** (arXiv:2604.13942) is a robotics system whose planner keeps memory M_t, revises the sub-task plan, detects completion or failure, and triggers reflection. — [arXiv](https://arxiv.org/html/2604.13942v1)
- **M-Plan** (arXiv:2610.27032) is a multi-agent planning framework that integrates long- and short-term memory. — [arXiv](https://arxiv.org/abs/2610.27032)
- The **Horizon Gap** survey (2608.06663) treats planning and memory as linked lifecycle stages. — [arXiv](https://arxiv.org/abs/2608.06663)

**Proactive memory / reminders (closest analogue to FocalPoint's heartbeat)**
- **"Remember When It Matters: Proactive Memory Agent for Long-Horizon Agents"** (arXiv:2607.08716, Jul 2026):
  - A separate memory agent observes the trajectory and updates a structured memory bank.
  - That agent **decides whether to inject a concise reminder** into the action agent's context.
  - Source: [arXiv](https://arxiv.org/html/2607.08716v1)
- **Pro-Memory, "Prospective Memory for Proactive LLM Agents"** (arXiv:2611.05731, Nov 2026, Bei et al.):
  - It separates *maintaining future intentions* from *deciding when and how to act on them*.
  - It improves proactive task performance while limiting unnecessary interventions.
  - It argues that remembering the right intention does not guarantee acting at the right time.
  - Source: [arXiv](https://arxiv.org/abs/2611.05731)
- **ProMemAgent** (arXiv:2610.27400) uses prospective memory over sensory context to decide whether assistance is needed. It introduces the CPMBench benchmark. — [arXiv](https://arxiv.org/abs/2610.27400)
- **PM-Bench** (arXiv:2607.12385) finds that "proactive monitoring remains a challenging bottleneck." — [arXiv](https://arxiv.org/html/2607.12385)
- **TriggerBench** (arXiv:2606.23459) is a benchmark for prospective, trigger-based action. It was seen only as a citation. — via [search](https://arxiv.org/pdf/2611.05731)

**Proactive-agent benchmarks**
- **ProAgentBench** (arXiv:2602.04482) uses real-world data for proactive assistance in continuous workflows. — [arXiv](https://arxiv.org/pdf/2602.04482)
- **ProactBench** (arXiv:2605.09228, Boson AI) measures noticing and acting on needs the user implied but did not state. — [arXiv](https://arxiv.org/html/2605.09228)
- **"Ask Now, Use Later" / ATRBench** (arXiv:2605.28108) finds that default agents fall at least 62 points below an oracle on the "proactivity gap", and that prompting closes little of it. — [arXiv](https://arxiv.org/pdf/2605.28108)
- **AutoBuddy** (arXiv:2611.12393, Nov 2026) evaluates always-on agents on task discovery and execution in a simulated workplace. — [arXiv](https://arxiv.org/pdf/2611.12393)
- **HALO** (arXiv:2610.39086) is a human-grounded, granular evaluation of proactive agents. — [arXiv](https://arxiv.org/pdf/2610.39086)
- Also seen only as citations: ProEvent (2607.17701) and the Proactive Agent Research Environment (2604.00842). — via [search](https://arxiv.org/pdf/2602.04482)
- **Security of proactive agents:** "Who Is Your Agent Serving? Provider-Side Indirect Prompt Injection in Proactive Agents" (arXiv:2610.05266). — [arXiv](https://arxiv.org/pdf/2610.05266)

**Attention / focus (context engineering)**
- Chroma's July 2025 "context rot" study tested 18 LLMs and found non-uniform degradation as input length grows. — via [Thoughtworks](https://www.thoughtworks.com/insights/blog/generative-ai/what-is-context-rot); [LogRocket](https://blog.logrocket.com/context-rot-slowing-down-your-ai-agent-how-fix/)
- Recommended mitigations include filtering tool outputs, summarizing history, just-in-time retrieval, compaction and structured note-taking. Rewriting current goals into the recent end of the context helps prevent drift. — [Thoughtworks](https://www.thoughtworks.com/insights/blog/generative-ai/what-is-context-rot)
- Anthropic: "Injected memories use up valuable tokens, and an incorrect note can be carried into every session." For strong models, "giving them freedom to judge salience was more effective than setting fixed rules." — [Anthropic](https://www.anthropic.com/research/components-of-agent-memory)

### Inferences
- FocalPoint's niche is now **validated as a research direction** but **no longer empty**:
  - MAGE (execution-state memory) and "Remember When It Matters" (a separate monitor that injects reminders) are close conceptual neighbors of the Spine/heartbeat design.
  - The Pro-Memory and ATRBench findings warn that a heartbeat which raises alerts needs a timing/intervention policy, or it will cause alert fatigue.
- These benchmarks offer FocalPoint ready-made ways to evaluate itself instead of only describing itself: MemoryArena (used by MAGE), PM-Bench, TriggerBench and AutoBuddy.
- The Anthropic quote about salience is a mild **threat** to FocalPoint's fixed, rule-based L0/L1/L2 trimming. Stronger models may do better choosing what they need, for example by pulling context through tools, than with fixed tiers.
- Role-based context (strategy/review/execution) and multi-role review have no direct memory-paper analogue that I found. The closest work is the per-role/scope governance in Governed Shared Memory and MAP-Graph.

### Gaps
- I did not find any paper that formally studies "focus shifting" between tasks, or attention-tier budgeting like L0/L1/L2, as a memory mechanism.
- I could not read the MemoryArena benchmark paper. Its ID and venue are unconfirmed.

---

## 5. Open problems researchers name

### Takeaway
Researchers recurrently name five open problems:
1. **Net value / evaluation validity**: memory often loses to raw RAG, and benchmark numbers are disputed.
2. **Security**: memory poisoning is persistent and bypasses write-time filters.
3. **Governance**: access control, forgetting and provenance in shared memory.
4. **Consolidation quality**: consolidation can distort memory or lower precision.
5. **Autonomous skill/intention quality**: self-generated skills lag curated ones, and agents act at the wrong time.

### Cited Findings
- **Evaluation:**
  - Memory Tax shows a negative net value for many systems. — [2610.34073](https://arxiv.org/abs/2610.34073)
  - LoCoMo scores are disputed across vendors (84% vs 58.44% vs 75.14%), and papers use incompatible metrics (LLM-judge vs evidence recall). — [Atlan](https://atlan.com/know/zep-vs-mem0/); [2608.12365](https://arxiv.org/pdf/2608.12365)
- **Memory poisoning:**
  - MINJA injects memories through ordinary queries. A survey reports 76.8% average attack success (98.2% injection success), evading embedding-level sanitization.
  - FARMA forges stored reasoning, reaching up to 100% attack success against defenses including A-MemGuard (2607.05029).
  - MemPoison (2607.14651) finds that write-time defenses degrade sharply beyond direct corruption, and calls for provenance-aware defense across the whole lifecycle.
  - Sources: [2607.05029](https://arxiv.org/html/2607.05029); [2607.14651](https://arxiv.org/pdf/2607.14651)
- **Certified defense:** SMSR (arXiv:2606.12703) adds HMAC-SHA256 provenance at write time:
  - Attack success on unsigned injection falls from 93–100% to 0%.
  - When the agent writes its own poison, attack success falls from 65.3% to 5.3% (n=150).
  - These are author-only results, not replicated.
  - Source: [arXiv](https://arxiv.org/abs/2606.12703v1)
- **Further security work:**
  - MemAudit (2605.23723) audits poisoned memory after the fact.
  - "From Untrusted Input to Trusted Memory" (2606.04329) studies poisoning systematically.
  - An EHR-agent study (2601.05504) finds that trust-threshold calibration is hard.
  - The OWASP Agentic Top 10 2026 lists memory/context poisoning as ASI06.
  - Sources: [2605.23723](https://arxiv.org/pdf/2605.23723); [2606.04329](https://arxiv.org/pdf/2606.04329); [2601.05504](https://arxiv.org/abs/2601.05504)
- **Governance and forgetting:** GateMem finds that no method gets utility, access control and forgetting all at once. — [2606.18829](https://arxiv.org/abs/2606.18829)
- **Privacy:** AIM (2609.12320) and Audience-Bound Persistent Memory (2609.36373) study it. — [2609.12320](https://arxiv.org/abs/2609.12320); [2609.36373](https://arxiv.org/pdf/2609.36373)
- **Admission control:** "Epistemic admission in shared agent memory" (2609.30813) is a benchmark and diagnostic study. — [arXiv](https://arxiv.org/pdf/2609.30813)
- **Consolidation risks:**
  - ZenBrain's sleep consolidation lowered precision. — [2604.23878](https://arxiv.org/pdf/2604.23878)
  - In Anthropic's tests, a wiki grew to 1.77× the size of its source corpus, and an incorrect injected note propagates into every session. — [Anthropic](https://www.anthropic.com/research/components-of-agent-memory)
- **Skills:**
  - Self-generated skills underperform curated ones. — [2602.06052](https://arxiv.org/pdf/2602.06052)
  - Noisy skill corpora erase gains. — [2606.23127](https://arxiv.org/pdf/2606.23127)
- **Proactivity:**
  - Timing is a problem separate from recall. — [2611.05731](https://arxiv.org/abs/2611.05731)
  - Monitoring is a bottleneck. — [2607.12385](https://arxiv.org/html/2607.12385)
- **Parametric memory:** it cannot delete or audit facts and suffers interference. — [2606.30306](https://arxiv.org/pdf/2606.30306)

### Inferences
- A deterministic Spine that owns all writes is well placed for the **provenance/authorization defenses** that the security literature recommends. SMSR-style signed writes and MAP-Graph-style typed provenance are natural additions.
- Exposing writes as MCP tools is itself an attack surface, because tool outputs can carry injected content. This is consistent with MINJA and the provider-side injection paper.
- Explicit node status and supersession in a DAG addresses the "conflict resolution / temporal supersession" problem more directly than vector stores do.
- The open problems FocalPoint would inherit are **forgetting/archival policy**, **evaluation on standard benchmarks**, and **alert-timing calibration**.

### Gaps
- I found no 2026 paper specifically on **conflict resolution** between contradictory memories as a standalone topic, beyond supersession in governance papers and Mem0-style UPDATE/DELETE.
- I did not fetch the 2026 long-term-memory security survey (arXiv:2604.16548), which was seen only as a citation.
- Note for the report writer: arXiv IDs with month prefixes up to 2611 are consistent with the current date (Nov 2026). None of the paper details could be opened in this session.
