# Benchmarks and Evaluation Methods for AI Agent Memory Systems (as of 2026-11-27)

Research note on method: in this session WebFetch could only reach github.com. arxiv.org, letta.com, mem0.ai, getzep.com and essays.bloo-mind.ai failed to resolve (blocked by the session's network allowlist). Primary sources fetched in full: the GitHub issue getzep/zep-papers#5 and the zxj0302 audit repo. Everything else comes from search-engine summaries of the linked pages. Those are marked "(search summary)" where the exact figure could not be checked against the page. Several arXiv IDs returned by search are 2605–2611 preprints. They fit the date range, but none was opened, so their numbers are unverified.

## 1. Conversational memory benchmarks: what they measure and known flaws

### Takeaway
LoCoMo and LongMemEval are still the two numbers every vendor reports. Both are now known to be flawed. LoCoMo is short enough to fit in context, has a 6.4% wrong answer key, and uses a lenient judge. LongMemEval has noisy evidence labels and timestamps. The 2025–2026 successors (MemoryAgentBench, BEAM, RealMem, LongMemEval-V2) test harder things: forgetting and conflict resolution, contexts over 1M tokens, and project-state updates. An independent audit found defects in all six popular benchmarks.

### Cited Findings
**LoCoMo (Maharana et al., 2024)**
- 20 dialogues, 3,368 QAs. A 2026 audit found 30 QAs credit the wrong speaker, 10 have wrong answers (dates, durations, polarity), 79 evidence pointers are broken, and there are 4 bugs in `evaluation.py` (for example, the ROUGE-L metric actually returns ROUGE-1), plus a contradictory judge prompt. — [zxj0302/agent-memory-benchmark-audit](https://github.com/zxj0302/agent-memory-benchmark-audit)
- The dial481 audit reports that 99 of 1,540 scored questions (6.4%) have wrong gold answers, which puts the theoretical ceiling at 93.57%. It found 156 issues in total, 99 of them score-corrupting. — [dial481/locomo-audit (via HN reader)](https://hn.jakechampion.name/reader/https://github.com/dial481/locomo-audit); [Penfield Labs write-up](https://penfieldlabs.substack.com/p/we-audited-locomo-64-of-the-answer)
- Penfield Labs claims the LLM judge accepts "up to 63% of intentionally wrong answers" (headline claim, not checked in the repo). — [Penfield Labs](https://penfieldlabs.substack.com/p/we-audited-locomo-64-of-the-answer)
- Zep's critique: LoCoMo conversations fit in modern context windows, there are no knowledge-update questions, and some questions have wrong speaker labels or several defensible answers. — [rajsuyash.com summary](https://rajsuyash.com/blog/mem0-vs-zep-benchmark-comparison.html) (search summary); see also Zep's comment in [zep-papers#5](https://github.com/getzep/zep-papers/issues/5)
- Category 5 (adversarial) is conventionally excluded. Including or excluding it was the core of the Zep/Mem0 dispute (see §3). — [zep-papers#5](https://github.com/getzep/zep-papers/issues/5)
- RealMem's authors describe LoCoMo as "limited to simulating human–human social chit-chat". — [RealMem arXiv 2601.06966](https://arxiv.org/abs/2601.06966)

**LongMemEval (Wu et al., ICLR 2025)**
- The S variant has about 115k tokens of history per question, according to Zep's full-context baseline. Full-context GPT-4o scores 60.2% and GPT-4o-mini 55.4%. — [Zep blog](https://blog.getzep.com/state-of-the-art-agent-memory/) (search summary)
- Audit of the Oracle and S_cleaned subsets (500 items each): 12.4% of `answer_session_ids` lists include distractor sessions, 42.2% of S_cleaned histories are unsorted, and 15.2% have timestamps earlier than the conversation they describe. The judge accepts ±1 day/week/month temporal errors and stale facts, and has a `'yes'` substring bug. — [zxj0302 audit](https://github.com/zxj0302/agent-memory-benchmark-audit)
- The original paper found dense retrieval clearly better than BM25. — [LongMemEval arXiv 2410.10813](https://arxiv.org/pdf/2410.10813) (search summary)
- LongMemEval-V2 (May 2026) is harder. The top listed system, AgentRunbook-C, averages 72.5% against an oracle ceiling of 86–90%. — [benchmarklist.com](https://benchmarklist.com/benchmarks/longmemeval_v2/) (aggregator; unverified)

**MSC / DMR**
- No 2025–2026 sources on MSC or DMR turned up in this session. DMR (Deep Memory Retrieval, from MemGPT) is generally treated as saturated, and Zep moved to LongMemEval for that reason. That is background knowledge, not verified here (see Gaps).

**MemoryAgentBench (Hu, Wang, McAuley, UCSD; arXiv 2507.05257, ICLR 2026)**
- It tests four competencies: accurate retrieval, test-time learning, long-range understanding, and selective forgetting. Information arrives incrementally and questions come afterwards. It adds two new datasets: EventQA (retrieval) and FactConsolidation (forgetting: later facts contradict earlier ones, with single- and multi-hop questions). The authors conclude that "current methods fall short of mastering all four." — [arXiv 2507.05257](https://arxiv.org/abs/2507.05257); [ICLR 2026 PDF](https://proceedings.iclr.cc/paper_files/paper/2026/file/fd1eff9dd295df50a41f2521942fa31d-Paper-Conference.pdf)
- Context depth is about 103k–1.44M tokens, with about 2,071 questions. — [cognee guide](https://www.cognee.ai/agent-memory-evaluation-datasets-benchmarks) (secondary)
- ICLR reviewers called the selective-forgetting task "somewhat artificial with synthetic serial data". — [UCSD reviews PDF](https://cseweb.ucsd.edu/~jmcauley/reviews/iclr26c.pdf) (search summary)
- Audit: 7 conflict-resolution items mark the superseded fact as gold. There are 15 verbatim train–test leaks, and a substring-match bug lets "no" match "I do not know". — [zxj0302 audit](https://github.com/zxj0302/agent-memory-benchmark-audit)
- A follow-up paper compares MemoryAgentBench's conflict-resolution scores against a "frozen last-write resolver" baseline, which suggests that simple deterministic bookkeeping is a strong baseline for that split. — [arXiv 2610.09193](https://arxiv.org/pdf/2610.09193) (title only; not read)
- A NeurIPS 2026 paper, "When Retrieval Is Not Enough: Obsolete Facts in Agent Memory", finds BM25 with top-64 beats top-512 (69% vs 58%) on single-hop FactConsolidation at 32K. More retrieved context hurts, because stale facts get retrieved. — [NeurIPS 2026](https://neurips.cc/virtual/2026/169567) (search summary)

**BEAM ("Beyond a Million Tokens", Tavakoli et al., arXiv 2510.27246, ICLR 2026)**
- 100 conversations and 2,000 questions, scaling from about 128K/100K up to 10M tokens. It covers about 10 abilities, including contradiction resolution and event ordering. Dialogues are single-user and single-narrative, so they are not agent trajectories. — [cognee guide](https://www.cognee.ai/agent-memory-evaluation-datasets-benchmarks); [mnemoverse field guide](https://mnemoverse.com/docs/research/evaluation/ai-memory-benchmarks-field-guide) (secondary; sources disagree on the minimum scale)
- Audit: 67 of 100 chat suites needed repair (115 QAs), with leap-day and skipped-month calendar bugs. In 10 evaluators the judge prompt omitted the question. — [zxj0302 audit](https://github.com/zxj0302/agent-memory-benchmark-audit)

**MemBench, PerLTQA**
- MemBench has 19 subsets and 26,637 QAs. The audit found 848 MCQs with duplicate options, 47 broken step pointers, and Python 2 `raise "..."` statements that crash the agent code. PerLTQA has 8,593 English QAs with a mistranslated character name that causes retrieval misses. — [zxj0302 audit](https://github.com/zxj0302/agent-memory-benchmark-audit)

**RealMem (arXiv 2601.06966, Findings of ACL 2026): the most relevant to FocalPoint**
- More than 2,000 cross-session dialogues across 11 scenarios, built around project-oriented long-term interaction. It has four query types: temporal reasoning, static retrieval, **dynamic updating of project states**, and proactive alignment. Existing memory systems "face significant challenges in managing the long-term project states and dynamic context dependencies." Code is at GitHub AvatarMemory/RealMemBench. — [arXiv](https://arxiv.org/abs/2601.06966); [ACL Anthology](https://aclanthology.org/2026.findings-acl.703/)

**Others mentioned**
- HaluMem (Chen et al., 2026) covers memory consistency and hallucination. — cited in [WorldLines arXiv 2606.18847](https://arxiv.org/pdf/2606.18847) (search summary)
- EvoMemBench evaluates memory "from a self-evolving perspective". — [arXiv 2605.18421](https://arxiv.org/pdf/2605.18421) (title only)
- HEAMS, "Holistic Evaluation of Agent Memory Systems" (Nov 2026). — [arXiv 2611.20144](https://arxiv.org/pdf/2611.20144) (title only; not read)
- ConvoMem is a dataset shipped in Supermemory's MemoryBench harness. — [Gitee mirror of supermemoryai/memorybench](https://gitee.com/joo2019/memorybench)
- THUIR's MemoryBench is a separate project about memory and continual learning. — [GitHub THUIR/MemoryBench](https://github.com/THUIR/MemoryBench)

### Inferences
- A score on any one of these benchmarks carries an error bar of several points just from label noise and judge leniency. Differences under about 5 points between vendors are not meaningful unless dataset version, answer model and judge are all fixed.
- LoCoMo mostly tests "find the fact in a chit-chat log". Knowledge-update and forgetting tests (LongMemEval KU, MemoryAgentBench FactConsolidation, RealMem dynamic project state) are where a structured, deterministic store like FocalPoint's task DAG plausibly has an advantage. The "frozen last-write resolver" paper hints that deterministic bookkeeping is a strong baseline there.

### Gaps
- MSC and DMR current status and numbers: no 2025–2026 source found.
- Per-method results tables for MemoryAgentBench and BEAM could not be read (arXiv blocked).
- HEAMS and the other 2026 holistic benchmarks were not read.

## 2. Agentic / long-horizon benchmarks relevant to task memory

### Takeaway
No standard benchmark covers "task tracking across sessions" for coding or project agents. The closest options are RealMem (project state in dialogue), Terminal-Bench and τ²-Bench used with a memory add-on, Vending-Bench 2 (long-term coherence over a simulated year), and Letta's Context-Bench (agentic context retrieval). One 2026 preprint shows a memory agent raising Terminal-Bench and τ²-Bench scores by 7–8 points, which is the kind of downstream-task evaluation that would suit FocalPoint.

### Cited Findings
- "Remember When It Matters: Proactive Memory Agent" (arXiv 2607.08716) runs a separate memory agent (Claude Opus 4.6) beside an unchanged action agent (Claude Sonnet 4.5). Terminal-Bench 2.0 goes from 37.6% to 45.9% and τ²-Bench from 55.0% to 61.8%. The paper says Terminal-Bench stresses "environment grounding, debugging continuity, procedural memory" and τ²-Bench stresses "policy adherence, user-state tracking, conversation-level execution state". — [arXiv 2607.08716](https://arxiv.org/html/2607.08716v1) (search summary)
- Vending-Bench ran one vending machine over about 2,000 messages and exposed weaknesses in long-term coherence. Vending-Bench 2 (Andon Labs, 2026) extends the simulation to a full year with adversarial suppliers. — cited in search summary of [arXiv 2607.08716](https://arxiv.org/pdf/2607.08716)
- Long-Horizon-Terminal-Bench 2.0 measures "sustained agent progress" (Oct 2026). — [arXiv 2610.14146](https://arxiv.org/pdf/2610.14146) (title only). LongHorizon-Harness reports Terminal-Bench 2.1 rising from 69.7% to 77.2% with Qwen 3.7-Plus. — [arXiv 2608.01964](https://arxiv.org/html/2608.01964) (search summary)
- Letta Context-Bench (Nov 2025) measures whether agents can retrieve, load and manage context across multi-step tasks. At launch: Claude Sonnet 4.5 74.0% at $24.58, Kimi K2 55.13% at $12.08. GPT-5 cost more than Sonnet 4.5 because it used more tokens. The leaderboard was last updated 2026-02-17. Context-Bench V2 is private. — [Letta Context-Bench](https://letta.com/blog/context-bench); [leaderboard.letta.com](https://leaderboard.letta.com/); [Tessl](https://tessl.io/blog/context-bench-benchmarking-ais-context-engineering-proficiency) (search summary)
- Letta reports that a model strong on SWE-Bench (GLM-5.2) is "one of the weakest models for memory generation and adherence". Coding skill does not imply memory skill. — [Letta, evaluating memory in production agents](https://www.letta.com/blog/evaluating-memory-in-production-agents/) (search summary)
- Other 2026 long-horizon benchmarks (titles only): E-Commerce Bench ([2608.30730](https://arxiv.org/pdf/2608.30730)), FM-Bench for long-horizon management with competing agents ([2608.18423](https://arxiv.org/pdf/2608.18423)), WorldLines for stateful embodied agents ([2606.18847](https://arxiv.org/pdf/2606.18847)), and OneDayAgent ([2608.05013](https://arxiv.org/pdf/2608.05013)).

### Inferences
- For a task/DAG memory, the most persuasive evidence is downstream: the same agent with and without FocalPoint, on a multi-session task suite, measuring task completion. Benchmark designs like Terminal-Bench or τ²-Bench with a memory layer already exist to copy.
- SWE-style benchmarks are single-session by default. Making them multi-session (killing the agent's context mid-task and resuming from memory) would test FocalPoint's core claim directly. I found no published standard version of this.

### Gaps
- No dedicated "project management / task tracking agent" benchmark was found beyond RealMem and FM-Bench.
- Vending-Bench 2 primary numbers were not retrieved (andonlabs not fetched).

## 3. Published head-to-head results and disputes

### Takeaway
Nearly all head-to-head numbers are vendor-reported, each on that vendor's own harness. The Zep–Mem0 LoCoMo dispute (May 2025) was never resolved: the issue was closed for inactivity. Letta's simple filesystem agent (74.0%) beat Mem0's best reported LoCoMo score (68.5%), and in Mem0's own paper full context scored highest on accuracy. Independent replications that exist show large drops from vendor numbers (for example, Mem0 on LongMemEval: 94.4 vendor-reported vs 49.0 in Vectorize's run).

### Cited Findings
**Mem0 paper (arXiv 2504.19413, April 2025), LoCoMo LLM-as-judge**
- Mem0 66.88%, Mem0g 68.44%, Zep 65.99%. — [atlan / search summaries](https://atlan.com/know/zep-vs-mem0/); Zep 65.99 ±0.16 is confirmed in [zep-papers#5](https://github.com/getzep/zep-papers/issues/5)
- Claims 91% lower p95 latency and more than 90% token savings vs full context. Full context "still achieved the highest score on accuracy". — [arXiv 2504.19413](https://arxiv.org/abs/2504.19413) (search summary). p95 from 17.12s to about 1.44s per a Mem0 FAQ. — [guide.mem0.ai](https://guide.mem0.ai/faq/memory-layer-slashes-p95-latency) (vendor)

**Zep paper (arXiv 2501.13956, Jan 2025) and LongMemEval blog**
- LongMemEval-S: Zep 71.2% vs full context 60.2% (GPT-4o), and 63.8% vs 55.4% (GPT-4o-mini). Median latency 2.58s vs 28.9s. Context about 1.6k vs about 115k tokens. — [Zep blog](https://blog.getzep.com/state-of-the-art-agent-memory/); [arXiv 2501.13956](https://arxiv.org/abs/2501.13956) (vendor; search summary)
- Zep originally claimed 84% on LoCoMo. — [zep-papers#5](https://github.com/getzep/zep-papers/issues/5)

**Zep vs Mem0 dispute: [getzep/zep-papers#5](https://github.com/getzep/zep-papers/issues/5) (fetched in full)**
- 2025-05-08: Mem0 CTO Deshraj posts "Revisiting Zep's 84% LoCoMo Claim". He says Zep's real score is 58.44% ±0.20 and lists these objections: the adversarial Category 5 questions were counted; Zep used a different system prompt and retrieval template from the other baselines; only a single run was published.
- 2025-05-12: Zep's Daniel Chalef replies with 75.14% ±0.17 over 10 runs, about 10 points above Mem0. He says Mem0 misconfigured Zep: both speakers were mapped to one user, timestamps were pasted into message text instead of `created_at` (available since May 2024), and searches ran serially, which inflated latency. He suggests LongMemEval instead.
- 2025-05-19: the issue is closed for inactivity. Secondary accounts say Zep edited its blog to say "we erred in how we calculated Zep's LoCoMo score". — [rajsuyash.com](https://rajsuyash.com/blog/mem0-vs-zep-benchmark-comparison.html) (search summary; not verified)
- Range of Zep LoCoMo scores now in circulation: 84 (Zep original), 75.14 (Zep corrected), 65.99 (Mem0 paper), 58.44 (Mem0 rerun).

**Letta "Is a filesystem all you need?" (2025-08-12)**
- An agent with GPT-4o-mini, LoCoMo history stored as files, and the tools grep/search_files/open/close scored 74.0%. Mem0g's reported best is 68.5%. Letta's explanation is that models are heavily trained on file operations from coding. Letta also says LoCoMo measures retrieval rather than memory management. — [Letta blog](https://www.letta.com/blog/benchmarking-ai-agent-memory); [Aktagon summary](https://signals.aktagon.com/articles/2026/02/benchmarking-ai-agent-memory-is-a-filesystem-all-you-need/) (search summary)
- A third-party table puts Letta at about 83.2% on LoCoMo. No source could be found for that figure. — noted in search summary of [dev.to](https://dev.to/varun_pratapbhardwaj_b13/5-ai-agent-memory-systems-compared-mem0-zep-letta-supermemory-superlocalmemory-2026-benchmark-59p3)

**2026 vendor claims on LongMemEval (all self-reported)**
- Mem0 94.4% (also cited as 93.4% for the managed platform), ByteRover 92.8%, Mastra 94.87%, Supermemory 85.2%, Zep 71.2%, EverMemOS 83.0%. A preprint, Agent Zero Memory, claims 95.60%. — [Agent Zero Memory arXiv 2608.29606](https://arxiv.org/pdf/2608.29606); [EverMind blog](https://evermind.ai/blogs/top-ai-memory-systems-benchmarked-in-2026) (search summaries)
- A Towards AI article says Mem0 published 92.5% on LoCoMo in April 2026. Unverified. — [Towards AI](https://pub.towardsai.net/mem0-vs-zep-vs-letta-a-folder-of-text-files-shouldnt-beat-the-61k-star-memory-layer-9d7e65c5799c) (search summary)
- MemPalace's headline 96.6% is recall_any@5 (any of the top 5 hits contains the answer), not QA accuracy. — [arXiv 2604.21284](https://arxiv.org/pdf/2604.21284) (search summary)

**Independent or semi-independent replications**
- Vectorize's evaluation (2026-03-15) puts Mem0 at 49.0 on LongMemEval, 45.4 points below Mem0's claim. — reported in [opensourceaireview.com](https://opensourceaireview.com/blog/which-ai-memory-layer-has-the-best-published-benchmarks-in-2026) (search summary; secondary)
- One developer measured Mem0's open-source edition at 32.4% on LongMemEval, against 93.4% vendor-reported for the managed platform. — [dev.to/everest_an](https://dev.to/everest_an/-i-benchmarked-ai-agent-memory-in-2026-and-the-numbers-tell-a-different-story-than-the-marketing-2ae4) (blog)
- No clean independent same-judge head-to-head of Mem0, Zep and Letta was found. Commentators note that almost no vendors report "the same dataset slice, same answer model, same judge, same ingestion rules". — search summary of [automem.ai](https://automem.ai/blog/agent-memory-in-2026-an-honest-comparison-of-mem0-zep-letta-and-the-rest) and [dreaming.press](https://dreaming.press/posts/how-to-read-an-agent-memory-benchmark.html)
- A Maximem paper reports the same system moving several points depending only on the answer model. — search summary for the LongMemEval leaderboard query (primary not located)
- Shared harnesses now exist that would allow same-judge reruns: Supermemory MemoryBench (MIT; providers supermemory/mem0/zep; benchmarks locomo/longmemeval/convomem; pluggable judge; ships a Claude Code skill for benchmarking a custom provider) and Mem0's `mem0ai/memory-benchmarks`. — [supermemory docs](https://supermemory.ai/docs/memorybench/overview); [GitHub supermemoryai/memorybench](https://github.com/supermemoryai/memorybench); Mem0 harness per [opensourceaireview](https://opensourceaireview.com/blog/which-ai-memory-layer-has-the-best-published-benchmarks-in-2026) (unverified)
- Supermemory's own guide warns that a 10-question smoke test "doesn't establish a reliable ranking". — [Supermemory blog](https://supermemory.ai/blog/build-agent-memory-eval-harness.md)

### Inferences
- Credibility ranking: (1) results on a public harness with disclosed answer model and judge, multiple runs, with a full-context baseline and a simple BM25/grep baseline included; (2) a vendor paper with code; (3) a vendor blog headline. Most 2026 numbers above 90% on LongMemEval are in category 3 and conflict with independent runs.
- The consistent pattern across sources: full context and simple file/grep agents are competitive or better on accuracy on LoCoMo-scale data. Memory products mostly win on latency and tokens, and on data that exceeds the context window.

### Gaps
- The exact full-context LoCoMo score in the Mem0 paper (commonly cited as about 72.9%) and Mem0's per-system token tables could not be verified (arXiv blocked).
- The Vectorize report itself was not located. OpenAI memory's numbers (in the Mem0 paper) were not verified.

## 4. Evidence on keyword/BM25/FTS vs embeddings vs hybrid vs agentic search

### Takeaway
The evidence is mixed but increasingly favourable to lexical and agentic search for agent memory. Letta's grep agent and a 2026 "Is Grep All You Need?" study both show grep or BM25 matching or beating vector retrieval on LoCoMo and LongMemEval. Embeddings or hybrid still win on paraphrased queries and on recall@k: the original LongMemEval paper favoured dense retrieval, and hybrid R@5 is about 95% vs 86% for BM25. One critique argues these benchmarks have high lexical overlap, which flatters BM25.

### Cited Findings
- Letta's grep/file-tool agent: 74.0% on LoCoMo, vs 68.5% for Mem0g. — [Letta](https://www.letta.com/blog/benchmarking-ai-agent-memory) (search summary)
- "Is Grep All You Need?" (arXiv 2605.15184): on 116 LongMemEval questions, grep generally beat vector retrieval in the same agents. As irrelevant context is added, vector retrieval degrades while grep stays roughly flat. Caveat from the same write-up: a good harness running vectors can beat a bad harness running grep. — [dev.to summary](https://dev.to/pueding/is-grep-all-you-need-grep-vs-vector-retrieval-for-agentic-search-534k) (secondary)
- A cost–retention preprint finds BM25 matches full context on LoCoMo at much lower cost (about 73.6%), but trails full context on LongMemEval's temporal and multi-session categories. — [preprints.org](https://www.preprints.org/frontend/manuscript/05ee2711cebcc1f5f3772edad42a993d/download_pub) (search summary; the full-context figure was garbled in the summary)
- MGMem (deterministic, provenance-preserving memory; Nov 2026): BM25 74.60% vs hybrid 73.80% on LongMemEval-S. — [arXiv 2611.33174](https://arxiv.org/pdf/2611.33174) (search summary). This is architecturally close to FocalPoint: deterministic and no embeddings needed.
- The original LongMemEval paper found dense retrieval "significantly higher" than BM25. — [arXiv 2410.10813](https://arxiv.org/pdf/2410.10813) (search summary)
- A MemPalace discussion reports BM25+vector hybrid at 95.2% R@5 vs BM25-only at 86.2% R@5 on LongMemEval-S (retrieval recall, not QA). — [GitHub MemPalace discussion #747](https://github.com/MemPalace/mempalace/discussions/747)
- The Entity-Collision protocol argues BM25 "trivially wins" because benchmark queries share entity tokens with gold passages but not with distractors. — [arXiv 2605.29630](https://arxiv.org/pdf/2605.29630) (search summary)
- "Beyond Top-K": BM25 67.3% grounding vs 58.0% for the authors' READ system, at a third of the cost and a third of the latency. — [arXiv 2608.06305](https://arxiv.org/pdf/2608.06305) (search summary)
- On stale facts, retrieving more makes things worse: BM25 top-64 69% vs top-512 58% on FactConsolidation. — [NeurIPS 2026](https://neurips.cc/virtual/2026/169567) (search summary)

### Inferences
- For FocalPoint's FTS5-only design, the literature supports the claim that a lexical store driven by an agent is a credible baseline, especially for entity-heavy task and project data. The open risk is paraphrase or synonym queries, so FocalPoint should report results by query type (lexical-overlap vs paraphrase), following the Entity-Collision idea.
- Stale-fact handling (supersession, last-write-wins on structured fields) matters more than retrieval method on update/forgetting tasks. A structured task DAG answers "current status" without retrieval at all.

### Gaps
- No direct study of FTS5 specifically (as opposed to BM25 in general) was found. FTS5 ranks with BM25, so results should transfer, but tokenizer and stemming choices matter. That point is untested here.

## 5. Metrics beyond accuracy

### Takeaway
Vendors mostly report p95 latency and tokens per query against a full-context baseline. Cost per benchmark run (Letta Context-Bench) is emerging. Write-side costs (ingestion latency, LLM calls per write, storage growth), staleness, conflict resolution and forgetting are rarely reported. MemoryAgentBench's selective-forgetting split, LongMemEval's knowledge-update category and RealMem's dynamic-project-state queries are the main places they are measured.

### Cited Findings
- Latency and tokens: Mem0 reports 91% lower p95 and more than 90% fewer tokens ([arXiv 2504.19413](https://arxiv.org/abs/2504.19413)). Zep reports 2.58s vs 28.9s median and 1.6k vs 115k tokens ([Zep blog](https://blog.getzep.com/state-of-the-art-agent-memory/)). The Zep–Mem0 dispute included latency inflated by serial search calls ([zep-papers#5](https://github.com/getzep/zep-papers/issues/5)).
- Cost to complete a benchmark: Context-Bench reports dollars per run ($24.58 Sonnet 4.5, $12.08 Kimi K2) and shows that token efficiency can outweigh per-token price. — [Letta Context-Bench](https://letta.com/blog/context-bench) (search summary)
- Forgetting and conflicts: MemoryAgentBench's selective forgetting and FactConsolidation ([arXiv 2507.05257](https://arxiv.org/abs/2507.05257)); obsolete facts ([NeurIPS 2026](https://neurips.cc/virtual/2026/169567)); BEAM's contradiction resolution ([cognee](https://www.cognee.ai/agent-memory-evaluation-datasets-benchmarks)); HaluMem's memory consistency and hallucination ([WorldLines citing it](https://arxiv.org/pdf/2606.18847)).
- Judge and metric choice: recall@5 vs QA accuracy (MemPalace's 96.6% is recall_any@5 — [arXiv 2604.21284](https://arxiv.org/pdf/2604.21284)). The LongMemEval judge accepts ±1-unit temporal errors and stale facts ([zxj0302 audit](https://github.com/zxj0302/agent-memory-benchmark-audit)).

### Inferences
- Write amplification is FocalPoint's natural differentiator. Mem0 and Zep run LLM extraction or graph construction on ingest. FocalPoint is deterministic SQLite with zero LLM calls per write (to be confirmed in the repo). That is reportable as LLM calls per write, write p50/p95 and bytes per memory, and almost no one reports these.
- Determinism (byte-identical context assembled from identical state) is a reproducibility metric competitors cannot easily match. It is worth reporting.

### Gaps
- No benchmark standardizes write-side cost or storage growth. No source quantified Mem0/Zep ingestion cost per message in this session.

## 6. What benchmark suite would be most persuasive for FocalPoint

### Takeaway
A credible package has three tiers. (A) Run the standard conversational benchmarks on a public harness for comparability, using the audited or corrected datasets, with full-context and naive-BM25 baselines and a disclosed judge. (B) Run update/forgetting/project-state benchmarks where a structured DAG should excel. (C) Run a downstream multi-session agent task suite (with vs without FocalPoint) measuring completion, tokens and latency. Report write-side and determinism metrics, which vendors omit.

### Cited Findings (building blocks)
- Comparability harness: Supermemory MemoryBench supports custom providers and runs locomo, longmemeval and convomem against mem0, zep and supermemory with a pluggable judge. — [GitHub](https://github.com/supermemoryai/memorybench); [docs](https://supermemory.ai/docs/memorybench/overview)
- Corrected datasets: the zxj0302 audit publishes a corrected suite for LoCoMo, BEAM, LongMemEval, MemoryAgentBench, MemBench and PerLTQA. — [GitHub](https://github.com/zxj0302/agent-memory-benchmark-audit). The dial481 LoCoMo audit gives a 93.57% ceiling. — [dial481](https://hn.jakechampion.name/reader/https://github.com/dial481/locomo-audit)
- Task-oriented: RealMem's dynamic project-state queries ([arXiv 2601.06966](https://arxiv.org/abs/2601.06966)); MemoryAgentBench FactConsolidation, with the last-write-resolver baseline ([arXiv 2610.09193](https://arxiv.org/pdf/2610.09193)); LongMemEval knowledge-update and temporal categories.
- Downstream protocol precedent: a memory add-on evaluated on Terminal-Bench 2.0 and τ²-Bench with an unchanged action agent (+8.3 and +6.8 points). — [arXiv 2607.08716](https://arxiv.org/html/2607.08716v1)
- Baselines to beat: full context (best accuracy in Mem0's own paper — [arXiv 2504.19413](https://arxiv.org/abs/2504.19413)) and a grep/filesystem agent (74.0% LoCoMo — [Letta](https://www.letta.com/blog/benchmarking-ai-agent-memory)).

### Inferences (recommended protocol)
1. **Tier A, comparability (required but not differentiating).** LongMemEval-S (cleaned or corrected) and LoCoMo (corrected; Categories 1–4; report the 93.57% ceiling) via MemoryBench, as a custom provider. Fix the answer model and judge, run 3 or more times, and report mean ± std. Include the full-context, BM25-over-raw-turns and grep-agent baselines. Report per-category scores, not only overall.
2. **Tier B, FocalPoint's home turf.** LongMemEval knowledge-update and temporal categories; MemoryAgentBench FactConsolidation (single- and multi-hop) against the last-write baseline; RealMem's dynamic project-state queries. Add a lexical vs paraphrase split (the Entity-Collision idea) to show honestly where FTS5-only search loses.
3. **Tier C, the claim that matters.** A custom multi-session task benchmark built from SWE-bench-Verified or Terminal-Bench tasks split into 3–5 sessions with context wiped between them. Run the same agent with (i) no memory, (ii) a notes or markdown file, (iii) Mem0 or Zep via MCP, (iv) FocalPoint. Measure task success, resume correctness (does the agent pick the right next DAG node), redundant-work rate, tokens and wall-clock time. Publish the tasks and harness.
4. **Systems metrics.** Write p50/p95, LLM calls per write (likely 0), context-assembly latency, DB size growth per 1k events, determinism (identical context hash on replay), cold start. Unit-test counts are not evidence of memory quality and should not be presented as such.
5. **Credibility hygiene.** Open-source the harness, pin dataset commits, disclose models and judge prompts, publish raw outputs, and invite reproduction. The Zep–Mem0 dispute shows that undisclosed configuration choices on either side end up discrediting everyone's numbers.

### Gaps
- No existing public benchmark for multi-session task-DAG memory was found, so Tier C has to be built. Whether RealMem's harness accepts plug-in memory providers was not checked.
