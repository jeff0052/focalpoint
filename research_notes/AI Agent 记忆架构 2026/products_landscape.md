# AI Agent Memory Product Landscape (as of 2026-11-27) — Competitive Map for FocalPoint

Method note: research was done via web search plus a few direct fetches. Several primary hosts were unreachable from this session (mem0.ai, mnemoverse.com returned DNS failures; the GitHub REST API was blocked for repos not attached to the session), so many figures come from search-result snippets of secondary sources. The direct fetches that worked were github.com/mem0ai/mem0 and code.claude.com/docs/en/memory. "Vendor claim" marks figures from a company's own material; "secondary" marks aggregators and blogs. Star counts change quickly and differ between sources; treat each as a snapshot.

## 1. Dedicated memory layers: players, features, architecture, pricing, license, traction

### Takeaway
The dedicated-memory market has settled on a few funded leaders: Mem0 (the largest open-source community, about 69.5K stars, $24M raised, AWS's exclusive Strands memory provider), Zep (which dropped its Community Edition to focus open source on the Graphiti temporal-graph library), Letta (now centred on Letta Code, a coding-agent harness) and Cognee ($7.5M seed in Feb 2026, the only clearly dated 2026 round I found). Below them sits a long tail: Supermemory, MemOS, Memori, Memobase, LangMem and Basic Memory. All of them are semantic/episodic *recall* systems built on vector, graph or hybrid stores. None I found is a task/project-state system.

### Cited Findings
**Mem0**
- GitHub repo: about 69.5K stars, 8.3K forks, Apache-2.0 license (fetched directly, Nov 2026) — [GitHub mem0ai/mem0](https://github.com/mem0ai/mem0)
- April 2026 "new memory algorithm" (vendor claim, managed platform). LoCoMo rose from 71.4 to 92.5 and LongMemEval from 67.8 to 94.4. BEAM scores are 64.1 at 1M tokens and 48.6 at 10M. The README says OSS users should expect smaller gains. Extraction is now single-pass and add-only, with entity linking, fused semantic + keyword (BM25) + entity retrieval, and temporal reasoning. An OSS v2 to v3 migration guide exists — [GitHub mem0ai/mem0](https://github.com/mem0ai/mem0)
- Product surface includes user/session/agent memory levels, a self-hosted Docker Compose server with auth on by default, a managed cloud, a CLI, "Agent Skills" for Claude Code and Cursor, and self-signup for agents (`mem0 init --agent`) — [GitHub mem0ai/mem0](https://github.com/mem0ai/mem0)
- Funding: $24M total, made up of a previously unannounced $3.9M seed (Kindred Ventures) and a $20M Series A led by Basis Set, with participation from Peak XV, YC and GitHub Fund. Announced Oct 28, 2025 — [TechCrunch](https://techcrunch.com/2025/10/28/mem0-raises-24m-from-yc-peak-xv-and-basis-set-to-build-the-memory-layer-for-ai-apps); [Mem0 Series A post](https://mem0.ai/series-a)
- AWS chose Mem0 as the "exclusive memory provider" for its Strands Agent SDK (vendor claim). There is also an Amazon Neptune Analytics integration for graph-native memory (July 2025) — [Mem0 blog](https://mem0.ai/blog/aws-and-mem0-partner-to-bring-persistent-memory-to-next-gen-ai-agents-with-strands); [AWS What's New](https://aws.amazon.com/about-aws/whats-new/2025/07/amazon-neptune-analytics-mem0-graph-native-memory-in-genai-applications)
- Secondary sources give star counts ranging from about 48K to 66K depending on date, which is consistent with growth to about 69.5K — [Atlan](https://atlan.com/know/best-ai-agent-memory-frameworks-2026/); [Vectorize](https://vectorize.io/articles/best-ai-agent-memory-systems)

**Zep / Graphiti**
- Zep ended support for Zep Community Edition and now focuses its open-source effort on Graphiti, so the self-hostable piece is the Graphiti library rather than the full Zep product — [Zep blog: new direction for open source](https://blog.getzep.com/announcing-a-new-direction-for-zeps-open-source-strategy/)
- Graphiti is a temporal knowledge graph under Apache-2.0. Zep's own post marked 20K stars together with an MCP server 1.0 release — [Zep blog: Graphiti hits 20K stars](https://blog.getzep.com/graphiti-hits-20k-stars-mcp-server-1-0.md). Secondary estimates are about 27.3K (June 2026) and 28K+ (Q3 2026), and one listing says about 32K. None of these was independently verified — [baeseokjae review](https://baeseokjae.github.io/posts/zep-ai-agent-memory-review-2026/); [GitHub getzep/graphiti](https://github.com/getzep/graphiti)
- Pricing is credit-based (secondary). One credit covers 350 bytes ingested and retrieval is free. Free tier: 1,000 credits/month. Flex: 50K credits/month, reported as $125/mo by most sources (one says $1,250/yr). Flex credits roll over for 30 days and Flex Plus for 60. Enterprise is custom — [Zep pricing](https://getzep.com/pricing); [Activepieces](https://www.activepieces.com/blog/zep-ai-pricing-in-2026-plans-and-what-you-pay.md)
- The pricing page banner reads "Introducing Zep Agent Skills" — [Zep pricing](https://getzep.com/pricing)
- Funding: YC W24, and one source lists a $500K pre-seed (March 2024). I found no larger round — [YC](https://ycombinator.com/companies/zep-ai); [Atlan](https://atlan.com/know/best-ai-agent-memory-frameworks-2026/)
- One review flags a 2026 CVE (graph query injection) in Graphiti (secondary, not verified) — [Glama memory list / roundups](https://glama.ai/mcp/best/memory)

**Letta (formerly MemGPT)**
- $10M seed led by Felicis (Sept 2024). I found no 2026 round — [PR Newswire](https://prnewswire.com/news-releases/berkeley-ai-research-lab-spinout-letta-raises-10m-seed-financing-led-by-felicis-to-build-ai-with-memory-302257004.html)
- Sleep-time agents shipped in April 2025, with a background agent that edits in-context memory blocks. Letta's 2026 "next phase" post says server-side sleep-time agents will be replaced by a client-side subagent system — [Letta: our next phase](https://letta.com/blog/our-next-phase)
- Letta Code launched Dec 2025 and was made the flagship product on Mar 16, 2026. It is described as a model-agnostic agent harness with persistent memory. A desktop app (April) and Channels (May) were reported by secondary sources — [Latent Space wiki](https://latent.space/wiki/letta-ai-558a42a5/); [rywalker research](https://rywalker.com/research/letta)
- Unverified: an August 2026 Agent SDK launch (single jobs-site source) — [Simplify](https://simplify.jobs/c/Letta)

**Cognee**
- $7.5M seed, Feb 19, 2026, led by Pebblebed and 42CAP. The money is earmarked for a managed cloud, a Rust engine for edge devices and more OSS connectors. Outlets disagree on the currency and amount (€7.5M; SeedTable says $8.9M) — [Cognee blog](https://www.cognee.ai/blog/cognee-news/cognee-raises-seven-million-five-hundred-thousand-dollars-seed); [Dealroom](https://dealroom.co/news/125999-cognee-raises-7-5m-to-build-memory-layer-for-ai-agents/)
- Architecture is knowledge graph + vector. About 22K stars as of July 2026 (secondary), and several 2026 MCP roundups rank it as the top memory MCP server — [Glama](https://glama.ai/mcp/best/memory); [ChatForest](https://chatforest.com/guides/best-memory-mcp-servers/)

**Supermemory**
- Seed of about $3M (Oct 2025) per StartupIntros. ZoomInfo says $2.6M. Toolradar gives an "Oct 2026" date, which conflicts with the others, and names Google, OpenAI and Cloudflare as investors. These conflicts are unresolved — [StartupIntros](https://startupintros.com/news/supermemory-seed-2025-10); [ZoomInfo](https://www.zoominfo.com/financial/supermemory-inc/5000576092); [Toolradar](https://toolradar.com/companies/supermemory-ai)

**MemOS (MemTensor)**
- Research-origin "memory OS". The repo lists 2026 releases: an OpenClaw plugin (cloud and local) in March 2026 and memos-local-plugin 2.0 for Hermes Agent/OpenClaw in May 2026. The paper claims first place across categories against Mem0, Zep, Memobase, MIRIX, MemU and Supermemory (vendor or academic self-report) — [GitHub MemTensor/MemOS](https://github.com/MemTensor/MemOS); [arXiv 2507.03724](https://arxiv.org/html/2507.03724)
- Note: the name "MemOS" directly overlaps with FocalPoint's "cognitive OS" framing.

**Memori, Memobase, LangMem, Basic Memory**
- Memori is positioned as SQL-native (BYODB) with auditable structured state, aimed at air-gapped enterprise. This comes from a comparison article; I did not confirm its current owner (GibsonAI/MemoriLabs) — [Atlan: Mem0 alternatives](https://atlan.com/know/mem0-alternatives/)
- LangMem: LangGraph-native, using the LangGraph Store with "zero new infrastructure" — [Atlan: Mem0 alternatives](https://atlan.com/know/mem0-alternatives/)
- Memobase is classed as profile-based user memory — [woshipm survey (CN)](https://www.woshipm.com/ai/6299825.html)
- Basic Memory is a local-first MCP server that keeps plain Markdown files and appears on several "best memory MCP" lists — [ChatForest](https://chatforest.com/guides/best-memory-mcp-servers/); [Hjarni](https://hjarni.com/blog/best-mcp-memory-server)

### Inferences
- The leaders compete on recall benchmarks (LoCoMo, LongMemEval, BEAM), hybrid retrieval and temporal graphs. Their unit of storage is the "fact/episode about a user or entity". They do not store "work item with state".
- Strategic convergence: Letta pivoted to a coding-agent product (Letta Code), and Mem0 and Zep both ship "Agent Skills" for coding assistants. The funded players are moving toward FocalPoint's target user (coding/ops agents), but through recall memory rather than task structure.
- Licensing: the leaders are permissive (Apache-2.0 for Mem0 and Graphiti). FocalPoint's BSL 1.1 is an outlier among OSS memory projects and may cost it adoption and "default choice" status in framework integrations.

### Gaps
- Current exact star counts for Graphiti, Letta, Cognee, Supermemory, MemOS, Memori, Basic Memory and LangMem. The GitHub API was blocked and only the Mem0 repo page was fetched.
- Mem0 current pricing tiers. mem0.ai was unreachable. From training knowledge, which I could not verify for 2026, the tiers were Free, Starter about $19/mo and Pro about $249/mo, with graph memory on Pro.
- Letta Cloud pricing, Supermemory pricing, Cognee cloud pricing: not found.
- Memori ownership, licence and 2026 status; Memobase 2026 status: not found.

## 2. Frontier-lab built-in memory: what shipped in 2025–2026

### Takeaway
All four large platforms now ship default-on or free consumer memory, and Anthropic ships developer primitives too: a client-side memory tool, context editing/compaction, and CLAUDE.md plus auto-memory in Claude Code with org-managed policy files. This removes the market for "personal-preference memory" plugins. It leaves room for structured, cross-tool, team-level work state, which the labs do not model.

### Cited Findings
**Anthropic**
- Claude consumer memory had been paid-only since Oct 2025. It opened to free users on Mar 2, 2026, together with an import tool that uses a prompt to pull context out of ChatGPT or Gemini, plus export — [MacRumors](https://macrumors.com/2026/03/02/anthropic-memory-import-tool/); [Engadget](https://www.engadget.com/ai/anthropic-brings-memory-to-claudes-free-plan-220729070.html); [Bloomberg](https://www.bloomberg.com/news/articles/2026-03-03/anthropic-tries-to-win-users-from-chatgpt-with-memory-feature)
- API memory tool (`memory_20250818`): client-side and file-based. Claude issues file operations, the developer implements storage, and the API injects a "check memory first" system prompt. Context editing (`context_management`) clears old tool results. Anthropic's internal figures, as reported by third parties, are +39% for memory + context editing and +29% for context editing alone. Sources disagree on whether the tool is still beta — [Claude cookbook: context engineering](https://platform.claude.com/cookbook/tool-use-context-engineering-context-engineering-tools); [devmoment](https://www.devmoment.dev/journal/memory-tool-lost-its-exemption-from-context-editing)
- One blog reports that the docs line recommending memory tool calls be exempted from context clearing was removed by Aug 2026 (single source) — [devmoment](https://www.devmoment.dev/journal/memory-tool-lost-its-exemption-from-context-editing)
- Claude Code has two layers. CLAUDE.md is written by humans and comes in managed-policy, user, project and local scopes; AGENTS.md is supported; `.claude/rules/` holds path-scoped rules; `claudeMdExcludes` and `claudeMd` handle org-wide policy in managed settings. Auto memory is written by Claude, scoped per repo and shared across worktrees, and loads the first 200 lines or 25KB of MEMORY.md. Subagents can keep their own persistent memory. The docs say both layers are context, not enforced configuration — [Claude Code docs: memory](https://code.claude.com/docs/en/memory)

**OpenAI**
- "Dreaming" (V3), announced June 4, 2026: background curation of memories from chat history, a user-steerable memory summary, and memories that update over time (a planned trip becomes "went to Singapore"). It launched to Plus/Pro in the US first, then expanded to Free/Go and more countries. OpenAI reports factual recall up from 67.9% (2025) to 82.8% (2026) (vendor claim; another outlet cites a 41.5% baseline) — [OpenAI](https://openai.com/index/chatgpt-memory-dreaming); [Engadget](https://engadget.com/2187811/chatgpt-s-memory-is-getting-better-especially-if-you-re-on-the-free-tier); [The Decoder](https://the-decoder.com/chatgpt-now-saves-narrative-dossiers-about-you-sorted-by-work-hobbies-and-travel-preferences/)
- Agents SDK: Sessions store conversation history in your own DB or OpenAI's Conversations API, with compaction via `OpenAIResponsesCompactionSession`. No documented long-term memory primitive (secondary, Mar 2026) — [Medium comparison](https://shibuiyusuke.medium.com/a-deep-comparison-of-memory-implementations-across-gemini-openai-and-anthropic-the-state-of-5b5fc9c1fa6c)

**Google**
- Gemini "Personal Intelligence" launched as a US paid beta in Jan 2026, drawing on Gmail, Photos, YouTube and Search. Memories plus chat import reached the UK on Apr 29, 2026. In Germany "Remembered Information" is on by default — [WebProNews](https://www.webpronews.com/google-brings-geminis-memory-and-chat-import-tools-to-uk-users-ending-personal-intelligence-holdout/); [PPC Land](https://ppc.land/gemini-brings-memories-and-chat-import-to-uk-users/); [heise](https://heise.de/-11276628)

**Microsoft**
- Consumer Copilot memory was unveiled April 2025 — [GeekWire](https://www.geekwire.com/2025/microsoft-gives-copilot-its-own-memory-in-new-push-to-personalize-its-ai-assistant/)
- M365 Copilot Memory GA has slipped repeatedly (Message Center MC1158329 was revised as late as June 2026, with a July 2026 GA target still showing "In development"). "Communication memory" built from email, chats and meetings is rolling out worldwide through Dec 2026 — [MC1158329 diff](https://mc.merill.net/message/MC1158329/compare/2025-10-23T17-45-04-953Z/2026-06-08T23-05-44-133Z); [Microsoft release comms](https://www.microsoft.com/releasecommunications/api/v2/m365/551195); [MC1127234](https://mc.merill.net/message/MC1127234)
- Reported (secondary): M365 Copilot memory lives in a hidden Exchange mailbox folder, and the GitHub Copilot SDK has no APIs to list, edit or delete memories — [nhimg](https://nhimg.org/ai-agent-memory-explained)

### Inferences
- Consumer memory is commoditised and free. Every lab added import tools in 2026 (Claude in March, Gemini by April), so memory has become a switching-cost battleground and is no longer a product third parties can sell into.
- Anthropic's developer primitives (the memory tool plus CLAUDE.md and auto-memory) are deliberately unopinionated files. They cover "what the agent knows" but not "what state the work is in". That leaves FocalPoint's niche open, but with a low bar: a well-kept MEMORY.md plus a TODO file is the free baseline FocalPoint has to beat.
- The labs' memory is siloed per product (ChatGPT, Claude, Gemini, Copilot). A neutral, cross-tool store accessed over MCP is the main structural opening left for third parties.

### Gaps
- I could not verify the current GA/beta status of the Claude memory tool directly on platform.claude.com docs.
- Claude "Managed Agents" and Claude Code task/todo persistence features in 2026 were not investigated. These may overlap with FocalPoint's task tracking and need checking.
- I did not confirm OpenAI Agents SDK long-term memory additions after March 2026.

## 3. MCP memory server ecosystem

### Takeaway
The official reference "Knowledge Graph Memory Server" (a local JSONL file of entities and relations) is still the most-installed baseline at about 45K weekly npm downloads per one source. Cognee, Graphiti, Basic Memory, mcp-memory-service, Mem0/OpenMemory and Supermemory lead the third-party lists. The rankings come mostly from vendor-written roundups.

### Cited Findings
- The reference Memory server in modelcontextprotocol/servers stores entities and relations in local JSONL. It has about 45K weekly npm downloads (one source) and was last released Jan 2026 — [DEV: memory MCP server review](https://dev.to/grove_chatforest/the-memory-mcp-server-a-knowledge-graph-that-needs-a-better-brain-4o4b); [Glama](https://glama.ai/mcp/best/memory)
- Frequently listed alternatives: Cognee (graph + vector, about 22K stars, July 2026), Graphiti MCP server 1.0, Basic Memory (Markdown on disk), mcp-memory-service (about 5 ms retrieval per one aggregator), Neo4j MCP memory, Mem0, Supermemory, Zep, and codebase-memory-mcp for code graphs — [ChatForest](https://chatforest.com/guides/best-memory-mcp-servers/); [Hjarni (vendor)](https://hjarni.com/blog/best-mcp-memory-server); [Coworker (vendor)](https://coworker.ai/blog/best-mcp-memory-servers); [Zep blog](https://blog.getzep.com/graphiti-hits-20k-stars-mcp-server-1-0.md)
- MCP directories list memory as a crowded category with many pages of servers — [mcpservers.org memory category](https://mcpservers.org/category/memory?page=4)

### Inferences
- "Memory MCP server" is a commodity category with hundreds of entries. FocalPoint's 23 MCP tools are not a differentiator by count. Discovery and positioning ("task-state server" rather than "memory server") will matter more.

### Gaps
- No independent download or install statistics across servers (npm/PyPI counts) beyond the single 45K/week figure.

## 4. Does any product offer task lifecycle / dependency DAG / proactive alerts / role-based context / decision review? Is FocalPoint's README table still accurate?

### Takeaway
On the five named competitors the README is largely still accurate: Mem0, Zep, Letta, CrewAI and Claude memory do not offer task status lifecycles, dependency DAGs, heartbeat alerts or decision review as first-class features. But it compares FocalPoint against the wrong category. The real competition for "agent task DAG as memory" is agent-native issue trackers, above all Steve Yegge's Beads, which reached 1.0 and made the Thoughtworks Technology Radar in Nov 2026.

### Cited Findings
- Beads (`bd`) is a git-backed issue tracker "designed as a persistent memory layer for coding agents". It gives a structured task graph with blocker relationships, four dependency types (blocks, related, parent-child, discovered-from) and ready-work detection (`bd ready`). It renders dependency trees (`bd dep tree`), now uses Dolt as its only storage backend, and has an MCP server (`beads-mcp`). An older README warned that MCP mis-routes issues across multiple repos — [GitHub steveyegge/beads](https://github.com/steveyegge/beads); [pkg.go.dev beads](https://pkg.go.dev/github.com/steveyegge/beads@v0.12.0); [Algolia docsearch: Beads](https://docsearch.algolia.com/mcp/docs/repo/gastownhall/beads)
- Thoughtworks Radar (Nov 2026): Beads reached 1.0 and moved to the Gas Town Hall organisation, and is now "heavier" than its lightweight origins — [Thoughtworks Radar: Beads](https://www.thoughtworks.com/zh-cn/radar/tools/beads)
- Letta's planned client-side subagent system and Letta Code's persistent memory, and the agent skills from Mem0 and Zep, target the same coding-agent users. None of the sources describe task status or dependency features — [Letta: our next phase](https://letta.com/blog/our-next-phase); [GitHub mem0ai/mem0](https://github.com/mem0ai/mem0); [Zep pricing](https://getzep.com/pricing)
- ChatGPT Dreaming updates memories over time (planned to done), a limited form of temporal state change, but for personal facts, not tasks — [OpenAI](https://openai.com/index/chatgpt-memory-dreaming)
- Claude Code scopes memory (managed/user/project/local, path-scoped rules, per-subagent memory). That is a partial analogue to "role-based context", but it is not task-aware — [Claude Code docs: memory](https://code.claude.com/docs/en/memory)
- Graphiti's temporal KG tracks fact validity over time (bi-temporal edges), which is closest among the memory layers to "state history", but it holds no task semantics — [GitHub getzep/graphiti](https://github.com/getzep/graphiti)

### Inferences
- Checking each claim against current evidence:
  - **Task lifecycle and DAG dependencies.** Still absent from Mem0, Zep, Letta and Claude memory. Present in Beads, and in general issue trackers (Linear, GitHub Issues), which agents now reach via MCP. FocalPoint itself syncs to GitHub and Notion. "No one has task lifecycle" is no longer true of the wider agent-tooling market.
  - **Proactive heartbeat alerts.** No memory layer found offers this. It remains a plausible differentiator, though schedulers and cron-style agents (e.g. scheduled tasks in agent platforms) can approximate it.
  - **Decision review workflow.** Not found in any memory product. Plausibly unique among memory layers, but weakly evidenced: absence of evidence in my searches is not proof.
  - **Role-based context.** Partly matched by Claude Code's scoped CLAUDE.md/rules/subagent memory and by Mem0's user/session/agent levels.
- The README's comparison table should add Beads, Claude Code auto-memory and CLAUDE.md, and a general issue tracker over MCP, and should date its claims. Otherwise a reviewer will see it as cherry-picked.
- The naming collision with MemOS (memory OS) and the general "agent OS" framing may blur positioning. "Structured work-state for agents" is the more defensible claim.

### Gaps
- I did not check CrewAI's 2026 memory and task features (CrewAI has native Task objects and Flows with dependencies at the orchestration layer, not as persistent memory; this is from training knowledge, not verified for 2026).
- I did not check Taskmaster AI, Linear's agent APIs or GitHub Copilot's coding-agent task features, any of which may overlap.
- Beads' current star count, licence and 1.0 feature list could not be fetched directly.

## 5. Business-model signals: raising, consolidating, dying; what customers pay for

### Takeaway
Funding in agent memory is modest and mostly happened in 2024–2025. Mem0's $24M is the largest disclosed round, Cognee's $7.5M is the only clearly dated 2026 round, and I found no 2026 rounds for Mem0, Zep or Letta. The business models I could see are usage-metered managed cloud (Zep credits per byte ingested), enterprise or hyperscaler distribution deals (Mem0–AWS), and pivots to end-user agent products (Letta Code). Open-source cores are being narrowed (Zep CE discontinued).

### Cited Findings
- Search results found no 2026 funding announcements for Mem0, Letta or Zep. Cognee (Feb 2026) was the only clearly dated 2026 round — [Atlan](https://atlan.com/know/best-ai-agent-memory-frameworks-2026/); [Cognee blog](https://www.cognee.ai/blog/cognee-news/cognee-raises-seven-million-five-hundred-thousand-dollars-seed)
- Mem0 $24M (Oct 2025); Letta $10M seed (2024); Zep about $500K pre-seed (2024); Supermemory about $3M seed (2025) — [TechCrunch](https://techcrunch.com/2025/10/28/mem0-raises-24m-from-yc-peak-xv-and-basis-set-to-build-the-memory-layer-for-ai-apps); [PR Newswire](https://prnewswire.com/news-releases/berkeley-ai-research-lab-spinout-letta-raises-10m-seed-financing-led-by-felicis-to-build-ai-with-memory-302257004.html); [Atlan](https://atlan.com/know/best-ai-agent-memory-frameworks-2026/); [StartupIntros](https://startupintros.com/news/supermemory-seed-2025-10)
- Zep monetises ingestion volume (credits per 350 bytes, free retrieval) and discontinued its self-hosted Community Edition — [Zep pricing](https://getzep.com/pricing); [Zep blog](https://blog.getzep.com/announcing-a-new-direction-for-zeps-open-source-strategy/)
- Mem0 distributes through the hyperscaler channel (exclusive Strands memory provider, Neptune and ElastiCache integrations) and advertises managed-platform-only benchmark gains, a sign that it gates its best algorithm behind the cloud — [Mem0 blog](https://mem0.ai/blog/aws-and-mem0-partner-to-bring-persistent-memory-to-next-gen-ai-agents-with-strands); [GitHub mem0ai/mem0](https://github.com/mem0ai/mem0)
- Letta moved from memory-server infrastructure to Letta Code as its flagship (Mar 2026) — [Latent Space wiki](https://latent.space/wiki/letta-ai-558a42a5/)
- Labs use memory as a free acquisition lever (Claude free memory plus import, framed as luring ChatGPT users) — [Bloomberg](https://www.bloomberg.com/news/articles/2026-03-03/anthropic-tries-to-win-users-from-chatgpt-with-memory-feature)

### Inferences
- Standalone "memory API" startups face pressure from the labs (free, built in) and from hyperscalers. The survivors are going vertical (coding agents), hyperscaler-embedded, or enterprise (compliance and on-prem, as with Memori and Cognee).
- Customers appear to pay for managed hosting at scale, recall quality on benchmarks, and enterprise deployment and compliance, not for the open-source library. For a solo/BSL project like FocalPoint, a hosted, team-shared work-state service (with GitHub/Notion sync and alerts) is a more plausible paid wedge than per-call memory pricing. That is speculative.
- I found no evidence of 2026 shutdowns or acquisitions among the named players. That should be read as "not found", not "none occurred".

### Gaps
- No revenue or ARR data for any memory startup (Sacra has a Mem0 page but it was not accessible).
- No confirmed M&A in agent memory in 2026, and no practitioner sentiment from Hacker News or Reddit was gathered in this pass.
