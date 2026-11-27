# FocalPoint (jeff0052/focalpoint): current state from its own repository

Scope and method: everything below comes from the repository and its git history only. I ran `git fetch origin main` on 2026-11-27. `origin/main` is at commit `83553df` (2026-05-10), and the local checkout (branch `claude/review-project-intro-6cw7w`) has the same HEAD, so there is no difference between local and remote main. Line references are to that commit. Base URL for citations: `https://github.com/jeff0052/focalpoint/blob/83553df/`, shortened below to `R/`. Nothing in the repo was modified. The test run used a scratch venv outside the repo. One test side effect created `/nonexistent/path` outside the repo, and I removed it afterwards.

## 1. Vision and stated differentiation (top-level docs and the numbered folders)

### Takeaway
FocalPoint presents itself as an "AI cognitive operating system: memory + attention management + workflow orchestration". It is the first component of a larger "FounderOS" vision for a one-founder company run by agent "Offices". The claimed difference from Mem0, Zep and Letta is that FocalPoint manages *work state* (a task DAG, status lifecycle, deadlines and risk alerts) rather than storing conversation snippets. It deliberately uses no vector database.

### Cited Findings
- **Tagline.** "Memory + Attention Management + Workflow Orchestration… remembering what was said is not the same as managing what needs to be done." [R/README.md](R/README.md)
- **Seven capabilities.** The README lists REMEMBER, PREPARE (workbench), THINK (3 roles), REVIEW (Three-Province), ALERT (heartbeat), SEARCH (FTS5) and SYNC (GitHub/Notion). [R/README.md](R/README.md)
- **Self-authored comparison table.** It compares against Mem0, Zep, Letta, CrewAI and Claude. It claims FocalPoint alone has task lifecycle, DAG, proactive alerts, knowledge inheritance, role-based context and decision review. Search is listed as "FTS5" against the competitors' "Vector". It claims "Zero external deps (SQLite only)". [R/README.md](R/README.md)
- **The ADR.** It states the positioning: "FocalPoint ≠ 记忆工具（那是 Mem0） ≠ 项目管理（Notion/Linear） ≠ prompt chain（LangChain）". It defines three layers: memory engine (done), cognitive layer (Work Mode), and collaboration layer (multi-agent, future). It says: "没有任何现有产品覆盖这个完整定位" ("no existing product covers this full positioning"). [R/1-vision/ADR-product-direction.md](R/1-vision/ADR-product-direction.md)
- **OVERVIEW.md.** It frames FocalPoint/FPMS inside FounderOS, with the core loop "State + Signal → Decision → Action → New State". The founder gives judgment, the agents ("Offices": CTO, Ops, Capital, Compliance, Risk, Growth) execute. Only the CTO Office has a PRD, marked "待搭建" (not yet built). The rest are "未来" (future). [R/OVERVIEW.md](R/OVERVIEW.md)
- **Five-layer company memory model.** OVERVIEW.md defines Constitution / Fact / Judgment / Office Memory / Narrative / Temporary. It admits that FPMS covers only Layer 2 (task state + narrative) and part of Layer 4 (knowledge docs). It points to `fpms/docs/FounderOS-Memory-Architecture-V1.md`, but that file is not in the repo (`git ls-files` shows no `fpms/docs/`). [R/OVERVIEW.md](R/OVERVIEW.md)
- **"External-first" baseline.** External tools (GitHub, Notion, Telegram) are the source of truth, and FounderOS is a cognitive and control layer. The baseline says not to build a PM system or a knowledge base, and that it overrides older docs where they conflict. [R/0-external-baseline/README.md](R/0-external-baseline/README.md)
- **Core design principle.** The LLM is the "Brain" and only issues tool calls. The "Spine" is a deterministic engine that does all validation and storage: "LLM never touches storage directly". [R/README.md](R/README.md); [R/OVERVIEW.md](R/OVERVIEW.md)
- **"Eyeball model" of context.** L0 dashboard → L1 neighborhood → L2 focus. [R/OVERVIEW.md](R/OVERVIEW.md); implemented in [R/fpms/spine/bundle.py#L1-L6](R/fpms/spine/bundle.py#L1-L6)
- **Requirements folder.** It holds deep, ambitious specs that go beyond the code:
  - Context lifecycle: load, entry check, mid-flight check, commit, exit report, compress; task decomposition as "context fractal". [R/2-requirements/PRD-context-lifecycle.md](R/2-requirements/PRD-context-lifecycle.md)
  - Narrative compression algorithm with LLM fallback. [R/2-requirements/PRD-compression-spec.md](R/2-requirements/PRD-compression-spec.md)
  - Telegram adapter and 15-minute polling sync. [R/2-requirements/PRD-cognitive-engine.md](R/2-requirements/PRD-cognitive-engine.md)
  - A CTO agent PRD. [R/2-requirements/PRD-cto-agent.md](R/2-requirements/PRD-cto-agent.md)
- **Architecture folder.** It has a current `ARCHITECTURE.md`, `INTERFACES.md` and a diagram, and keeps an `ARCHITECTURE-V3.1-outdated.md`. [R/3-architecture/](R/3-architecture/)
- **Token-economy note (2026-03-25).** It speculates about agent-to-agent micropayments with "Context/Memory infrastructure" as the AWS analogue. It describes FocalPoint as BSL licensed and "还没用户，谈不上收费" ("no users yet, so charging is moot"). [R/docs/thinking/2026-03-25-token-economy-for-ai-agents.md](R/docs/thinking/2026-03-25-token-economy-for-ai-agents.md)

### Inferences
- The differentiation is real but narrow. FocalPoint is closer to an agent-operated, MCP-exposed project and attention tracker with narrative logs than to a "memory" system in the Mem0/Zep/Letta sense. The competitor table compares across categories, and the project authored it itself.
- The documentation has far more scope than the code, especially the FounderOS, five-layer memory and multi-Office material. The docs also mix Chinese and English, and "FounderOS", "FPMS" and "FocalPoint" are used for the same thing.

### Gaps
- The referenced `FounderOS-Memory-Architecture-V1.md` is missing, so I could not verify the full five-layer design document.
- I did not read the WhitePaper, the GLOSSARY or every PRD line by line. I covered them through their headings and the sections cited.

## 2. What the `fpms` package actually implements (module → feature map)

### Takeaway
The code is a compact, deterministic, pure-Python/SQLite engine: about 6.1k lines in `fpms/`. It has a node graph, validation, narratives, knowledge docs, risk/heartbeat, focus scheduling, L0/L1/L2 bundle assembly with role budgets, FTS5 search and an MCP server with 24 tools. Several marquee features are thinner than the docs suggest:
- **"Three-Province review" is a verdict recorder.** The calling LLM supplies the verdicts. If it supplies none, they default to approved.
- **GitHub/Notion sync is not wired into the MCP server at all.**
- **Compression is spec-only.**

### Cited Findings
- **Package layout.** `fpms/mcp_server.py` (585 LOC), `fpms/analytics.py` (1158), `fpms/spine/*` (store 634, bundle 631, tools 852, `__init__` 453, dashboard 432, heartbeat 327, focus 315, adapters about 540, others). Running `wc -l` over `fpms/` and `tests/` gives 19,657 lines in total. [R/fpms/](R/fpms/)
- **Storage schema (SQLite in WAL mode, foreign keys on).** Tables: `nodes`, `edges`, `session_state`, `audit_outbox`, `recent_commands` (idempotency), `narrative_index`, plus the FTS5 virtual table `fts_index(node_id, title, narrative_text, knowledge_text, tokenize='unicode61')`. [R/fpms/spine/schema.py#L7-L97](R/fpms/spine/schema.py#L7-L97)
- **Node model.** `status ∈ {inbox, active, waiting, done, dropped}`. `node_type ∈ {goal, project, milestone, task, unknown}`. A CHECK enforces the XOR "is_root vs parent_id". There are external-source fields (`source`, `source_id`, `source_url`, `source_synced_at`, `source_deleted`), compression flags and JSON `tags`. Edges are `parent` or `depends_on`. [R/fpms/spine/schema.py#L8-L50](R/fpms/spine/schema.py#L8-L50)
- **Validation.** `validator.py` enforces the status state machine, DAG cycle prevention and the XOR rule. `command_executor.py` is a serial executor with idempotency. [R/4-implementation/ROADMAP.md](R/4-implementation/ROADMAP.md); [R/fpms/spine/validator.py](R/fpms/spine/validator.py)
- **Context assembly.** `BundleAssembler` builds the L0 dashboard, L_Alert, L1 neighborhood (depth 1 only; caps of 15 children, 10 dependencies, 10 dependents, 10 siblings) and L2 focus. L2 prefers `{node}.compressed.md` if one exists, and otherwise uses the raw narrative.
  - Role filtering maps narrative categories: strategy → {decision, feedback}, review → {risk, progress}, execution → {technical, progress}.
  - Role budgets are 8,000 tokens (10,000 for "all").
  - Tokens are estimated as `len(text)//4`.
  - Each assembly is logged to `assembly_traces.jsonl`.
  
  [R/fpms/spine/bundle.py#L24-L44](R/fpms/spine/bundle.py#L24-L44), [#L424-L430](R/fpms/spine/bundle.py#L424-L430), [#L594-L596](R/fpms/spine/bundle.py#L594-L596)
- **Heartbeat.** It raises five prioritized alert types: urgent_deadline (<24h), critical_blocked, deadline_warning (24–48h), stale (>72h with no status change) and inbox_stale (>7 days). There is 24h dedup and an "Anti-Amnesia" re-push if no substantive action happened. `append_log` does not count as a substantive action. Top 3 alerts are returned. [R/fpms/spine/heartbeat.py#L1-L56](R/fpms/spine/heartbeat.py#L1-L56)
  - `SpineEngine.heartbeat()` also **auto-archives** eligible nodes and swallows exceptions. [R/fpms/spine/__init__.py#L288-L300](R/fpms/spine/__init__.py#L288-L300)
- **Archive (forgetting by status).** A node is archived when it is terminal, more than 7 days past its status change, not persistent, and has no unarchived dependents or descendants. Archiving runs bottom-up, and `unarchive` exists. [R/fpms/spine/archive.py#L1-L30](R/fpms/spine/archive.py#L1-L30)
- **Focus scheduler.** One primary, at most 2 secondary (LRU eviction), and a LIFO stash of at most 2. Primary and secondary decay after 3 days without a touch. Stash entries are written to the narrative after 24h. [R/fpms/spine/focus.py#L1-L27](R/fpms/spine/focus.py#L1-L27)
- **Narratives.** Append-only Markdown per node. Categories are `{decision, feedback, risk, technical, progress, general}`. [R/fpms/spine/models.py#L12](R/fpms/spine/models.py#L12); [R/fpms/spine/narrative.py](R/fpms/spine/narrative.py)
- **Knowledge docs.** Markdown files under `knowledge/{node_id}/{doc_type}.md`. Inheritance walks the parent chain recursively, and the child's own docs override the parent's. `knowledge_dir` is derived as a sibling of `narratives_dir`. [R/fpms/spine/knowledge.py#L60-L88](R/fpms/spine/knowledge.py#L60-L88); [R/fpms/spine/__init__.py#L55](R/fpms/spine/__init__.py#L55)
- **Workbench.** `activate_workbench(node_id, role)` is a stateless aggregation. It returns the role-filtered bundle, inherited knowledge, subtasks in Kahn topological order, `suggested_next` (the first non-terminal subtask), decisions or risks extracted by category, and a role prompt loaded from `fpms/prompts/{strategy,review,execution}.md`. [R/fpms/spine/__init__.py#L146-L286](R/fpms/spine/__init__.py#L146-L286)
- **Three-Province review (sansei_review).** It takes a `proposal` plus optional `review_verdict` and `engineer_verdict` dicts.
  - **Missing verdicts default to approved.** The defaults are "No issues found" and "Feasible".
  - On rejection it increments a counter in `session_state` and writes narrative entries. On approval it logs a decision.
  - It sets `escalate_to_human` when `rejection_count > 3`.
  - The counter is never reset after an approval.
  - There is no parallel execution, and no separate agent or LLM call. The "parallel review" is whatever the calling LLM role-plays.
  
  [R/fpms/spine/__init__.py#L353-L422](R/fpms/spine/__init__.py#L353-L422)
- **FTS5 search.** Search indexes incrementally before each query and uses OR semantics across tokens. It falls back to LIKE on FTS syntax errors, and also on empty results for non-ASCII (CJK) queries, because the `unicode61` tokenizer cannot segment CJK text. [R/fpms/spine/store.py#L473-L509](R/fpms/spine/store.py#L473-L509)
- **GitHub and Notion adapters.**
  - `GitHubAdapter` covers sync_node, list_updates, write_status (PATCH), write_comment (POST) and status mapping, using a bearer token. [R/fpms/spine/adapters/github_adapter.py](R/fpms/spine/adapters/github_adapter.py)
  - `NotionAdapter` is analogous. [R/fpms/spine/adapters/notion_adapter.py](R/fpms/spine/adapters/notion_adapter.py)
  - **No production code instantiates the adapters or calls `set_adapter_registry`.** A grep outside `tests/` finds only the definitions. The MCP server builds `SpineEngine` from three env paths only, so `_adapter_registry` stays `None` and `sync_all()` returns 0. [R/fpms/mcp_server.py#L35-L44](R/fpms/mcp_server.py#L35-L44); [R/fpms/spine/__init__.py#L89-L112](R/fpms/spine/__init__.py#L89-L112), [#L424-L428](R/fpms/spine/__init__.py#L424-L428)
- **MCP server.** It uses FastMCP and registers **24** `@mcp.tool()` functions:
  - `create_node`, `update_status`, `update_field`, `attach_node`, `detach_node`, `add_dependency`, `remove_dependency`, `append_log`, `unarchive`, `set_persistent`
  - `shift_focus`, `expand_context`, `get_node`, `search_nodes`, `get_assembly_trace`, `heartbeat`, `bootstrap`, `get_context_bundle`, `activate_workbench`
  - `set_knowledge`, `get_knowledge`, `delete_knowledge`, `sansei_review`, `get_stats`

  The server instructions embed a "Work Mode Protocol" in Chinese, mapping strategy, review and execution to the 中书省, 门下省 and 尚书省 ("Three Provinces"). The README still says "23 tools", while the OVERVIEW and CHANGELOG say 24. [R/fpms/mcp_server.py#L48-L75](R/fpms/mcp_server.py#L48-L75), [#L101-L562](R/fpms/mcp_server.py#L101-L562); [R/README.md](R/README.md)
- **Default paths are relative to the current working directory.** The DB defaults to `fpms/db/fpms.db`, events to `fpms/events.jsonl` and narratives to `fpms/narratives`, overridable through `FPMS_*` env vars. Where data lands therefore depends on the directory the MCP client launches from. [R/fpms/mcp_server.py#L39-L43](R/fpms/mcp_server.py#L39-L43)
- **Stats dashboard.** `fpms/analytics.py` reads events, assembly traces and the DB. It computes a 0–100 health score over 5 dimensions and emits a self-contained HTML dashboard with a node browser, plus a CLI `focalpoint-stats` and the MCP tool `get_stats`. [R/fpms/analytics.py](R/fpms/analytics.py); [R/pyproject.toml](R/pyproject.toml)
- **Compression is not implemented.** `write_compressed()` is defined but no production code calls it. `needs_compression` and `compression_in_progress` are only persisted, never set by logic. The roadmap marks the "压缩引擎" (compression engine) as deferred "until node count grows". [R/fpms/spine/narrative.py#L147-L160](R/fpms/spine/narrative.py#L147-L160); [R/4-implementation/ROADMAP.md](R/4-implementation/ROADMAP.md)
- **SYSTEM-CONFIG.md is documentation only.** Code modules hard-code the constants and mention SYSTEM-CONFIG in comments; nothing parses the file. Its budgets (100k–1M tokens per task type) differ from the 8k/10k role budgets in code. [R/SYSTEM-CONFIG.md](R/SYSTEM-CONFIG.md); [R/fpms/spine/bundle.py#L39-L44](R/fpms/spine/bundle.py#L39-L44)
- **No LLM calls.** The package has no LLM client and no Anthropic or OpenAI dependency. Its dependencies are `pydantic`, `mcp[cli]>=1.2.0` and `httpx`. [R/pyproject.toml](R/pyproject.toml)

### Inferences
- **Documented vs. implemented:**
  - **Implemented:** storage, validation, narratives, knowledge with inheritance, heartbeat/alerts, focus, bundle L0/L1/L2 with role filtering and budgets, archive, FTS5, MCP server and the analytics dashboard.
  - **Implemented but only usable from Python:** the GitHub and Notion adapters and write-back.
  - **Recorder only, not an orchestrator:** the Three-Province review.
  - **Spec only:** narrative compression, context entry/mid-flight checks, the Telegram adapter, scheduled sync, configurable status mapping, the Interview flow, automatic recording, multi-agent and the CTO agent.
- The "PREPARE/THINK/REVIEW" value depends entirely on the host LLM following the prompt protocol. The engine itself makes no decisions.

### Gaps
- I did not exercise the adapters against live GitHub or Notion APIs. The roadmap claims "真实 API 验证 ✅" (validated against the real APIs), but I have no log of that run. [R/4-implementation/ROADMAP.md](R/4-implementation/ROADMAP.md)

## 3. Memory representation and advanced memory capabilities

### Takeaway
Memory is structured records plus files:
- **Records:** SQLite rows for the node graph, focus and session state.
- **Files:** append-only Markdown narratives per node, with categories, and Markdown knowledge docs per node, inherited down the tree.

Retrieval is structural (graph neighborhood, focus, role or category filter) plus lexical FTS5. There are no embeddings or semantic retrieval, no consolidation or compression, and no LLM-based extraction. "Forgetting" means status-based archiving and focus decay. There is no conflict resolution beyond "the external tool is authoritative for synced fields", and no real multi-user or multi-agent support.

### Cited Findings
- **Narratives.** Stored as `narratives/{node_id}.md` (append-only), with a `narrative_index` table holding offsets, timestamps, event types and mentions. [R/fpms/spine/schema.py#L72-L79](R/fpms/spine/schema.py#L72-L79); [R/2-requirements/PRD-compression-spec.md](R/2-requirements/PRD-compression-spec.md)
- **FTS5 is the only search.** The README positions FTS5 against competitors' vectors, and says "No vector DB, no Redis, no PostgreSQL". [R/README.md](R/README.md); [R/fpms/spine/schema.py#L81-L87](R/fpms/spine/schema.py#L81-L87)
- **Conflict policy (spec).** External data wins for synced fields, and the cognitive layer owns why, depends_on and narrative. "两者不会冲突，因为管的字段不重叠" ("the two never conflict because the fields they manage do not overlap"). External deletes set `source_deleted`. In code, `sync_all` simply overwrites title, status and owner from the snapshot. [R/2-requirements/PRD-cognitive-engine.md](R/2-requirements/PRD-cognitive-engine.md) (§6.3); [R/fpms/spine/__init__.py#L424-L453](R/fpms/spine/__init__.py#L424-L453)
- **Forgetting and consolidation.**
  - Archive rules (7-day cooldown) and focus decay (3 days) are the only aging mechanisms. [R/fpms/spine/archive.py#L1-L30](R/fpms/spine/archive.py#L1-L30); [R/fpms/spine/focus.py#L1-L27](R/fpms/spine/focus.py#L1-L27)
  - Narrative compression to ≤10% (`compress.target_ratio = 0.1`) is specified but not implemented. [R/SYSTEM-CONFIG.md](R/SYSTEM-CONFIG.md); [R/fpms/spine/narrative.py#L156](R/fpms/spine/narrative.py#L156)
- **Multi-user and multi-agent.**
  - The only multi-agent artifacts are role prompts and the single `owner` text field. [R/fpms/spine/schema.py#L19](R/fpms/spine/schema.py#L19)
  - Multi-agent collaboration and cross-agent shared memory are "v0.6+ 未来" (future). [R/OVERVIEW.md](R/OVERVIEW.md); [R/1-vision/ADR-product-direction.md](R/1-vision/ADR-product-direction.md)
  - The MCP engine is a per-process singleton, and its comment notes "Thread-safe not required for stdio". [R/fpms/mcp_server.py#L35-L37](R/fpms/mcp_server.py#L35-L37)
- **Planned automatic recording.** "AI 自动记录（参考 OpenViking）" (automatic recording modelled on OpenViking) is planned for v0.5. The token-efficiency target cites OpenViking's 80% token reduction. The current injection is "~8,000–10,000" tokens against a target of 4,000, and context utilization is "未量化" (not yet measured). [R/OVERVIEW.md](R/OVERVIEW.md); [R/SYSTEM-CONFIG.md](R/SYSTEM-CONFIG.md) (§10)

### Inferences
- The memory model is a work-item graph with logs and docs. It is closer to "structured state plus narrative", in the style of a PM tool or ADR log, than to episodic or semantic memory. Writing memory depends on the agent calling tools explicitly, and the project's own analytics show agents rarely do so properly (see §5).
- The lack of semantic retrieval and of LLM-driven consolidation is the main technical distance from the 2025–2026 memory systems that other researchers cover.

### Gaps
- There is no evaluation of retrieval quality anywhere in the repo, so I cannot compare FTS5 recall against vector retrieval from repo evidence.

## 4. Test suite, code quality signals, benchmarks

### Takeaway
There are 690 tests. With `mcp<2` installed they run in about 9s, with **688 passed and 2 failed**. Both failures are environment artifacts of running as root. With the latest `mcp` (2.x) the MCP server does not even import, because `pyproject.toml` declares `mcp[cli]>=1.2.0` without an upper bound. There are no benchmarks or evals of memory quality. The only "eval" is the self-scoring health dashboard.

### Cited Findings
- **Test layout.** 31 files in `tests/`, including 7 "invariant" suites (archive hot zone, atomic commit, DAG, derived isolation, idempotency, status machine, XOR) and several end-to-end files. [R/tests/](R/tests/)
- **Run with a fresh install (the resolver picked mcp 2.x).** 680 passed, 4 failed, 6 errors in 10.8s. The `mcp_server` tests fail with `ModuleNotFoundError: No module named 'mcp.server.fastmcp'`, because mcp 2.x renamed FastMCP to MCPServer. A fresh `pip install focalpoint` would therefore produce a server that crashes on start. [R/fpms/mcp_server.py#L23](R/fpms/mcp_server.py#L23); [R/pyproject.toml](R/pyproject.toml)
- **Run with `mcp==1.30.0` pinned.** 688 passed, 2 failed in 8.9s.
  - `test_invariant_atomic_commit::test_narrative_failure_preserves_facts` expects writing to `/nonexistent/path` to fail. As root, `makedirs` succeeds. [R/tests/invariants/test_invariant_atomic_commit.py#L126-L153](R/tests/invariants/test_invariant_atomic_commit.py#L126-L153)
  - `test_analytics::TestKnowledgeStats::test_missing_dir` then sees the directory the previous test created. [R/tests/test_analytics.py#L225-L227](R/tests/test_analytics.py#L225-L227)
  - Both pass as non-root. This is consistent with the "690 测试全绿" (690 tests all green) claim. [R/OVERVIEW.md](R/OVERVIEW.md)
- **Process signals.**
  - The project documents a TDD process ("铁律测试永不修改", invariant tests are never modified), docs written before code, and a `4-implementation/CLAUDE.md` guiding AI coding sessions. [R/OVERVIEW.md](R/OVERVIEW.md); [R/4-implementation/CLAUDE.md](R/4-implementation/CLAUDE.md)
  - 35 of 51 commits carry `Co-Authored-By: Claude`. (git log)
- **Weaker spots.**
  - Several broad `except Exception: pass` blocks: auto-archive in heartbeat and the per-node loop in `sync_all`. [R/fpms/spine/__init__.py#L295-L298](R/fpms/spine/__init__.py#L295-L298), [#L449-L451](R/fpms/spine/__init__.py#L449-L451)
  - Token estimates use `len//4`, which is crude for CJK text even though the project's own config says Chinese text costs about 2 tokens per character. [R/fpms/spine/bundle.py#L594-L596](R/fpms/spine/bundle.py#L594-L596); [R/SYSTEM-CONFIG.md](R/SYSTEM-CONFIG.md)
  - The tool count is stale in the README (23 vs. 24).
  - Packaging metadata says "License :: Other/Proprietary License" and points Homepage/Repository to the old repo name `jeff0052/founderOSclaudecode`. [R/pyproject.toml](R/pyproject.toml)
- **Benchmarks and evals.** None exist in the repo, such as LoCoMo, LongMemEval or any token-savings measurement. Token efficiency targets are listed as "待真实项目数据验证" (awaiting real project data). [R/SYSTEM-CONFIG.md](R/SYSTEM-CONFIG.md)

### Inferences
- The engine code is well tested for its deterministic invariants. The product-level claims ("AI works like an experienced team", token savings) are untested.
- The unpinned `mcp` dependency is a concrete, high-priority fix if the project continues: either pin `mcp<2` or migrate to `MCPServer`.

### Gaps
- PyPI was not checked, because the scope was the repo only. I cannot confirm whether the published 0.4.0 wheel has the same dependency spec or how many downloads it has.

## 5. Activity and traction signals

### Takeaway
Product development was a burst of roughly four days, 2026-03-22 to 2026-03-25 in this repo's history. Earlier milestones v0, v1 and M1 were done in a predecessor repo, `founderOSclaudecode`. Every commit after 2026-03-25 is unrelated to FocalPoint. There is a single author and no tags. The only traction evidence is the author's own usage, 48 nodes with a health score of 64/100, which showed that advanced tools went unused. The project's own notes say it has no users.

### Cited Findings
- **Commit history.** 51 commits on `origin/main`, all by "Jeff". By month: 47 in 2026-03, 1 in 2026-04, 3 in 2026-05.
  - The first commit, `8bb0ec3` (2026-03-22, "M2 Notion integration"), imports the whole codebase and docs at once.
  - The last FocalPoint-related commit is `02bf202` (2026-03-25, the token-economy note).
  - Later commits are a business-proposal HTML that was added and then reverted, personal health notes in `memory/`, and an essay page that was added and then removed. These are not product work.
  
  (git log origin/main)
- **No git tags.** Versions exist only in commits and the CHANGELOG:
  - 0.3.0 (2026-03-22) → 0.3.1 (FTS auto-index, delete_knowledge) → 0.3.2 (PyPI and ClawHub versions unified) → 0.3.3 and 0.3.4 (shift_focus fix, type hints) → 0.4.0 (2026-03-24, analytics).
  - `pyproject.toml` has version `0.4.0` and "Development Status :: 4 - Beta".
  
  [R/CHANGELOG.md](R/CHANGELOG.md); [R/pyproject.toml](R/pyproject.toml)
- **Distribution.** The project says it is published on PyPI (`pip install focalpoint`) and on ClawHub/OpenClaw as `focalpoint-memory` 0.4.0. `clawhub-skill/focalpoint-memory/SKILL.md` is the OpenClaw skill manifest (it installs via `uv` and needs `python3`) plus English marketing copy and a competitor table. [R/clawhub-skill/focalpoint-memory/SKILL.md](R/clawhub-skill/focalpoint-memory/SKILL.md); [R/README.md](R/README.md); [R/CHANGELOG.md](R/CHANGELOG.md)
- **Dogfooding data.** On 48 real nodes the health score was 64/100.
  - "Recording Habits 4/10 — 89% logs 没有 category" (89% of logs have no category).
  - "Tool Utilization 2/10 — workbench/三省/搜索从未使用" (workbench, Three-Province review and search were never used).
  - The author's conclusion: "AI 不主动给 log 打 category" (the AI doesn't categorize logs on its own) and "高级工具没人用" (nobody uses the advanced tools).
  
  [R/docs/milestones/2026-03-24-v040-analytics-and-research.md](R/docs/milestones/2026-03-24-v040-analytics-and-research.md)
- **Self-assessed traction.** "FocalPoint | BSL | 还没用户，谈不上收费" (no users yet, so charging is moot). [R/docs/thinking/2026-03-25-token-economy-for-ai-agents.md](R/docs/thinking/2026-03-25-token-economy-for-ai-agents.md)
- **No issue or PR references, stars or download stats** appear anywhere in the repo files. (repo-wide)
- **Unrelated content.** `memory/` contains unrelated personal notes from 2026-04/05, apparently written by an assistant, and nothing used by the code. [R/memory/](R/memory/)

### Inferences
- The project has been dormant for about 8 months as of 2026-11-27. Its own most important empirical finding is that agents do not use the structured or advanced tools without being pushed. That finding argues for automatic extraction and recording (v0.5) over more manual tools.

### Gaps
- No external traction data was in scope (PyPI downloads, GitHub stars, ClawHub installs).

## 6. Self-identified gaps, roadmap, and license

### Takeaway
The stated next step is v0.5: an Interview flow (modelled on gstack `/office-hours`) and AI auto-recording (modelled on OpenViking). Then v0.6+ brings multi-agent collaboration. Many M2/M3 items are explicitly deferred. The BSL 1.1 license allows free use, including production, except as a competing commercial memory or context service, and converts to Apache-2.0 on 2030-03-22.

### Cited Findings
- **Roadmap.** v0.5 is the Interview flow plus AI auto-recording plus token-efficiency work, marked "← 下一步" (next step). v0.6+ is multi-agent. Layer 3 ("协作层", collaboration) is in the inbox. [R/OVERVIEW.md](R/OVERVIEW.md); [R/4-implementation/ROADMAP.md](R/4-implementation/ROADMAP.md)
- **Explicitly deferred items.**
  - Heartbeat-triggered sync, now planned as event-driven.
  - Cross-source rollup.
  - Compression engine.
  - Configurable status mapping ("硬编码够用", hard-coding is enough for now).
  - Fuller offline degradation.
  - Fuller Assembly Trace.
  
  [R/4-implementation/ROADMAP.md](R/4-implementation/ROADMAP.md)
- **Open questions from v0.4.0.**
  - Interview "先 prompt 层试效果" (try it at the prompt layer first).
  - "先用 v0.4.0 跑 Personal Landing Page 项目，收集真实使用数据" (first run a personal landing-page project on v0.4.0 to collect real usage data). There is no later evidence that this happened.
  
  [R/docs/milestones/2026-03-24-v040-analytics-and-research.md](R/docs/milestones/2026-03-24-v040-analytics-and-research.md)
- **Unmeasured token-efficiency metrics.** `context_injection_tokens` and `tokens_per_task_completion` are marked "未量化" (not yet measured), and the injection target is 4k against roughly 8–10k actual. [R/SYSTEM-CONFIG.md](R/SYSTEM-CONFIG.md) (§10)
- **Stale or out-of-sync planning docs.** `NEXT-SESSION.md` still describes the v0.3 plan, from when 584 tests and 18 tools existed. [R/4-implementation/NEXT-SESSION.md](R/4-implementation/NEXT-SESSION.md)
- **License terms.** BSL 1.1. The Licensor is Onta Network and the Licensed Work is "FPMS — Focal Point Memory System". The Additional Use Grant allows any use "EXCEPT for offering it as a competing commercial memory/context management service for AI agents". The Change Date is 2030-03-22 and the Change License is Apache 2.0. [R/LICENSE](R/LICENSE)

### Inferences
- **The BSL protects a future hosted memory/context service.** It does not block individual or internal use. However, it puts off contributors and integrators: it is not OSI-approved, and the package metadata labels it "Proprietary". That matters for a project with no users that competes with permissively licensed Mem0, Letta and Graphiti.
- **The project's own gaps line up with the obvious research directions:**
  - automatic capture of memory (the agent doesn't log on its own);
  - compression and consolidation;
  - measured token efficiency and retrieval quality (no evals);
  - real multi-agent shared memory.
- **The more defensible differentiators are:**
  - a deterministic "spine" with invariants, idempotency and audit;
  - task/DAG-aware attention and risk alerts;
  - knowledge inheritance down a work hierarchy.

### Gaps
- No explicit TODO/FIXME markers exist in the code. The only `NotImplementedError` cases are optional adapter methods and one executor guard. Self-identified gaps live in the docs, not the code. [R/fpms/spine/adapters/base.py#L20-L53](R/fpms/spine/adapters/base.py#L20-L53); [R/fpms/spine/command_executor.py#L47](R/fpms/spine/command_executor.py#L47)
