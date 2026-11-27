# Task / Working Memory and Context Engineering for AI Agents (as of 2026-11-27)

Method note: about 16 tool calls. Primary pages fetched this session: the Anthropic context-engineering and harness posts, the Beads, Spec Kit and Backlog.md GitHub pages, and the Cognition post (search snippet only). manus.im and venturebeat.com could not be reached from this sandbox because of network allowlist failures. GitHub API star lookups were blocked, so star counts come from fetched GitHub HTML pages where marked, and from secondary aggregators otherwise. Items taken from search snippets or secondary blogs are marked "(secondary)".

## 1. Context engineering as a discipline: canonical writings and patterns (2025-2026)

### Takeaway
Context engineering is now an established discipline with a stable set of patterns: curate a minimal set of tokens; write state outside the window (todo lists, NOTES.md, progress files, JSON feature lists, git); retrieve just in time; compact; and isolate work in sub-agents. The canonical texts all treat "structured external task state" as the fix for long-horizon work. That is FocalPoint's thesis, but in the field it is mostly done with plain files and git, not a dedicated task database.

### Cited Findings
- Anthropic, "Effective context engineering for AI agents" (2025-09-29, Applied AI team), defines context engineering as "the set of strategies for curating and maintaining the optimal set of tokens (information) during LLM inference" and calls it the evolution of prompt engineering — [Anthropic](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- Same post: "context rot". As context grows, "the model's ability to accurately recall information from that context decreases", so context is a finite resource with diminishing returns — [Anthropic](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- Same post lists the core techniques:
  - compaction: summarize near the limit, keeping decisions and unresolved bugs and dropping redundant tool output; clearing old tool results is the lightest form
  - structured note-taking: Claude Code to-do lists, a NOTES.md file, and Claude playing Pokémon keeping multi-hour progress tallies
  - sub-agents: work in clean windows and return condensed results of about 1,000-2,000 tokens
  - just-in-time retrieval: lightweight references such as paths and queries, with a hybrid of up-front CLAUDE.md loading plus glob/grep
  - the memory tool: file-based, public beta with Sonnet 4.5

  — [Anthropic](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- Anthropic, "Effective harnesses for long-running agents" (2025-11-26, Justin Young):
  - An initializer agent sets up `init.sh`, a progress log (claude-progress.txt) and an initial git commit.
  - Coding agents then make incremental progress. The key insight was "finding a way for agents to quickly understand the state of work when starting with a fresh context window."
  - A JSON feature list starts with every feature marked failing. Agents may only flip the `passes` field. JSON was chosen because the model is less likely to inappropriately change it than Markdown.
  - The agent works on one feature per session.
  - Each session starts with a routine: read the logs and progress notes, pick the highest-priority unfinished feature, and run an end-to-end test.
  - Failure modes it addresses: declaring victory early, leaving undocumented progress, and marking features done prematurely.

  — [Anthropic](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
- A follow-up Anthropic post, "Harness design for long-running apps", extends this to a multi-agent harness (planner/generator/evaluator). It was reported by InfoQ in April 2026 (secondary) — [Anthropic](https://www.anthropic.com/engineering/harness-design-long-running-apps); [InfoQ](https://www.infoq.com/news/2026/04/anthropic-three-agent-harness-ai)
- LangChain's framework, widely summarized as **write / select / compress / isolate**: write means keeping context outside the window (scratchpads, memories); select means retrieving only what is relevant now; compress means summarizing or trimming; isolate means giving sub-tasks clean windows (secondary summaries; original LangChain blog, June 2025, not fetched this session) — [LangChain blog](https://blog.langchain.com/context-engineering-for-agents/); [summary](https://baeseokjae.github.io/posts/context-engineering-ai-coding-agents-2026/)
- Manus, "Context Engineering for AI Agents: Lessons from Building Manus" (Yichao "Peak" Ji, July 2025). Its known lessons: design around KV-cache hit rate; mask tools rather than removing them; use the file system as unlimited context; "manipulate attention through recitation" by constantly rewriting a todo.md; keep errors in context; avoid few-shot ruts. **The page could not be fetched here (manus.im is not on the network allowlist), so these points are from prior knowledge, not re-verified** — [Manus](https://manus.im/blog/Context-Engineering-for-AI-Agents-Lessons-from-Building-Manus)
- Cognition, "Don't Build Multi-Agents" (Walden Yan, June 2025): principle 1 is "Share context". Principles 1 and 2 "are so critical, and so rarely worth violating, that you should by default rule out any agent architectures that don't abide by them". In its Flappy Bird example, sub-agents misread their subtasks — [Cognition](https://cognition.ai/blog/dont-build-multi-agents)
- Academic work in 2026 formalizes the area:
  - "Codified Context: Infrastructure for AI Agents in a Complex Codebase" (arXiv 2602.20478) — [arXiv](https://arxiv.org/pdf/2602.20478)
  - "Harness Engineering for Agentic AI Coding Tools" (arXiv 2602.14690) — [arXiv](https://arxiv.org/pdf/2602.14690)
  - work on context management for multimodal long-horizon agents (arXiv 2608.29897) — [arXiv](https://arxiv.org/pdf/2608.29897)
- Caveat on context files: a 2026 blog cites a 138-repo study finding that LLM-generated AGENTS.md files reduced task success and raised cost. Secondhand only, primary study not located — [metacto](https://www.metacto.com/blogs/context-rich-environments-ai-agents)
- Secondary sources report that the vocabulary has shifted from "context engineering" toward "harness engineering" in 2026 (for example, a Claude article on harness design patterns) — [Claude resources](https://claude.com/resources/articles/harnessing-claudes-intelligence)

### Inferences
- FocalPoint's pieces map almost one-to-one onto the canonical patterns:
  - The workbench is a "select" step that assembles goal, knowledge, context and subtasks, much like the harness's start-of-session routine.
  - The L0/L1/L2 budgets with trimming are "compress".
  - Narratives and knowledge docs are "write" or structured note-taking.
  - Role prompts and parallel review are "isolate".
- The thesis is mainstream, not contrarian. The difference is in implementation. Anthropic, Manus and practitioners deliberately use plain files (JSON, Markdown, git) because models already know how to read and edit them. A SQLite+MCP store has to justify itself with queryability: dependencies, "what's ready", staleness. Holding the data alone does not justify it.
- Anthropic chose JSON over Markdown so the model would not tamper with state. That supports a structured store with constrained mutations (only status changes), which FocalPoint can enforce through MCP tool schemas better than a file can.

### Gaps
- Could not re-verify the Manus post text this session (network block).
- Did not fetch the original LangChain post, or any 2026 AI Engineer World's Fair talks, on context engineering.

## 2. Agent-native task trackers, issue systems and spec-driven tools

### Takeaway
This is FocalPoint's most direct competitive set, and it is crowded and popular:
- Beads: about 29k stars; a dependency graph with `bd ready`; Dolt storage; compaction of old tasks.
- Claude Code's native Tasks: dependencies, a shared task list across sessions, files under ~/.claude/tasks.
- Task Master: PRD-to-tasks.json, MCP.
- Backlog.md: about 7.5k stars; Markdown plus MCP.
- Spec Kit: about 152k stars; spec, plan, tasks.

Most of them store state in git-friendly files or a versioned database. Dependency-aware "ready work" is already table stakes.

### Cited Findings
- **Beads** (Steve Yegge; repo now at gastownhall/beads). About 29.1k stars and 2.0k forks on the GitHub page fetched 2026-11-27.
  - It is described as a "distributed graph issue tracker for AI agents".
  - Storage is **Dolt** (version-controlled SQL), embedded by default or as a `dolt sql-server` in server mode. `.beads/issues.jsonl` is an export only.
  - It has a dependency graph with `bd ready` (unblocked work) and `bd update --claim`, hash IDs to avoid merge collisions across agents, **compaction that summarizes old closed tasks to reduce context**, JSON output, threaded messaging, and links such as `relates-to` and `duplicates`. There is also a `beads-mcp` package.

  — [GitHub](https://github.com/steveyegge/beads)
- Beads growth: 707 stars around October 2025 (aggregator) — [ecosyste.ms](https://awesome.ecosyste.ms/projects/github.com%2Fsteveyegge%2Fbeads); Yegge said it passed 20k stars "a bit over 5 months since launch" (roughly March 2026) — [Yegge/Medium](https://steve-yegge.medium.com/gas-town-from-clown-show-to-v1-0-c239d9a407ec). Yegge framed it as "a coding agent memory system" — [Medium](https://steve-yegge.medium.com/introducing-beads-a-coding-agent-memory-system-637d7d92514a). It is listed on the Thoughtworks Tech Radar as a git-backed issue tracker that coding agents share to coordinate — [Thoughtworks](https://www.thoughtworks.com/en-es/radar/tools/beads). It is maintained by Gas City, the company behind the Gas Town multi-agent orchestrator (secondary).
- **Claude Code Tasks** (early 2026). Todos were turned into "Tasks", which support dependencies (B waits on A) and cross-session collaboration. `CLAUDE_CODE_TASK_LIST_ID` lets multiple Claude instances, `claude -p` runs and the Agent SDK share one list stored under `~/.claude/tasks/<id>/`. Without the variable, tasks are session-scoped. Version v2.1.16+ is unverified. All of this is secondary; VentureBeat could not be fetched — [VentureBeat](https://venturebeat.com/orchestration/claude-codes-tasks-update-lets-agents-work-longer-and-coordinate-across); [Dale Seo](https://daleseo.com/claude-code-tasks/); [Ultimate guide](https://cc.bruniaux.com/guide/workflows/task-management/)
- **Task Master AI** (eyaltoledano/claude-task-master) parses a PRD into `tasks.json` with dependencies, priorities and test strategies, and ships an MCP server. One aggregator snapshot of unknown date shows 8,367 stars and 863 forks; the live count is likely higher — [README](https://unpkg.com/task-master-ai@0.20.0/README.md); [ecosyste.ms](https://awesome.ecosyste.ms/projects/github.com%2Feyaltoledano%2Fclaude-task-master)
- **Backlog.md** (MrLesk). About 7.5k stars and 506 forks on the page fetched 2026-11-27.
  - Tasks are Markdown files with YAML frontmatter in `backlog/`. Git is optional.
  - It has an MCP server (`backlog mcp start`) with setup guides for Claude Code, Codex, Gemini CLI and Kiro.
  - It supports milestones and task dependencies, acceptance criteria and Definition of Done checklists, a terminal Kanban board and web UI, fuzzy search across tasks, docs and decisions, JSON output, and no telemetry.

  — [GitHub](https://github.com/MrLesk/Backlog.md)
- **GitHub Spec Kit**. About 151.9k stars and 13.7k forks on the page fetched 2026-11-27.
  - Workflow: `/speckit-constitution`, `/speckit-specify`, `/speckit-plan`, `/speckit-tasks`, `/speckit-implement`, then `/speckit-converge`.
  - State lives in a `.specify/` folder: memory, bugs and assessments.
  - A secondary blog says it installs into more than 40 agents.

  — [GitHub](https://github.com/github/spec-kit); [Augment](https://www.augmentcode.com/tools/best-spec-driven-development-tools)
- **Kiro** (AWS IDE) has built-in specs: requirements.md, design.md and tasks.md. **BMAD-METHOD** is a phase-based method with persona agents (PM, architect, UX, dev, QA). OpenSpec is another spec-driven development framework. All three come from secondary comparisons; star counts were not obtained — [Augment](https://www.augmentcode.com/tools/best-spec-driven-development-tools); [aicodingpatterns](https://aicodingpatterns.com/en/patterns/openspec-vs-spec-kit-vs-bmad/)

### Inferences
- FocalPoint's task DAG (status lifecycle, dependencies, projects) overlaps heavily with Beads and Claude Code Tasks. Beads also already does "ready work" queries and "compaction of closed tasks" (a memory-decay analogue). Claude Code Tasks gives a free, native dependency-aware list to every Claude Code user. A pure "task DAG over MCP" is therefore close to redundant for coding agents.
- BMAD's persona agents cover FocalPoint's role prompts (strategy/review/execution). Spec Kit and Kiro cover its "goal + plan + tasks" decomposition.
- Possible differentiators:
  1. **Inheritable knowledge docs** that flow from project to task. Beads, Tasks and Backlog.md do not appear to have this, although Spec Kit's constitution and memory folder is a project-level analogue.
  2. **An explicit context-budget assembler.** None of these trackers budgets tokens when it hands context to the agent.
  3. **Proactive staleness alerts.**
  4. **Non-coding / general knowledge work.** Every competitor here is coding-centric and repo-scoped.
- The storage choice matters for adoption. The winners are git-friendly (Markdown/JSONL, or Dolt so branches merge). A plain SQLite file does not merge across branches or machines, which is the exact problem Beads moved to Dolt to solve.

### Gaps
- No verified current star counts for Task Master, BMAD-METHOD, OpenSpec or Kiro adoption.
- No official Anthropic documentation fetched for Claude Code Tasks; the date and version are unverified.
- Linear/Jira/GitHub agent integrations (Linear's agent API, GitHub Copilot coding agent assigned from issues) were not researched within the call budget.

## 3. Long-running and autonomous agent frameworks: how they keep state across sessions

### Takeaway
The dominant cross-session state mechanism is still **git plus repo files** (progress logs, feature lists, AGENTS.md/CLAUDE.md, spec folders), together with platform-level compaction and memory tools. Vendors are adding native primitives (Claude Tasks, the memory tool, cloud scheduled tasks). Little evidence was found of vendors adopting an external task database.

### Cited Findings
- The Anthropic harness persists state across context windows using git commits, a progress file, a JSON feature list and `init.sh`. Its open questions are single versus multi-agent setups, and generalization beyond web development — [Anthropic](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
- The Claude Developer Platform memory tool is file-based storage outside the context window, for knowledge bases and project state across sessions (beta, 2025-09) — [Anthropic](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- OpenAI Codex has cloud tasks and scheduled tasks (formerly "automations"). In February 2026, OpenAI published a long-running experiment in which GPT-5.3-Codex built a design tool from an empty codebase. All secondary; no documented maximum runtime found — [Wikipedia](https://en.wikipedia.org/wiki/Codex_(AI_agent)); [enjoy.dev](https://enjoy.dev/blog/codex-on-a-schedule/)
- Cognition/Devin emphasizes shared context and a single-threaded linear agent with compression of history (2025 position) — [Cognition](https://cognition.ai/blog/dont-build-multi-agents)

### Inferences
- Each platform owns its own session state: Claude Tasks, Codex cloud tasks, Devin sessions. A cross-tool, vendor-neutral store for "what needs to be done" that persists across Claude, Codex and Cursor is an open slot. Beads and Backlog.md also target it, and they do so by being repo-local files, not a separate service.

### Gaps
- Did not research the session-state internals of Devin, Cursor background agents, Google Jules or OpenHands in 2026 (call budget). The report writer should treat this section as thin.

## 4. Demand evidence for proactive, heartbeat-style agents and scheduled check-ins

### Takeaway
Demand for scheduled agents is real and productized by every major vendor: Claude Code routines / cloud scheduled tasks, Codex scheduled tasks, Cursor automations, and OpenClaw heartbeats. OpenAI's inferred proactive feed (ChatGPT Pulse) was reportedly retired and folded into user-configured scheduled tasks. The market signal is "user-defined schedules and checklists" rather than "the agent decides what to tell you".

### Cited Findings
- OpenClaw heartbeat: by default, the Gateway runs an agent turn every 30 minutes. The agent reads a short HEARTBEAT.md checklist and either replies `HEARTBEAT_OK` (a silent ack) or flags an issue. The docs advise keeping the file tiny because it is injected on every tick. An empty file skips the model calls. Newer docs say cadence is owned by the Automations scheduler and proactive behavior is opt-in — [OpenClaw docs](https://docs.openclaw.ai/gateway/heartbeat); [DEV](https://dev.to/hex_agent/openclaw-heartbeats-making-your-ai-agent-proactive-instead-of-reactive-2pi5)
- Claude Code routines launched 2026-04-14 as a research preview and were later renamed "cloud scheduled tasks". They can be triggered by a schedule, the API or GitHub events, run with the laptop closed, and have a 1-hour minimum interval (secondary) — [aicatchup](https://aicatchup.com/comparisons/claude-code-routines-vs-cursor-automations); [Claude Code handbook, arXiv 2608.26742](https://arxiv.org/html/2608.26742)
- ChatGPT Pulse launched 2025-09-25, Pro-only and mobile-first. It was **reportedly retired in June 2026**, with its functionality moved into scheduled tasks and web monitoring. The single secondary source found is unconfirmed by OpenAI in the results — [prowlo](https://prowlo.com/blog/chatgpt-pulse-shut-down)

### Inferences
- FocalPoint's heartbeat alerts ("task X blocked for 3 days") are structurally better grounded than OpenClaw's free-text checklist, because they are computed from task state. They are cheap, deterministic and need no LLM call per tick. This is a genuine differentiator, provided it plugs into the schedulers users already have (Claude/Codex scheduled tasks, cron) instead of requiring its own daemon.
- If the Pulse retirement report is accurate, it is a warning against unsolicited LLM-generated proactivity. Rule-based, user-scoped alerts are the safer design.

### Gaps
- No usage numbers for any heartbeat or scheduled-task feature were found.
- The Pulse shutdown needs confirmation from an official OpenAI source.

## 5. Multi-agent review and debate for decisions: does it work?

### Takeaway
The research evidence is skeptical. Homogeneous multi-agent debate usually fails to beat self-consistency or majority voting at matched compute, and the gains mostly come from extra sampling. Multi-agent setups work for breadth-first, parallelizable tasks such as research, at a large token cost, and are risky for tightly coupled decisions. Heterogeneous models and separate evaluator agents are where evidence of benefit is strongest.

### Cited Findings
- Huang et al. (ICLR 2024, arXiv 2310.01798), section 4, "Multi-agent debate does not outperform self-consistency": at an equal number of responses, debate significantly underperforms majority voting — [arXiv](https://arxiv.org/pdf/2310.01798)
- Smit et al., "Should we be going MAD?" (ICML 2024): multi-agent debate does not reliably outperform self-consistency or ensembling, though tuned variants and agreement-level adjustments can help — [arXiv](https://arxiv.org/pdf/2311.17371)
- Zhang et al., "Stop Overvaluing Multi-Agent Debate" (arXiv 2502.08788, 2025): across five debate methods and nine benchmarks, debate often fails to beat chain-of-thought or self-consistency even with more compute. **Model heterogeneity improves debate** — [arXiv](https://arxiv.org/abs/2502.08788)
- Anthropic's multi-agent research system (Opus 4 lead with Sonnet 4 sub-agents) is reported to have beaten single-agent Opus 4 by 90.2% on an internal research eval, while using about 15x the tokens of chat; token usage explained about 80% of the performance variance. These figures are secondary and were not verified against the primary post — [LangChain](https://blog.langchain.com/how-and-when-to-build-multi-agent-systems); [Claude blog](https://www.claude.com/blog/building-multi-agent-systems-when-and-how-to-use-them)
- Cognition argues that parallel agents make conflicting implicit decisions without shared context — [Cognition](https://cognition.ai/blog/dont-build-multi-agents)
- Anthropic's 2026 harness work uses a separate evaluator agent, a generator/evaluator split (secondary) — [InfoQ](https://www.infoq.com/news/2026/04/anthropic-three-agent-harness-ai)

### Inferences
- FocalPoint's "parallel multi-role review before major decisions" is likely to help mainly as structured self-consistency and checklist coverage, not through debate dynamics. Its value improves if the reviewers get (a) different information or views (risk, cost, user) rather than the same prompt with a different hat, (b) different models where possible, and (c) the full shared context, per Cognition. It should be positioned as a cheap, optional gate on high-stakes decisions, not a default.

### Gaps
- No rigorous evaluation found of "role-based review panels" specifically for planning or strategy decisions, as opposed to math or QA benchmarks.

## 6. Overlap, redundancy and unmet needs for FocalPoint

### Takeaway
FocalPoint's bet that task state is different from conversational memory is validated by the whole field. That is why Beads, Claude Tasks, Spec Kit and the Anthropic harness exist. The same fact makes its core DAG commoditized. Its defensible space is narrower: context assembly with budgets across tools, inheritable project knowledge, state-derived proactive alerts, and non-coding long-horizon work, all delivered in a git-friendly, vendor-neutral way.

### Cited Findings
- Dependency-aware ready-work queries plus compaction of old tasks: already in Beads (about 29k stars) — [GitHub](https://github.com/steveyegge/beads)
- A native cross-session task list with dependencies inside Claude Code — [Dale Seo](https://daleseo.com/claude-code-tasks/)
- A spec, plan and tasks decomposition with a project "constitution": Spec Kit (about 152k stars) — [GitHub](https://github.com/github/spec-kit)
- Markdown tasks, docs, decisions and MCP: Backlog.md (about 7.5k stars) — [GitHub](https://github.com/MrLesk/Backlog.md)
- A start-of-session state rehydration routine as a harness pattern — [Anthropic](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
- Scheduled and heartbeat check-ins productized by vendors — [OpenClaw](https://docs.openclaw.ai/gateway/heartbeat); [aicatchup](https://aicatchup.com/comparisons/claude-code-routines-vs-cursor-automations)

### Inferences
- **Redundant or commoditized:**
  - basic task CRUD, status lifecycle and dependencies (Beads, Claude Tasks, Backlog.md, Task Master)
  - role personas (BMAD)
  - spec-to-tasks decomposition (Spec Kit, Kiro, Task Master)
  - generic scheduled check-ins (vendor scheduled tasks)
- **Overlapping but differentiable:**
  - The workbench is like the harness's "get your bearings" routine, but automated and token-budgeted. No surveyed tracker assembles budgeted context.
  - Narratives and knowledge docs are like NOTES.md or Spec Kit memory, but scoped and inheritable along the DAG.
- **Unmet needs and opportunities:**
  1. A vendor-neutral task and working-memory layer that travels across Claude Code, Codex, Cursor and other tools. Every native solution is siloed.
  2. Context budgeting as a first-class, measurable feature (L0/L1/L2) that addresses context rot.
  3. Deterministic, state-derived staleness and blocked alerts that feed existing schedulers.
  4. Non-coding, multi-week knowledge work (strategy, research, operations), where repo-centric tools do not fit.
  5. Decision records linked to tasks.
- **Risks:**
  - SQLite does not merge across branches or machines (Beads moved to Dolt). Consider a JSONL/Markdown export or git sync.
  - Platform absorption: Anthropic already shipped Tasks and the memory tool.
  - The research does not support debate-style review as a quality lever unless reviewers differ in model or information.

### Gaps
- No data on how many users run more than one coding agent; this would validate the cross-tool need.
- No head-to-head benchmark of structured task stores versus plain files for long-horizon agent success.
