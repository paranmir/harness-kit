---
status: draft
scope: repository
---

# Harness MCP Tool Surface

## Authority
Source of truth for the MCP tool surface exposed by the agentlaw-memory server
and shared with target projects via the bundled scaffold. `agentlaw verify`
checks package-data sync and tool coverage against `src/agentlaw/server/tools/`.

Governing law: `agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md`. Amendments land
through a plan that updates this file and any dependent law
clause in the same change.

## Purpose
Contract reference for each MCP tool's name, parameters, return shape, and
error semantics. Tool-selection rules live in
[`agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md`](../law/MEMORY_AND_CONTINUITY_RULES.md)
§ Memory Tool Surface (MCP).

## Tool Description Format

Every MCP tool description in this server carries five axes:

1. **Actionable first line** — a single sentence stating what the tool does, in imperative voice, so the host knows immediately whether to dispatch.
2. **Negative spec** — when NOT to use this tool. Names the closest sibling tool the host might confuse it with, and the discriminator.
3. **Timing** — when in a session/plan/workflow this tool fits. Names the upstream and downstream tools or phases.
4. **Use-case example** — a one-line concrete example that grounds the abstract description (parameter values, returned shape, or sample invocation context).
5. **BEFORE-CALL prerequisite reading list** — a short list of `agentlaw_docs/`-relative paths the host should consult before invoking. Universal: `agentlaw_docs/contracts/agentlaw-mcp-tools.md` (this file). Tool-family-specific:
   - Memory tools: `agentlaw_memory/LOOKUP_RULES.md`, `agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md`.
   - Session tools: `agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md` §Canonical Save/Restore Route.
   - Plan-review tools: `agentlaw_docs/planning-protocol/review-method.md`, `agentlaw_docs/law/PLANNING_AND_REVIEW_RULES.md`, this file's plan-review surface section.
   - For tools with code-defined valid input values (e.g., `clarity_scores` keys for `agentlaw_plan_review_interview_answer_submit`), axis 5 also names the source-code location of valid values (e.g., `src/agentlaw/server/tools/plan_review.py CLARITY_WEIGHTS_*`) so consumers do not need to grep source code for valid keys.

New and modified tool descriptions must keep this five-axis form. Load-bearing
tool descriptions without axis 5 fail form-vs-substance review and verifier
coverage under `agentlaw_docs/law/MECHANICAL_ENFORCEMENT_POLICY.md`.

## Governance Reminder Skill (agentlaw-governance)

`agentlaw init` deploys a cross-platform Agent Skills open standard SKILL.md to both `.claude/skills/agentlaw-governance/` (Claude Code) and `.agents/skills/agentlaw-governance/` (Codex). The skill description matches when the agent's current work concerns the agentlaw harness: fix / init / upgrade / align / governance drift, tracker-policy violation, repeated harness-rule bypass, agentlaw setup, agentlaw kit upgrade, MCP or memory drift, hook routing, or planning/review protocol drift. It surfaces the matching root-control document (`AGENTLAW_FIX_TOOL.md` / `AGENTLAW_INIT_TOOL.md` / `AGENTLAW_UPDATE_TOOL.md` / `AGENTLAW_ALIGN_TOOL.md`) before plan-spawning or other action-consequential steps. It is not for ordinary project bug fixes, feature work, dependency updates, or app setup unless the issue affects the agentlaw harness itself. Both deployment paths receive identical SKILL.md content (byte-equal); directory difference is platform convention. `agentlaw init --merge` refreshes the skill files additively (no overwrite of existing customization).

Scaffold root also receives `AGENTS.md` and `CLAUDE.md` reminder lines (universal config files that Codex and Claude Code respectively auto-load every session), pointing at the same skill and root-control documents. The reminder surfaces are skill first, entry-document second, and tool-description fallback third.

## Plan Authoring Reminder Skill (agentlaw-plan-authoring)

`agentlaw init` also deploys a cross-platform Agent Skills open standard
SKILL.md to both `.claude/skills/agentlaw-plan-authoring/` (Claude Code) and
`.agents/skills/agentlaw-plan-authoring/` (Codex). The skill description
matches when the agent is creating, reviewing, revising, executing, or
archiving an agentlaw execution plan and routes the agent to
`agentlaw_docs/planning-protocol/plan-template.md`,
`agentlaw_docs/planning-protocol/review-method.md`,
`agentlaw_docs/planning-protocol/task-classification.md`,
`agentlaw_docs/planning-protocol/persona-decks-core.md`,
`agentlaw_docs/planning-protocol/persona-decks-specialized.md`, and
`agentlaw_docs/planning-protocol/persona-section-map.md`.

The skill is a procedural reminder only. It must not introduce a new plan
format, duplicate the planning protocol, or override `agentlaw_docs/law/*`. Both
deployment paths receive identical SKILL.md content (byte-equal); directory
difference is platform convention.

## Post-Task Retrospective Reminder Skill (agentlaw-post-task-retrospective)

`agentlaw init` also deploys a cross-platform Agent Skills open standard
SKILL.md to both `.claude/skills/agentlaw-post-task-retrospective/` (Claude
Code) and `.agents/skills/agentlaw-post-task-retrospective/` (Codex). The
skill description matches near task closeout, after meaningful fix,
implementation, plan/oracle/archive, publish, verification, or governance
work, and when the agent or user raises retrospective / 회고 / skill-route
questions.

The skill is a procedural reminder and routing aid only. It tells the agent to
choose among skill, law/contract, test/verifier, memory/reference, tracker, or
chat-only outcomes; it does not make skill creation the default. The route uses
a Promotion Score so repeated friction, user correction, command/tool friction,
boundary risk, compaction risk, or ignored existing guidance cannot be dismissed
as chat-only merely because some related artifact already exists. Real
corrections, repeated failures, governance gaps, or rule bypasses in the
agentlaw harness still route through `AGENTLAW_FIX_TOOL.md` before file
changes. Ordinary project retrospectives route to the owning project layer.
Both deployment paths
receive identical SKILL.md content (byte-equal); directory difference is
platform convention.

The score treats command/tool friction as promotion evidence when wrong
commands, wrong shell forms, wrong Python invocations, wrong pytest selectors,
wrong working directories, MCP/raw-tool route confusion, schema mistakes, or
repeated retries create future-prevention value. `chat-only` remains valid only
for low-risk one-off issues that are already covered with no meaningful
recurrence risk.

At closeout, the final response reports both the durable route and the
`skill create/update decision`. The skill decision is explicit even when no
skill file changed, so users can see whether the retrospective concluded
`skill`, another durable route, or no promotion target.

When skill lifecycle telemetry is available, the retrospective route may use
`agentlaw_skill_lifecycle_report` as evidence and may record meaningful use via
`agentlaw_skill_event_record`. Telemetry is lower-authority runtime state; stale
candidate reports do not authorize automatic skill creation, deletion, archive,
quarantine, or file rewrites.

## Tool Operations Authority

- All tools operate on `.agentlaw/index/meta.db` (SQLite + FTS5 + sqlite-vec) and the canonical Markdown layer under `agentlaw_memory/*`.
- Write tools must write the canonical Markdown layer first, then update derived DB/index rows in a SQLite transaction. If the canonical write succeeds but the derived update fails, the canonical Markdown remains the source of truth and the tool must return visible derived-index drift with a repair path.
- Tools must never mutate `agentlaw_docs/law/*`, `AGENTLAW_CONSTITUTION.md`, root control documents, or `plans/*`. Memory cannot promote itself into law; only `memory_propose_promotion` may suggest promotion.
- Tools must respect the conflict resolution rules in `MEMORY_AND_CONTINUITY_RULES.md` (`authority > scope > recency`, status transitions, exclusion rules).

## Common Conventions

- All timestamps are ISO 8601 UTC unless stated otherwise.
- All ids are stable slugs persisted in markdown front matter (see schema reference).
- All tools return a `runtime_status` field reporting whether the index, vector store, and embedding model are available. The `runtime_status` block contains: `mcp_server`, `meta_db`, `fts_index`, `vector_index`, `memory_items_index` (`ready` when the items table matches on-disk Markdown under `agentlaw_memory/{known-facts,rules}/`, `stale_relative_to_disk` when on-disk count exceeds items table count; `agentlaw_session_restore` triggers items-only auto-rebuild on stale before assembling the packet so the returned packet reflects post-rebuild state), `embedding_model`, `embedding_model_on_disk`, `embedding_model_load`, `vector_alignment`, `schema_version`, `kit_version`, and `embedding_runtime`.
- When a tool cannot complete due to missing runtime, it returns a structured `degraded` result rather than raising; the caller decides how to fall back.
- Closed-set parameters (`type`, `scope`, `status`, `to_status`, `kind`, `mode`, `order_by`, `target_kind`, skill lifecycle `event_kind`, `source`, and `outcome`) advertise their permitted values via JSON Schema `enum` constraints. MCP hosts may reject invalid values at the schema layer before the tool is invoked. The server still validates every closed-set parameter at runtime — schema enforcement is advisory; the server remains authoritative.

### Error Codes
Expected Harness domain failures are returned in-band in the tool result as an `error` object plus `runtime_status` when available:

```json
{
  "error": { "code": "memory.not_found", "id": "fact/missing-example" },
  "runtime_status": {}
}
```

Unexpected transport, protocol, or server failures still follow JSON-RPC 2.0 (MCP transport):

- `-32700` Parse error
- `-32600` Invalid Request
- `-32601` Method not found
- `-32602` Invalid params
- `-32603` Internal error
- `-32000` to `-32099` Application errors (server-defined).

Domain codes used in this surface include:

| Code | When it is raised |
| --- | --- |
| `memory.not_found` | An id was provided but does not exist in `memory_items` or `memory_logs`. |
| `memory.governance_violation` | A write tool was asked to modify a path reserved for governance (e.g., `agentlaw_docs/law/`, root control documents, `plans/`). |
| `memory.invalid_transition` | A lifecycle operation is rejected because the target state is not reachable from the current state (e.g., `memory_supersede` where the replacement id is not `active`). |
| `memory.read_only_violation` | An append-only resource (log entry) was asked to be overwritten. |
| `memory.runtime_unavailable` | A required runtime dependency is not available (missing/corrupt DB, embedding model missing in a path that mandated it). |
| `memory.derived_index_drift` | A canonical Markdown write succeeded, but the derived DB/index update failed. The response includes the markdown path and a `memory_runtime_repair` scope. |
| `memory.runtime_repair_conflict` | A runtime repair request was rejected because another runtime repair job is already queued or running. |
| `memory.job_stale` | A queued or running runtime repair job exceeded the stale threshold and is treated as abandoned/failed. |
| `memory.invalid_params` | A parameter fails a general validation check not covered by a more specific code (e.g., unknown `type`, `scope`, or `status` value; `old_id == new_id`). |
| `memory.invalid_datetime` | A timestamp or date-bearing value violates the Timestamp Integrity law (see `agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md`), e.g., a caller-supplied log id whose date segment does not match today's UTC date. |
| `memory.not_implemented` | Reserved for future deferrals — a documented tool parameter path not yet implemented in the current runtime. No tool parameter currently returns this code. |

### Pagination Details
List and search tools return a `next_cursor` (opaque string). Pass it as `cursor` on the next call to fetch the next page. Cursor-based pagination is stable under concurrent writes; offset-based pagination is not supported.

```
memory_search(query="...", limit=10)
→ { hits: [...], next_cursor: "abc123" }

memory_search(query="...", limit=10, cursor="abc123")
→ { hits: [...], next_cursor: "def456" }
```

When `next_cursor` is null, the result set is exhausted.

### Concurrency And Multi-Agent
Each MCP server process opens `.agentlaw/index/meta.db` in WAL mode (`PRAGMA journal_mode=WAL`) with `PRAGMA busy_timeout=5000`. Multiple agent processes may share the same database file safely:

- Concurrent reads are non-blocking.
- Writes are serialized by SQLite; brief lock waits are normal.
- No application-level authentication in v1; the server trusts its caller (single-tenant per agent process).

If per-agent attribution is needed later, a `created_by` field may be added through a schema migration without breaking existing tools.

---

## Read Tools

### `memory_search`
Hybrid FTS + vector search across memory items by default. Use for prior judgment, decisions, rationale, or cross-session continuity questions; for current source/law facts use Grep / Read instead. Routing criterion: `agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md` §Read Routing Criterion.

Logs are historical records. They are not default search targets and are never vector-search targets in v1. Set `include_logs=true` when the caller explicitly wants log/history recall; matching log hits then come from FTS only and carry `type="log:<kind>"`. Results from the item FTS5 path and the sqlite-vec path are blended via **Reciprocal Rank Fusion (RRF, k=60)** at query time. RRF uses each hit's rank in the two lists; absolute score scales are ignored, so no normalization is required and no blended score is stored. RRF score per item: `Σ 1/(k + rank_in_list)` across the FTS and vector lists.

**Parameters**
- `query` (string, required) — natural-language query, KO/EN mixed allowed.
- `types` (array of strings, optional) — filter by `fact` / `preference` / `rule` / `log`.
- `scopes` (array of strings, optional) — filter by `repository` / `session`; `global` is accepted only as a legacy local-target filter for rows written by older harness versions. It never means cross-project memory.
- `tags` (array of strings, optional) — restrict to entries carrying any of these tags.
- `statuses` (array of strings, optional) — defaults to `["active"]`. Override to include other statuses. Allowed values: `active` / `tentative` / `stale` / `superseded` / `disputed` / `suppressed` / `quarantined`.
- `time_window_days` (integer, optional) — restrict to entries updated within N days.
- `limit` (integer, optional, default 10) — maximum hits returned.
- `mode` (string, optional, one of `hybrid` / `fts` / `vector`, default `hybrid`).
- `cursor` (string, optional) — opaque pagination cursor from a previous response.
- `include_logs` (boolean, optional, default `false`) — include log FTS hits in addition to memory item hits. Logs are excluded by default to avoid historical-session noise in ordinary recall.

**Returns**
- `hits` — array of `{ id, type, scope, status, anchor, snippet, path, rrf_score, ranks: { fts, vector } }`. `ranks` records each item's 0-based rank in the source lists (null when absent from a list).
- `next_cursor` (string or null).
- `query_token_estimate`, `result_token_estimate`.
- `runtime_status`.

**Errors**
- `memory.invalid_params` for an empty query, unknown mode, invalid filter value, invalid limit, or malformed cursor.
- `memory.runtime_unavailable` when `mode="vector"` requires the embedding/vector runtime and it cannot load, encode, or query.
- In `mode="hybrid"`, vector runtime failures degrade to FTS-only results with a `degraded` note. Hybrid vector model load/encode has a 10-second budget; on timeout the tool returns FTS results with `vector_degraded: timeout_after_10s`.
- MCP startup starts embedding-model warmup in an isolated background worker process by default, but server initialization and tool calls must not block on model load or worker startup. The worker is considered inference-ready only after the parent MCP process successfully round-trips a readiness `encode_query` request and receives a 384-dimensional vector; the child's load-complete signal alone is not enough. The first hybrid search uses vector only if that parent-side validation has completed; while the model is loading, stalled, or failed, it returns FTS results with a visible `vector_degraded: model_not_loaded (...)` note. Worker spawn failures, startup timeouts, process exits, and readiness-validation failures are reported through `embedding_model_load.status="failed"` rather than killing MCP startup. Set `AGENTLAW_MCP_DISABLE_STARTUP_WARMUP=1` only for diagnostics or emergency degraded operation.
- When the current filters leave no searchable memory item chunks, `memory_search`
  does not load the embedding model. `mode="hybrid"` returns FTS results plus
  `degraded=["vector_skipped: no_searchable_item_vectors"]`; `mode="vector"`
  returns an empty `hits` list without `memory.runtime_unavailable`.

---

### `memory_get`
Fetch a single item or log entry by id. Direct id-keyed lookup; no routing decision. Ids come from `memory_save_item` / `memory_append_log` responses, plan or working-set references, `memory_search` / `memory_recent_logs` / `memory_list` results, or `agentlaw_session_save.log_entry_id` — `memory_search` is not a prerequisite when an id is already known.

**Parameters**
- `id` (string, required).
- `include_chunks` (boolean, optional, default false) — include chunk-level body breakdown.

**Returns**
- `entry` — full record from `memory_items` or `memory_logs` (kind inferred from id).
- `chunks` — present only when `include_chunks=true`.
- `runtime_status`.

**Errors**
- `not_found` if id does not exist.

---

### `memory_read_source`
Read a closed-set canonical memory source object from `agentlaw_memory/*`.

This tool returns canonical Markdown source text for specific memory source objects. It does not accept filesystem paths and is not a general Markdown, repository file, law, plan, reference, or source-code reader.

**Parameters**
- `kind` (string, required) — one of `working_set`, `lookup_rules`, `preferences`, `known_fact`, or `log_entry`.
- `id` (string, required only for `known_fact` and `log_entry`) — `fact/<slug>` for known facts, or `log/YYYY-MM-DD/<slug>` for log entries.
- `max_chars` (integer, optional, default 12000, maximum 50000) — maximum source characters returned. Larger sources return `truncated: true`.

Singleton kinds (`working_set`, `lookup_rules`, `preferences`) reject `id`.

**Returns**
- `kind`, `id`, `path`.
- `canonical` — always true when a source object is returned.
- `source_authority` — `memory`.
- `authority_warning` — reminder that memory is lower authority than law, root controls, plans, references, and current repository state.
- `content` — canonical Markdown source text or bounded prefix.
- `front_matter` — parsed YAML front matter for singleton/known-fact files, or parsed entry metadata for log entries.
- `content_hash` — SHA-256 hash of the full canonical source text.
- `truncated` — whether `content` was bounded by `max_chars`.
- `token_estimate`.
- `runtime_status`.

**Errors**
- `memory.invalid_params` for unknown kind, malformed id, missing required id, id on singleton kinds, or invalid `max_chars`.
- `memory.not_found` when the requested closed-set memory source object does not exist.
- `memory.source_read_violation` when source resolution would leave the repository's canonical `agentlaw_memory/` namespace.

---

### `memory_list`
Filtered list of items by metadata. Use when the question is "what items of kind X exist" rather than free-text search. Routing criterion: `agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md` §Read Routing Criterion.

**Parameters**
- `types` (array, optional) — same semantics as `memory_search.types`. Allowed values: `fact` / `preference` / `rule`.
- `scopes` (array, optional) — same semantics as `memory_search.scopes`. Allowed values: `repository` / `session`; `global` is accepted only as a legacy local-target filter for older rows.
- `statuses` (array, optional) — same semantics as `memory_search.statuses`. Allowed values: `active` / `tentative` / `stale` / `superseded` / `disputed` / `suppressed` / `quarantined`.
- `tags` (array of strings, optional) — same semantics as `memory_search.tags`.
- `applies_when` (array of strings, optional) — match `rule` items whose trigger tags include any of these values.
- `order_by` (string, optional, one of `updated_at_desc` / `created_at_desc`, default `updated_at_desc`).
- `limit` (integer, optional, default 50).
- `cursor` (string, optional) — opaque pagination cursor from a previous response.

**Returns**
- `items` — array of `{ id, type, scope, status, title, path, updated_at, tags }` (no body to keep packets small; use `memory_get` for body).
- `next_cursor` (string or null).
- `total` (integer) — total matching count even when limited.
- `runtime_status`.

---

### `memory_recent_logs`
Tail of the append-only log, newest first. Use to recover recent session context, decisions made earlier, or to verify whether a finding has already been logged before re-investigating. Routing criterion: `agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md` §Read Routing Criterion. Default time window 14 days.

**Parameters**
- `days` (integer, optional, default 14).
- `kinds` (array of strings, optional) — filter by `decision` / `correction` / `session_save` / `verification`.
- `tags` (array of strings, optional).
- `limit` (integer, optional, default 50).
- `cursor` (string, optional) — opaque pagination cursor from a previous response.

**Returns**
- `logs` — array of `{ id, kind, scope, title, path, occurred_at, tags }`.
- `next_cursor` (string or null).
- `runtime_status`.

---

## Write Tools

### `memory_save_item`
Create or update a durable memory item — rules (govern future behavior), facts (persist as current state), or preferences (user / maintainer style). Items are vector-indexed and shape `memory_search` results; poorly scoped items pollute semantic recall until marked stale. Selection criterion (stricter than logs): `agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md` §Item Write Criterion. For transient findings use `memory_append_log`.

Types `fact`, `rule`, and `preference` are all fully supported. `fact` and `rule` use one canonical file per item under `agentlaw_memory/known-facts/` or `agentlaw_memory/rules/` with YAML front matter + body. `preference` uses single-file section-aware editing of `agentlaw_memory/preferences.md`: each preference is one `## <slug>` section per the Machine-Writable Grammar in `agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md` §"Preferences File"; the writer rewrites only the target section's bytes, refreshes the file-level `updated_at` timestamp, and leaves all sibling sections byte-identical.

Calls with `type="working_set_entry"` return `memory.invalid_params` — the value is not a declared type at the tool layer. The working-set file is rewritten in full by `agentlaw_session_save` rather than addressed at item-level.

For all types: writes canonical Markdown first, then updates derived item/chunk/tag/vector rows.

**Parameters**
- `type` (string, required) — `fact` / `preference` / `rule`.
- `id` (string, optional) — required on update; on create the server generates a slug if omitted.
- `scope` (string, required) — `repository` / `session`. Memory scopes are local to the current target project. Direct legacy calls with `scope="global"` are stored as `scope="repository"` with a degraded note; `global` is not advertised in the MCP write schema and never means cross-project memory. `scope="user"` is invalid; preferences are represented by `type="preference"`.
- `status` (string, optional, default `active`) — one of `active` / `tentative` / `stale` / `superseded` / `disputed` / `suppressed` / `quarantined`.
- `title` (string, optional).
- `body` (string, required) — markdown body. For preferences, the body is the prose under the field-list block; the writer assembles the field list from `id` / `status` / `scope` / fixed defaults (`source: conversation`, `last_checked: <today>`, `applies_to: final_response`, `loses_to: [law, current_explicit_instruction, safety, verification]`).
- `tags` (array of strings, optional).
- `path` (string, optional) — target markdown path; the server picks a default by type when omitted (`agentlaw_memory/preferences.md` for preferences; per-slug paths for facts and rules).
- `supersedes` (string, optional) — id this entry supersedes (sets supersession links on both records).

**Returns**
- `entry` — the persisted record metadata (post-merge for updates). The
  body itself is **not** echoed; instead `entry.body_chars` carries the
  character count of the persisted body so callers can round-trip-check
  what was sent (`assert response["entry"]["body_chars"] == len(sent_body)`).
  Fetch the full body via `memory_get(id)` or read the canonical markdown
  at `markdown_path`.
- `created` (boolean).
- `markdown_path` — absolute path to the canonical markdown that was written.
- `chunks_written` (integer).
- `runtime_status`.
- On derived DB/index failure after the Markdown write: `error.code = memory.derived_index_drift`, `degraded`, and `drift = { canonical_written, markdown_path, repair_tool, repair_scope }`.

**Errors**
- `governance_violation` if `path` targets `agentlaw_docs/law/`, `AGENTLAW_CONSTITUTION.md`, root control docs, or `plans/*`.
- `memory.invalid_params` for unknown `type`, `scope`, or `status`; `scope="user"` is rejected with guidance to use `type="preference"` and `scope="repository"` for durable local preferences.
- `derived_index_drift` if the canonical Markdown write succeeded but derived DB/index update failed.

---

### `memory_append_log`
Append-only log entry for session-spanning findings, decisions, and corrections. For durable rules or current-state facts, use `memory_save_item`. Selection criterion: `agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md` §Log Write Criterion. Logs are FTS-only and do not affect `memory_search` semantic ranking.

Writes canonical Markdown first to today's log file, then updates derived log/chunk/tag rows. V1 does not create log vector rows.

**Parameters**
- `kind` (string, required) — `decision` / `correction` / `prune` / `session_save` / `verification`.
- `scope` (string, required) — `repository` / `session`. Memory scopes are local to the current target project. Direct legacy calls with `scope="global"` are stored as `scope="repository"` with a degraded note; `global` is not advertised in the MCP write schema and never means cross-project memory. `scope="user"` is invalid.
- `title` (string, required).
- `body` (string, required).
- `tags` (array of strings, optional).
- `session_id` (string, optional).
- `id` (string, optional) — server generates if omitted.

**Returns**
- `entry` — the appended record metadata. The body itself is **not** echoed;
  `entry.body_chars` carries the character count of the persisted body for
  caller-side round-trip sanity. Fetch full body via `memory_get(id)` or
  read the canonical markdown at `markdown_path`.
- `markdown_path` — absolute path to today's log file.
- `chunks_written` (integer).
- `runtime_status`.
- On derived DB/index failure after the Markdown append: `error.code = memory.derived_index_drift`, `degraded`, and `drift = { canonical_written, markdown_path, repair_tool, repair_scope }`.

**Errors**
- `read_only_violation` if the caller attempts to update an existing log id.
- `memory.invalid_params` for unknown `kind` or write `scope`.
- `derived_index_drift` if the canonical Markdown append succeeded but derived DB/index update failed.

---

## Lifecycle Tools

### `memory_set_status`
Transition an item's status following the conflict rules in `MEMORY_AND_CONTINUITY_RULES.md`.

**Parameters**
- `id` (string, required).
- `to_status` (string, required) — one of the seven allowed statuses: `active` / `tentative` / `stale` / `superseded` / `disputed` / `suppressed` / `quarantined`.
- `reason` (string, required) — recorded as a log entry of kind `correction`.

**Returns**
- `entry` — updated record.
- `log_entry_id` — the correction log entry that was appended.
- `runtime_status`.

**Errors**
- `invalid_transition` if the status change is not allowed by the conflict rules.

---

### `memory_supersede`
Mark an item as `superseded` and link it to a replacement in one operation.

**Parameters**
- `old_id` (string, required).
- `new_id` (string, required) — must already exist and be `active`.
- `reason` (string, required).

**Returns**
- `old_entry`, `new_entry` — both with supersession links set.
- `log_entry_id`.
- `runtime_status`.

---

## Governance Tool

### `memory_propose_promotion`
Propose escalating a memory entry into law, plan, reference, or shared artifact. Append-only proposal — writes a `promotion-proposal` log entry; **does not directly modify the target.** Selection criterion (three-property gate: durability + future operational relevance + authority gap): `agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md` §Promotion Proposal Protocol.

**Parameters**
- `source_ids` (array of strings, required) — memory ids to promote.
- `target_kind` (string, required) — `law` / `plan` / `reference` / `shared_artifact`.
- `target_path_suggestion` (string, optional).
- `rationale` (string, required).

**Returns**
- `proposal_id` — id of a `memory_logs` entry with kind `decision` and a tag `promotion-proposal`.
- `promotion_protocol` — the agent-applied promotion rule: required criteria, negative cases, source-id rule, and `runtime_selects_candidates=false`.
- `runtime_status`.

The proposal is a record only; it does not modify the target document set. Reviewed promotion goes through the normal change path (plan or direct law edit).
Runtime never selects semantic promotion candidates. The agent must call this tool only after judging that the source information has durability, future operational relevance, and an authority gap.

---

## Session Tools

### `agentlaw_session_restore`
Implements the Canonical Restore Route in `MEMORY_AND_CONTINUITY_RULES.md`.

**Parameters**
- `log_lookback_days` (integer, optional) — overrides the default from `LOOKUP_RULES.md`.
- `include_drift_check` (boolean, optional, default true).
- `recent_logs_limit` (integer, optional, default `15`) — cap `recent_logs` at the most-recent N entries within the lookback window. Pass `0` for unlimited (returns every entry inside the lookback window, matching the historical pre-cap behavior). The cap keeps a typical restore packet compact; use `0` only when a specific task genuinely needs the full history. The same flag exists on the CLI fallback as `agentlaw session-restore --recent-logs-limit N`.

**Returns**
- The full `agentlaw_session_restore` packet defined in `MEMORY_AND_CONTINUITY_RULES.md` ("Session Restore Packet Format"). Always includes `runtime_status`, `authority_warnings`, source pointers, excluded memory, and `token_estimate`.
- `active_plan_bodies` (array of objects) — one entry per file under `agentlaw_docs/plans/active/*` matching the order of `active_plans`. Each entry carries `path`, `title`, `body` (full file content, truncated above 30_000 chars with a marker line), `body_chars` (full file size), `truncated` (boolean). Entries that fail to read carry `error` instead of `body`. The runtime fetches these so the agent that consumes the packet has the body-level substance §Canonical Restore Route Mandatory Tier step 4 requires; the on-disk files remain authoritative when verification matters.
- `active_rule_bodies` (array of objects) — one entry per `active_rules` entry, matching that list by `id`. Each entry carries `id`, `scope`, `applies_when`, `summary`, `path`, `body` (full rule body, truncated above 12_000 chars with a marker line), `body_chars`, `truncated`. Entries that fail to read carry `error` instead of `body`. Satisfies Mandatory Tier step 7 — every active rule's body must reach the agent's working context, not just its summary.
- `preferences_body` (object or null) — the full content of `agentlaw_memory/preferences.md` as `{path, body, body_chars}`, or `null` when the file is absent (fresh project). Satisfies Mandatory Tier step 8.
- `lookup_rules_body` (object or null) — the full content of `agentlaw_memory/LOOKUP_RULES.md` as `{path, body, body_chars}`, or `null` when the file is absent. Satisfies Mandatory Tier step 9.
- `known_facts_manifest` (array of objects) — one entry per active known-fact (`type='fact'`, `status='active'`). Each entry carries `id`, `title`, `scope`, `path`, `last_checked`. **Bodies are NOT included** — the manifest gives a complete catalog of remembered facts so the agent does not invent or contradict known state, while bodies are pulled on demand under the Conditional Tier or via the working-frame search hits. Satisfies Mandatory Tier step 10.
- `working_frame_memory_hits` (array of objects) — top-K hits (default K=10) from a `memory_search` call composed from `current_goal + next_actions + open_questions` and filtered to `types=["fact"]`, `statuses=["active"]`. Each entry carries `id`, `title`, `path`, `snippet`, `rrf_score`. Empty list when the working frame is empty or the search engine is unavailable. Satisfies Mandatory Tier step 11; the search is the indexed-memory layer's intended retrieval path, paired with the manifest as a safety net.
- `recent_friction_logs` (array of objects) — compact summaries of recent likely-repeat friction logs. Each entry carries `id`, `kind`, `scope`, `path`, `title`, `occurred_at`, `recorded_at`, `tags`, and `body_included: false`. Log bodies are never embedded here; fetch by id through `memory_get` when the substance is needed.
- `latest_session_save_body` (object or null) — the body of the most recent `kind: session_save` log entry, with `id`, `path`, `title`, `occurred_at`, `body`, `body_chars`, `truncated`. Truncation cap is 8_000 chars with a `memory_get(id=...)` pointer for the full entry. `null` when no `session_save` log entry exists (e.g., fresh project). Recovers the directly preceding session's wrap-up summary so the agent does not have to issue a separate `memory_get` call before composing the first response.
- `token_estimate.packet_tokens` reflects the **full serialized packet** — including `runtime_status`, `authority_recall`, `governance_drift`, every per-entry field, and the body-level payloads (`active_plan_bodies`, `active_rule_bodies`, `preferences_body`, `lookup_rules_body`, `known_facts_manifest`, `working_frame_memory_hits`, `recent_friction_logs`, `latest_session_save_body`). Clients comparing this figure against a fixed context-window budget must account for body-level reads on every restore; the Mandatory Tier guarantees the packet carries substance, not just paths.
**Framework reminder fields (always present on every restore packet)**

These fields are runtime-generated reminders bundled by `runtime_reminders_for_restore()` in `authority.py`. They do not persist to disk and do not depend on `agentlaw_memory/working-set.md` content; they are regenerated fresh on every restore so each session starts with the same framework guidance.

- `authority_recall` (object) — compact runtime guidance pointing to governing sources and the promotion protocol. Sub-keys:
  - `governing_sources` (array) — list of `{path, reason}` entries for the law layer (constitution, MEMORY_AND_CONTINUITY_RULES.md, REPOSITORY_ARTIFACT_RULES.md, agentlaw-mcp-tools.md).
  - `task_guidance` (array) — list of `{task, read: [path]}` entries advising which law files to read for common task categories (memory subsystem change, promotion decision, artifact structure change).
  - `promotion_protocol` (object) — see `promotion_reminder` schema below; same shape, embedded for the "promotion decision" task path.
  - `memory_intent_rule` (object) — same shape as `memory_intent_reminder` below, embedded for completeness.
  - `persistence_rule` (string) — short reminder that routine authority checks should not be persisted as durable Markdown records.
  This field does not include runtime-selected `promotion_candidates`.

- `write_discipline_reminder` (object) — universal framework reminder for the Write Discipline rule. Sub-keys:
  - `checklist` (array of strings) — per-turn questions the agent should run before composing a final response (log criterion, item criterion, read routing, read-cluster check, working-set field discipline at session-save time).
  - `criteria_anchors` (object) — pointers from semantic key (`log_write`, `item_write`, `read_routing`, `log_body_format`, `working_set_discipline`, `promotion`) to the corresponding `agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md §Heading` reference.
  - `rule` (string) — one-line restatement: "Silence is a valid answer when the criteria are not met. Volume is not the target; selectivity is."
  - `runtime_boundary` (string) — note that runtime surfaces the reminder; the agent judges whether each criterion is met.

- `restore_procedure_reminder` (object) — surfaces the §Canonical Restore Route Mandatory Tier procedure that every agent must follow on session restore. Sub-keys:
  - `rule` (string) — restatement of the timing rule.
  - `steps` (array) — one entry per Mandatory Tier step, each `{step, action, purpose}`. Steps cover runtime integrity check, working_set read, full law layer body read, every active plan body read, latest session_save body read, recent_logs title scan, every active rule body fetch, preferences body read, lookup_rules body read, known-facts manifest scan, working-frame memory_search, drift inspection, packet assembly, and gap surfacing.
  - `rule_anchor` (string) — `agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md §Canonical Restore Route — Mandatory Tier`.
  - `runtime_boundary` (string) — runtime surfaces the procedure and pre-fetches every body field the procedure asks for (`active_plan_bodies`, `active_rule_bodies`, `preferences_body`, `lookup_rules_body`, `known_facts_manifest`, `working_frame_memory_hits`, `latest_session_save_body`); the agent still owns step execution and gap-surfacing judgment.

- `consult_before_answer` (object) — read-side timing rule packet for the §Consult-Before-Answer Rule; pins the rule that memory-routed questions must consult memory **before** composing the answer, not as a follow-up. Sub-keys:
  - `rule` (string) — restatement of the rule.
  - `applies_when` (string) — trigger signal vocabulary (Q1/Q2 of §Read Routing Criterion: 'we', 'previously', 'earlier', '왜', '근거', '어디까지', 'outstanding', plus multilingual analogues). Q3 (current source / law fact) does NOT trigger.
  - `anchor` (string) — `agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md §Consult-Before-Answer Rule`.
  - `runtime_boundary` (string) — runtime surfaces the rule; the agent judges whether the question's shape triggers it and which read tool to call.

- `plan_discipline_reminder` (object) — planning workflow reminder for non-trivial work. Sub-keys:
  - `rule` (string) — compact plan-first obligation.
  - `non_trivial_triggers` (array of strings) — common cases that require a plan, such as law/root-control edits, public runtime surface changes, schema additions, and cross-concern commits.
  - `trivial_exemptions` (array of strings) — narrow cases where a repository-tracked plan is normally not required.
  - `anti_rationalization` (array of strings) — clauses that prevent auto-mode, broad user authorization, or elastic "trivial" interpretation from bypassing the plan gate.
  - `planning_workflow` (array of strings) — canonical sequence: `classify_request`, `draft_plan`, `review_with_persona_decks`, `synthesize_review_findings`, `produce_revised_plan`.
  - `classification_source` (string) — `agentlaw_docs/planning-protocol/task-classification.md`.
  - `review_method_source` (string) — `agentlaw_docs/planning-protocol/review-method.md`.
  - `persona_deck_sources` (array of strings) — `agentlaw_docs/planning-protocol/persona-decks-core.md` and `agentlaw_docs/planning-protocol/persona-decks-specialized.md`.
  - `review_evidence_fields` (array of strings) — compact plan fields and section names that make review readiness parseable: `Review required`, `Plan reviewed`, `Personas applied`, `Revised after review`, `Review exemption reason`, and `Separate Persona Review Passes`.
  - `mechanical_consequence` (string) — explains the plan-coverage verifier consequence for uncovered non-trivial changes.
  - `anchor` (string) — governing law anchors for the pre-edit and active-plan-field obligations.

- `friction_capture_reminder` (object) — immediate failure-capture guidance for high-impact or likely-repeat command/tool/MCP workflow friction. Sub-keys:
  - `capture_when` (array of strings) — failure classes that should trigger capture consideration.
  - `skip_when` (array of strings) — one-off or already-controlled cases that do not need new durable records.
  - `immediate_actions` (array of strings) — search-before-retry and closed resolution steps.
  - `post_search_resolution` (array of strings) — exactly-one outcomes after search: cite a covering record, record compact friction evidence, or state an explicit non-save reason.
  - `minimum_record_fields` (array of strings) — minimum reusable facts to preserve.
  - `compact_record_constraints` (array of strings) — limits that keep records reusable, compact, and free of local-only incident detail.
  - `storage_boundary` (string) — use MCP memory or `agentlaw_session_save` `log_entry`; do not edit memory files directly or promote without the normal authority path.
  - `runtime_selects_memory` and `runtime_selects_candidates` (boolean, always `false`).
  - `anchor` (string) — `agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md §Immediate Friction Capture`.

- `suggested_queries` (array of strings) — topic-mined phrase list from recent log entries within the lookback window. Empty list when no recent logs exist (fresh session, fresh project, or `memory_logs` table missing). Otherwise, the top-K most-frequent tokens (default K=5) extracted from log titles + tags, weighted with linear recency decay so newer entries outrank older ones. The list is a starting-point reminder, not a curated retrieval — agents may use it to seed `memory_search` queries or ignore it if irrelevant. The phrase shape is short tokens (lowercased ASCII for English / digits, raw Hangul for Korean) suitable for direct use as `memory_search.query` substrings.

- `memory_intent_reminder` (object) — forward-looking reminder for the Memory Intent Rule, surfaced on every restore packet so the rule is refreshed at every session start. Sub-keys:
  - `triggers` (array of strings) — flat list of multilingual keyword stems (English, Korean, Japanese, Chinese, German, French, Spanish, Russian, Arabic) whose presence in user input may indicate memory intent. Word-level matching is heuristic; the agent uses surrounding context.
  - `triggers_by_language` (object) — same keywords keyed by ISO 639-1 language code, for callers that want per-language access.
  - `required_resolution` (array) — the four valid resolutions: `memory_write`, `promotion_proposal`, `associative_marker`, `explicit_non_save`. The `associative_marker` path covers the round-trip "user asks to remember; later asks 'what did I ask to remember?'" case where the substance already lives in another durable artifact and only a navigation breadcrumb is needed; see `agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md` §Memory Intent Rule for when each path applies.
  - `resolution_details` (object) — explanation for each resolution kind.
  - `prohibited` (string) — what is not allowed (verbal acknowledgment without a real resolution).
  - `runtime_selects_memory` (boolean, always `false`).
  - `runtime_selects_candidates` (boolean, always `false`).
  - `runtime_boundary` (string) — note that runtime surfaces the reminder; the agent judges intent presence and resolution path.

---

### `agentlaw_session_save`
Implements the Canonical Save Route in `MEMORY_AND_CONTINUITY_RULES.md`.

Usage note: call this at material session boundaries such as milestones, important decision changes, handoffs, or before stopping work. Do not use it as a frequent heartbeat after small edits or routine verification.

**Parameters**

All working-set fields below carry the **current snapshot only** — each entry is one or two short lines. Substantive session narrative (Finding/Evidence/Resolution prose, decision rationale, change history) does not belong in working-set fields; it belongs in the `log_entry` body. See `agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md` §Working Set Field Discipline for the full rule and concrete examples.

- `current_goal` (string, required) — one or two sentences describing the session's working goal. No paragraph-length narrative.
- `current_decisions` (array of strings, optional) — short list of standing decisions. Each entry is one short sentence stating a current state, not the rationale path that produced it.
- `open_questions` (array of strings, optional) — short list of open issues. Each entry one short sentence.
- `next_actions` (array of strings, optional) — short list of next steps. Each entry one short sentence.
- `authority_warnings` (array of strings, optional) — short list of standing warnings. Each entry one short sentence.
- `log_entry` (object, optional) — `{ kind, title, body, tags? }`. When provided, appended to today's log file via `memory_append_log` semantics. **Substantive session narrative belongs here, not in the working-set fields above.**
- `investigation_log_gap` (object, optional) — agent-supplied turn-level signal for the §Read Routing Criterion / §Log Write Criterion review. Recommended shape: `{ turns_with_substantive_reads, turns_with_reads_but_zero_writes, flagged_turns: [string] }`. The MCP server cannot observe turn boundaries itself; when omitted the response returns a stub with a note. See `agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md` §Write Discipline for what counts as a "flagged turn".

**Returns**
- `working_set_updated_at`.
- `log_entry_id` (when log_entry was provided).
- `save_status` — `saved` or `saved_with_degraded_log`.
- `verification_required` — always `true` in V1. The tool surfaces the post-save verification obligation but does not run project verification commands.
- `verify_hint` — concise caller guidance for the verification command.
- `memory_write_summary` — `{ total_writes, log_writes_by_kind, item_writes_by_type, zero_write_warning, lookback_since, lookback_fallback_used }`. Counts memory writes since the previous working-set `updated_at`; falls back to a 24-hour window when no prior timestamp is readable. `zero_write_warning` is non-null when the count is zero, with a pointer to the §Log Write Criterion. Histograms are keyed by what the DB tracks (log kind, item type), not by tool name.
- `skill_lifecycle_summary` — bounded lower-authority telemetry summary: `{ available, skills_total, stale_after_days, stale_candidate_count, top_stale_candidates, authority }` when telemetry tables are available, or `{ available: false, degraded }` when they are not. This field is advisory evidence for retrospective routing and never authorizes skill mutation.
- `investigation_log_gap` — echoes the agent-supplied dict when provided, or returns a stub `{ turns_with_substantive_reads: null, turns_with_reads_but_zero_writes: null, flagged_turns: [], note: "..." }` when omitted.
- `promotion_reminder` (object) — save-time reminder of the agent-applied promotion protocol. The dict is forward-looking; runtime never selects promotion candidates. Sub-keys:
  - `required_when` (array) — the three properties that must all be true for a promotion proposal: `durability`, `future_operational_relevance`, `authority_gap`.
  - `required_when_explanation` (object) — one-line explanation for each property.
  - `negative_cases` (array) — examples of when NOT to propose promotion (routine progress updates, one-off instructions, etc.).
  - `agent_action` (string) — the call to make when criteria are met.
  - `source_id_rule` (string) — id provenance — only ids returned by other memory tools may be used.
  - `source_ids_available` (object) — any source ids surfaced by this save (typically `{log_entry_id: "..."}` when `log_entry` was provided).
  - `runtime_selects_candidates` (boolean, always `false`).
  - `runtime_boundary` (string).

- `memory_intent_reminder` (object) — save-time reminder of the Memory Intent Rule. Same schema as the `memory_intent_reminder` field on the `agentlaw_session_restore` packet (see Read Tools section above).
- `plan_discipline_reminder` (object) — save-time planning workflow reminder. Same schema as the `plan_discipline_reminder` field on the `agentlaw_session_restore` packet (see Read Tools section above).
- `friction_capture_reminder` (object) — save-time reminder of the Immediate Friction Capture rule. Same schema as the `friction_capture_reminder` field on the `agentlaw_session_restore` packet (see Read Tools section above).
- `degraded` and `log_error` when the working set was written but a requested log append failed.
- `runtime_status`.
- `diagnostics` — `{ duration_ms, stages }`. `stages` is an ordered list of `{ name, duration_ms }` for save substeps such as prior working-set read, working-set write, optional log append, write-summary calculation, skill lifecycle summary, and runtime status assembly.

---

## Operational Tools

### `memory_runtime_check`
Preview memory runtime repair from canonical Markdown without mutating runtime state.

**Parameters**
- `scope` (string, optional, default `all`) — `all` / `items` / `logs`.

**Returns**
- `items_indexed`, `logs_indexed`, `chunks_generated`, `embeddings_generated`.
- `check_only` — always `true`.
- `duration_ms`.
- `runtime_status`.

Use before repair when source drift is suspected and the agent needs counts/warnings only. This operation parses canonical Markdown and reports what repair would process, but does not create a job row or mutate DB, FTS, vector, or embedding metadata tables.

---

### `memory_runtime_repair`
Repair derived DB/chunk/FTS state and item vector rows from canonical Markdown.

**Parameters**
- `scope` (string, optional, default `all`) — `all` / `items` / `logs`.

**Returns**
- `items_indexed`, `logs_indexed`, `chunks_generated`, `embeddings_generated`.
- `check_only` — always `false`.
- `job_id`, `job_status`.
- `duration_ms`.
- `runtime_status`.

**Conflict**
- When another runtime repair job is `queued` or `running`, returns `error.code = memory.runtime_repair_conflict` with `active_job`, `active_job_id`, `active_status`, `active_scope`, and `status_tool = memory_runtime_repair_status`. The caller should poll the active job instead of starting another repair.

Use after schema migration, after embedding model swap, or when source drift is detected. This operation does not rebuild canonical memory; it repairs derived DB, FTS, and item vector state from canonical Markdown. Log vector rows are not generated in v1; log/all repair removes accidental log vector rows while preserving log records and log chunks. Runtime repair is globally single-flight, and source Markdown is parsed only after the repair job guard is acquired.

---

### `memory_runtime_repair_start`
Start a memory runtime repair job and return immediately.

**Parameters**
- `scope` (string, optional, default `all`) — `all` / `items` / `logs`.

**Returns**
- `job_id`.
- `status` — initially `queued`.
- `operation` — `runtime_repair`.
- `scope`.
- `status_tool` — `memory_runtime_repair_status`.
- `runtime_status`.

**Conflict**
- Same `memory.runtime_repair_conflict` shape as `memory_runtime_repair`.

Use when model-backed runtime repair may exceed the agent host's MCP tool-call timeout.

---

### `memory_runtime_repair_status`
Return status and result for a memory runtime repair job.

**Parameters**
- `job_id` (string, required).

**Returns**
- `id`, `operation`, `scope`, `check_only`.
- `status` — `queued` / `running` / `succeeded` / `failed`. A queued/running job that has exceeded the stale threshold is converted to `failed` with `error_code = memory.job_stale`.
- `requested_at`, `started_at`, `finished_at`.
- `result` when succeeded.
- `error_code`, `error_details` when failed.
- `runtime_status`.

---

### `memory_health_check`
Report runtime status.

**Parameters** — none.

**Returns**
- `mcp_server` — `running` / `degraded` / `unavailable`.
- `meta_db` — `ready` / `missing` / `corrupt` / `migration_required`.
- `fts_index` — `ready` / `missing` / `rebuilding` / `stale`.
- `vector_index` — `ready` / `missing` / `rebuilding` / `stale`.
- `embedding_model` — `loaded` / `on_disk` / `incomplete` / `missing`. `loaded` means the model is in the MCP server's in-memory cache and has completed one warmup encode, so search can use it without first-inference latency. `on_disk` means the files are fully present but the model is not inference-ready yet (for example, startup warmup is still loading, disabled for diagnostics, failed, or the server is in a read-only phase). `incomplete` means files exist but the set is partial — run `agentlaw init` (without `--skip-model`) to repair. `missing` means no model directory.
- `embedding_model_on_disk` — `present` / `incomplete` / `missing`. Separates the file-presence signal from the in-memory signal above, so a client can tell whether a `missing`-on-memory state is caused by missing files or a failed load.
- `embedding_model_load` — observable per-process load state without starting a model load: `{ status, elapsed_ms, last_error }`, where status is typically `not_started`, `loading`, `loaded`, `failed`, or `missing`. `loaded` means the MCP parent can use the worker for inference, proven by a parent-side readiness encode round trip. `failed` includes isolated worker spawn failures, startup timeouts, worker exits, readiness-validation failures, and model-load failures. Default hybrid search must not start a cold model load; it returns FTS results with a degraded note until the model is loaded.
- `schema_version` (integer).
- `kit_version` (string).
- `embedding_runtime` — object containing `model_id`, `dimension`, `status`, `last_rebuild_at`, and `last_error` for the active vector-index contract.

**Offline inference by design**: the MCP server sets `HF_HUB_OFFLINE=1`, `TRANSFORMERS_OFFLINE=1`, `HF_HUB_DOWNLOAD_TIMEOUT=5`, and `HF_HUB_DISABLE_TELEMETRY=1` at entry-point top and passes `local_files_only=True` to the embedding loader, so no Hugging Face Hub contact occurs while serving requests. The initial model download is a separate path handled by `agentlaw init`. If a manual rebuild against Hub is ever needed, set these env vars externally before invoking `run-mcp`.

---

## Skill Lifecycle Tools

### `agentlaw_skill_event_record`
Record one explicit skill lifecycle event in lower-authority runtime
telemetry. Use when an agent actually invokes, applies, considers, or reviews a
skill route and wants future retrospective decisions to have mechanical
evidence. Do not use this tool to store full prompts, secrets, private
transcripts, or to authorize skill mutation.

**Parameters**
- `skill_name` (string, required) — skill name or directory-style identifier; normalized to a stable lowercase slug.
- `event_kind` (string, required) — one of `reminded`, `considered`, `invoked`, `applied`, `succeeded`, `failed`, or `stale_reviewed`.
- `outcome` (string, optional, default `unknown`) — one of `unknown`, `useful`, `neutral`, `failed`, or `skipped`.
- `gain` (number, optional) — small caller-supplied usefulness signal. It is an aggregate hint, not a reward model.
- `source` (string, optional, default `agent_declared`) — one of `agent_declared`, `hook_reminder`, `session_save`, `manual_review`, or `test`.
- `evidence` (string, optional) — short evidence text or id, capped by runtime. Full prompts and secrets are prohibited.
- `session_id` (string, optional) — related session id when available.
- `tags` (array of strings, optional).
- `strict_known_skill` (boolean, optional, default `false`) — when true, unknown skills are rejected after discovery; when false, explicit events can create a registry row for externally provided skills.

**Returns**
- `event_id`.
- `skill_name` — canonical normalized skill name.
- `event_kind`, `occurred_at`, `source`, `outcome`.
- `runtime_status`.

**Errors**
- `unknown_skill` when `strict_known_skill=true` and discovery/registry has no matching skill.
- `invalid_event_kind`, `invalid_source`, or `invalid_outcome` for invalid closed-set values.

### `agentlaw_skill_lifecycle_report`
Return read-only skill lifecycle telemetry: invocation count, last-used
timestamp, outcome/gain aggregates, score, and stale-skill candidate status.
Stale candidates are advisory only and do not authorize archive, quarantine,
deletion, creation, or file edits.

**Parameters**
- `stale_after_days` (integer, optional, default `30`) — age threshold for stale-candidate calculation.
- `include_events` (boolean, optional, default `false`) — include a bounded list of recent event rows per skill.
- `skill_name` (string, optional) — limit the report to one normalized skill.

**Returns**
- `generated_at`.
- `stale_after_days`.
- `retention_review_days` — default lifecycle event retention-review threshold, currently `180`.
- `skills_total`.
- `stale_candidate_count`.
- `entries` — per-skill objects containing `skill_name`, `display_name`, `source_paths`, `invocation_count`, `last_used_at`, `event_counts`, `outcome_counts`, `gain_total`, `score`, and `stale_candidate`.
- `authority` — reminder that telemetry is lower-authority runtime evidence.
- `runtime_status`.

**Errors**
- `stale_after_days_must_be_non_negative`.

---

### `agentlaw_authority_lookup`
Return the authority sources an agent should consult before a named action class. Distinguishes memory recall from law/protocol/contract recall: memory is lower authority and is referenced via `memory_lookup` guidance; the binding sources for the action live in `repository_authority_sources`.

**Parameters**
- `action` (string, required) — action class such as `planning`, `plan_review`, `fix`, `commit`, `release`, `memory_read`, `memory_write`, or `general`.
- `detail` (string, optional) — free-form note from the host describing the specific situation; preserved in the response but does not change routing.

**Returns**
- `matched_action` — canonical action key the lookup resolved to.
- `unknown_action` (boolean) — `true` when the requested action did not match a known class and the response fell back to the `general` defaults; `false` otherwise.
- `memory_lookup` — guidance object describing the recommended `memory_search` queries plus an `authority_warning` reminding the agent that memory is below law/protocol/contract.
- `repository_authority_sources` — array of `{ path, authority_level, reason }` entries naming the binding sources to read.
- `runtime_selects_final_authority` (boolean) — always `false`; the agent decides which sources to actually open.
- `warnings` — array of advisory strings; populated when the host should be cautious (for example unknown action classes).

**Errors**
- None at the tool boundary; an unknown action returns the `general` defaults plus `unknown_action: true` rather than an error.

---

### `agentlaw_plan_review_session_lookup`
Look up plan-review session ids by repo-relative plan path. This is a
read-only tool for hosts that need the session id for lifecycle operations
after a restart or handoff. Use it instead of querying `.agentlaw/index/meta.db`
directly.

**Parameters**
- `plan_path` (string, required) — repo-relative POSIX path stored on the review session.
- `include_archived` (boolean, optional, default `false`) — when `false`, returns only non-archived sessions. When `true`, archived rows are included.
- `limit` (integer, optional, default `10`) — maximum rows to return, clamped to the range `1..100`.

**Returns**
- `plan_path` — normalized input path with backslashes converted to `/`.
- `found` — `true` when at least one matching row is returned.
- `session_count` — number of returned rows.
- `include_archived` and `limit` — effective query controls.
- `sessions` — newest-first session rows. Alongside identity, phase, persona,
  hash, and timestamp fields, each row carries `review_stage`,
  `blocked_reason`, `last_successful_transition`, and
  `next_action` so hosts can recover without reading SQLite directly.
- `recovery_status` — present on include-archived misses. `relocation_candidates_found` means exact path lookup missed, but archived sessions with matching plan name and/or content hash were found; `no_relocation_candidates` means no bounded candidate matched.
- `relocation_candidates` — present when `recovery_status=relocation_candidates_found`; array of lookup-row payloads plus `candidate_reason`, `confidence` (`hash_and_name`, `hash`, or `name`), and `requested_plan_path`.
- `next_actions` — present with relocation candidates. Hosts should prefer lookup on the original plan path, reconcile, and oracle continuation over raw SQLite inspection or path-only judgment.

**Errors**
- `invalid_limit` when `limit` is not integer-like.

---

### `agentlaw_plan_review_session_start`
Open a persona-review-loop session for a plan in `agentlaw_docs/plans/active/` or `agentlaw_docs/plans/draft/`. Completes bounded preflight work before atomically creating the initialized `plan_review_session` row, then returns the first interview question plus the clarity threshold the host must meet before persona review begins. Retrying the same request while its untouched initial session is active returns that session instead of creating a duplicate.

**Parameters**
- `plan_path` (string, required) — repo-relative POSIX path to the plan markdown file.
- `intent_text` (string, required) — short restatement of the user's ask. Used for preflight analysis and identical-retry detection; it does not gate any transition.
- `selected_personas` (array of strings, optional) — explicit initial persona roster. When omitted, the initial roster and queue contain only Trigger Coverage Verifier. Structured Domain Coverage and sensitive task-class requirements are derived from `persona-section-map.md` and persisted as candidates, but candidates do not enter the roster or queue until Deep Review Selection records `selected`. Trigger prose is not used as a selector.
- `round_cap` (integer, optional, default 20) — maximum number of rounds before the session is forced into `stalled`.

**Returns**
- `session_id` — opaque id of the form `session/plan-review/<uuid4>`.
- Plan-review lifecycle tools accept either the canonical `session/plan-review/<uuid4>` value or its raw UUID alias as input; successful responses and persisted records always use the canonical full value.
- `phase` — always `interview` on success.
- `round_number` — always `0` on success.
- `selected_personas` — durable review roster for the session.
- `current_round_queue` — personas scheduled in the current round.
- `persona_candidates` — callable structured requirements derived from the current plan.
- `undispositioned_persona_candidates` — candidates that still require a Deep Review Selection decision.
- `persona_requirement_dispositions` — persisted `selected` or reasoned `not_applicable` decisions bound to the current candidate fingerprint.
- `pending_persona_requirements` — selected callable requirements without submitted review evidence.
- `domain_coverage_marks` — identifiers parsed from the current plan body's `## Domain Coverage` section, refreshed after plan changes.
- `clarity_threshold` — the value persona-review entry will compare against (default `0.995`).
- `ambiguity_threshold` — compatibility echo equal to `1 - clarity_threshold`.
- `first_interview_question` — a starter prompt the host can use; the host may substitute its own wording in the conversation.
- `round_cap` — echoes the round cap that will gate the loop.
- `review_stage`, `transition_history` — current lifecycle stage and bounded
  initial transition evidence.
- `replayed_existing_session` — `true` when an identical retry returns the untouched active session created by the original request.
- `diagnostics.stage_durations_ms` — bounded timings for preflight stages and, on a fresh creation response, the session INSERT. Diagnostic timing does not govern review state.

**Errors**
- `plan_outside_active_or_draft` when the path is not under `agentlaw_docs/plans/active/` or `agentlaw_docs/plans/draft/`.
- `plan_not_found` when the file does not exist on disk.
- `active_session_exists` when a non-archived session exists but the request identity differs or the session has already advanced.
- `plan_review.session_start_preflight_failed` when bounded initialization fails before persistence; includes `failed_stage` and leaves no active row.
- `plan_review.write_conflict_retry_later` when SQLite cannot acquire the write lock within its configured busy timeout; leaves no partial row.

---

### `agentlaw_plan_review_finding_transition`
Transition one finding after synthesis, verification, explicit risk acceptance,
or false-positive adjudication.

**Parameters**
- `session_id`, `finding_id`, `target_state`, and `reason` are required.
- `target_state` is `open`, `amend_pending_verification`, `resolved`,
  `accepted_risk`, or `false_positive`, subject to the current-state transition
  table.
- `user_authorization` is required when `target_state=accepted_risk`.

**Returns**
- The updated `lifecycle_state` and remaining
  `open_substantive_finding_ids`.

**Errors**
- `finding_not_found` or `invalid_finding_transition`.

---

### `agentlaw_plan_review_synthesis_submit`
Apply one integrated amendment for every open substantive finding. Do not use
it for independent persona edits. Call it after
`agentlaw_plan_review_round_check` returns `synthesis_required`; successful
application opens a section-scoped targeted review.

**Parameters**
- `session_id`, `expected_plan_hash`, `alternatives`, `dispositions`, and
  `amendment_ops` are required. Every open finding must have exactly one
  disposition, and every amendment operation names its `finding_ids`.

**Returns**
- The new plan hash, changed sections, targeted reviewers, and next reviewer.

**Errors**
- Rejects non-synthesis stages, stale hashes, incomplete or invalid
  dispositions, unauthorized risk acceptance, failed operations, and failed
  post-apply structural preflight without partially changing the plan. The
  synthesis check uses the review-entry preflight because `Plan reviewed`,
  `Personas applied`, `Separate Persona Review Passes`, and `Revised after
  review` are completed only after targeted re-review. Session finalize remains
  responsible for the full active-plan readiness and review-evidence check.

Before calling, read `agentlaw_docs/planning-protocol/review-method.md`,
`agentlaw_docs/law/PLANNING_AND_REVIEW_RULES.md`, and this contract.

---

### `agentlaw_plan_review_outcome_sufficiency_submit`
Decide whether the revised plan still connects the user need, selected design,
work units, verification, and expected result. Do not use it before all
substantive findings close. Call it after targeted review; PASS permits
Self-Challenge, while strengthening reopens synthesis.

**Parameters**
- `response` contains `verdict` and cited `causal_chain` entries.
- Reviewer provenance fields follow the persona-review provenance contract.

**Returns**
- The resulting review stage, improvement-quality report, and
  `idempotent_replay`. An exact retry of a successful response and reviewer
  provenance returns the stored success; a conflicting retry remains rejected.

**Errors**
- Rejects wrong stage, missing reviewer provenance, incomplete causal
  evidence, or quality evidence below the improvement threshold.

Before calling, read `agentlaw_docs/planning-protocol/review-method.md`,
`agentlaw_docs/law/PLANNING_AND_REVIEW_RULES.md`, and this contract.

---

### `agentlaw_plan_review_execution_entry_check`
Confirm that reviewed source, protocol, and runtime signals are still current
before implementation starts. Do not use it as a post-implementation oracle.
Call it after session finalization and before editing source.

**Parameters**
- `session_id` identifies the review session.

**Returns**
- `execution_entry_ready: true` with the current fingerprint, or a drift report
  listing changed signals and affected plan sections.

**Errors**
- Rejects missing sessions, sessions without a baseline, and sessions whose
  renewed review has not been finalized.

Before calling, read `agentlaw_docs/planning-protocol/review-method.md`,
`agentlaw_docs/law/PLANNING_AND_REVIEW_RULES.md`, and this contract.

---

### `agentlaw_plan_review_execution_entry_refresh`
Recover a finalized review when `agentlaw_plan_review_execution_entry_check`
reports source, protocol, or runtime drift. The caller must refresh and
acknowledge every affected section reported by the latest check. The tool
reopens only reviewers mapped to those sections, clears stale outcome and
Self-Challenge readiness, and keeps execution blocked until the renewed review
is finalized and the execution-entry check passes.

**Parameters**
- `session_id` identifies the session in `review_refresh_required`.
- `refreshed_sections` must exactly match the currently affected sections.
- `refresh_summary` states what was re-read and why the existing plan remains
  sufficient or needs no broader review.

**Returns**
- The affected sections, targeted reviewer queue, durable review roster,
  refreshed baseline fingerprint, audit-log id, and next review action.

**Errors**
- Rejects missing sessions, non-pending refresh states, placeholder summaries,
  missing or extra section acknowledgements, missing section maps, and refreshes
  that produce no callable reviewer.

Before calling, read `agentlaw_docs/planning-protocol/review-method.md`,
`agentlaw_docs/law/PLANNING_AND_REVIEW_RULES.md`, and this contract.

---

### `agentlaw_plan_review_interview_provenance_repair`
Repair only a finalized interview turn's provenance after validating the exact
stored answer against a memory log. Do not use it to edit answers, scores,
findings, or review decisions. Call it only when valid evidence exists but the
stored source marker is missing or wrong.

**Parameters**
- `session_id`, one-based `turn_number`,
  `user_answer_memory_log_entry_id`, and a non-empty `reason` are required.

**Returns**
- The repaired source fields and append-only audit-log id; an already-correct
  repair returns an idempotent result.

**Errors**
- Rejects non-finalized sessions, changed plan hashes, missing turns, missing or
  mismatched logs, invalid timestamps, and inadequate reasons.

Before calling, read `agentlaw_docs/planning-protocol/review-method.md`,
`agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md`, and this contract.

---

### `agentlaw_plan_review_interview_answer_submit`
Submit one interview turn: the user's answer text, the host's clarity scores per dimension, and a one-line opposite scenario describing how the scoring could be wrong. Transitions phase to `interview_self_verify`.

**Parameters**
- `session_id` (string, required).
- `user_answer` (string, required, non-empty after stripping whitespace).
- `clarity_scores` (object, required) — keys among `goal`, `constraint`, `success`, `context`. Values must be in `[0, 1]`. Presence of `context` triggers brownfield weighting; otherwise greenfield weights apply.
- `opposite_scenario` (string, required, non-empty after stripping whitespace) — host's own counterfactual describing how the scoring could be wrong.
- `user_answer_memory_log_entry_id` (string, optional) — id of a `memory_logs` row that contains the user's answer. When supplied, the tool resolves the id via `memory_get`, requires the entry's body to contain `user_answer` verbatim, and requires the entry's `recorded_at` to be at least 5 seconds AFTER the plan's authoring timestamp (the plan body's `- Date authored:` line, accepting plain, backticked, or quote-wrapped ISO/date values, with bounded git first-commit fallback when absent or unparseable) as anti-self-fabrication friction. Supplying it earns the `memory-log` source marker on the interview turn; omitting it records the `llm-self-submitted` marker that the verifier surfaces as a warning during consistency checks.

**Returns**
- `session_id`, `phase` (always `interview_self_verify` on success).
- `clarity` — weighted clarity from the submitted scores using greenfield or brownfield weights.
- `min_axis_clarity` — the lowest scored required dimension.
- `clarity_threshold` — echoed for convenience; persona review requires both `clarity` and `min_axis_clarity` to meet it.
- `raw_ambiguity`, `ambiguity_calibration_floor`, `ambiguity`, `ambiguity_threshold` — compatibility fields derived from clarity.
- `next_action_message` — short instruction telling the host what to do next.

**Errors**
- `wrong_phase` when the session is not in `interview`.
- `user_answer_too_short` for a blank answer.
- `opposite_scenario_too_short` for a blank opposite scenario.
- `scores_out_of_range` when any clarity dimension is outside `[0, 1]` or non-numeric.
- `session_not_found` when the id does not match any row.
- `user_answer_memory_log_entry_not_found` when the supplied id resolves to no `memory_logs` row.
- `user_answer_not_in_memory_log_body` when the resolved entry's body does not contain `user_answer` verbatim (case-sensitive substring).
- `user_answer_memory_log_timestamp_unparseable` when the entry's `recorded_at` is not ISO 8601 parseable.
- `user_answer_memory_log_not_old_enough` when the entry's `recorded_at` is less than 5 seconds after the plan's authoring timestamp (including the entry-predates-plan case where the gap is negative). Carries `gap_seconds`, `required_gap_seconds`, `recorded_at`, and `plan_authoring_timestamp` for diagnostics.
- `plan_authoring_timestamp_not_derivable` when neither the plan body's accepted `- Date authored:` value nor the bounded git first-commit fallback yields a parseable timestamp. Carries `plan_path` and a `detail` message.

---

### `agentlaw_plan_review_interview_self_verify_submit`
Confirm or reject the host's own clarity scoring. `verdict='pass'` plus weighted and per-axis clarity at or above `0.995` advances to persona review; `re-score` or below-threshold clarity returns to `interview` for another question round.

**Parameters**
- `session_id` (string, required).
- `verdict` (string, required) — `pass` or `re-score`.
- `reason` (string, required, non-empty after stripping whitespace) — short justification for the verdict.

**Returns**
- `session_id`, `phase` (`persona_review` on advance, `interview` otherwise).
- `round_number` — `1` when persona review begins.
- `current_persona` — first selected persona on advance, otherwise null.
- `clarity`, `min_axis_clarity`, `clarity_threshold` — last recorded gate values for the session.
- `ambiguity`, `ambiguity_threshold` — compatibility echoes.
- `next_action_message` — set when the session returns to interview.

**Errors**
- `wrong_phase` when the session is not in `interview_self_verify`.
- `invalid_verdict` for any value other than `pass` or `re-score`.
- `reason_too_short` for a blank reason.
- `session_not_found` when the id does not match any row.

---

### `agentlaw_plan_review_batch_manifest`
Return the current persona-review round manifest for host-run subagent fan-out.
The MCP server does not call models or spawn agents; it exposes session state,
remaining persona order, deck mandates, and the accepted finding payload shape.
The default review path is subagent review. Self-review is allowed only when the
user requested it or subagents cannot be called.

**Parameters**
- `session_id` (string, required).

**Returns**
- `session_id`, `phase`, `round_number`, `plan_path`, `plan_content_hash`.
- `selected_personas` (durable roster), `current_round_queue`, `current_persona`, `remaining_personas`, and `pending_persona_requirements`.
- `persona_candidates`, `undispositioned_persona_candidates`, and `persona_requirement_dispositions` expose the complete selector input and persisted decisions without scheduling every candidate.
- `persona_deck_payload` — deck entries for `remaining_personas`.
- `finding_schema` — compact shape expected by submit tools.
- `host_orchestration` — states that reviewer execution belongs to the host, default review provenance is `subagent`, and MCP remains the state machine. It includes reviewer-effort policy version 2, task-capability guidance, a server-derived `review_start_disclosure.disclosure_id`, adaptive-concurrency guidance, and `revision_rereview_guidance`. The guidance chooses capability from the review task's hardest material judgment: deterministic format/mapping/schema checks prefer a fast cost-efficient capability, ordinary semantic work prefers a balanced capability, and architecture, security, concurrency, data-loss, irreversible-action, or external-contract judgment uses `deep` with a reason. Persona names do not determine tiers; mixed tasks use the highest materially required class. Before reviewer work starts, the host reports the actual model or model class, reasoning effort, fallback status, and selected concurrency to the user. These remain host assertions unless provider or runtime telemetry verifies them; user acknowledgment does not attest accuracy or gate readiness. Concurrency is an LLM judgment based on the current host's observable capability and policy; shared artifacts do not prescribe a provider-specific or fixed numeric limit.

**Errors**
- `wrong_phase` when the session is not in `persona_review`.
- `state: "plan_body_changed"` when the plan file's hash differs from the session's recorded hash.
- `session_not_found` when the id does not match any row.

---

### `agentlaw_plan_review_finding_submit`
Submit one persona's finding. Verifies the persona matches the current slot, the mandate quote matches the deck verbatim, the line citations match the plan body byte-for-byte, and the severity is allowed. Advances `current_persona` or transitions to `persona_review_round_check` after the last persona of the round. When Trigger Coverage finishes and structured candidates remain undecided, the transition enters `review_stage=deep_review_selection` without adding those candidates to the roster or queue.

**Parameters**
- `session_id` (string, required).
- `persona` (string, required) — must equal the session's `current_persona`.
- `mandate_quote` (string, optional validation echo) — when supplied, it must match the persona mandate after whitespace canonicalization. The server derives and stores the canonical mandate when omitted.
- `finding_text` (string, required, non-empty after stripping whitespace). Must satisfy §Substance Enforcement in `agentlaw_docs/planning-protocol/review-method.md`: `severity=must-change|should-change` requires non-placeholder `Evidence`, `Plan risk`, `Required plan change`, and `Verification`; `severity=note` requires the literal `PASS` token and any declared coverage reference.
- `plan_line_citations` (array of objects, required) — each object requires `line: integer` (1-indexed). `quote` is optional; when supplied it must match the normalized plan body. The server derives and stores the canonical quote.
- `severity` (string, required) — `must-change`, `should-change`, or `note`.
- `review_source` (string, required) — `subagent`, `self_review_user_requested`, or `self_review_subagent_unavailable`.
- `reviewer_agent_id` (string, required when `review_source: subagent`) — spawned reviewer id or equivalent run id.
- `self_review_reason` (string, required when `review_source` is a self-review fallback) — records why self-review is allowed.
- `reviewer_effort` (object, required when `review_source: subagent`) — bounded persona/run identity, tier, actual model or model class, provider-neutral model class, reasoning effort, and any escalation or capability-fallback reason.
- `batch_effort_evidence` and `review_start_disclosure` (objects, required when `review_source: subagent`) — one-reviewer batch metrics and the manifest-derived disclosure id, displayed values, channel, and ordering timestamps. The server derives policy/session/plan identity and stores host observations as `attestation_status: host_asserted`.
- `policy_version_echo` (integer, optional) — stale-client check only; the server owns the canonical policy version.
- `exceptional_confirmation_id` (string, required for `exceptional`) — server-owned confirmation bound to the same plan.
- `amend_proposal` (array of objects, optional) — each op `{ type: "insert_after" | "replace" | "delete", target: { section_header: string, line_offset_in_section: integer }, content: string, rationale: string }`. Required content (`content` non-blank) for `insert_after` / `replace`. Missing on must-change / should-change yields a non-blocking `amend_proposal_missing_legacy_warning` field while the parameter remains optional.

**Returns**
- `session_id`, `phase` (`persona_review` while personas remain, `persona_review_round_check` after the last persona).
- `round_number`, `current_persona` (next persona in the round, null at round boundary).
- `next_action_message` — set when the round boundary is reached.
- `transition_payload` — present when Deep Review Selection is required. It echoes current `domain_coverage_marks` and persisted candidates, returns an opaque `host_submission_capability`, and directs the parent host to `agentlaw_plan_review_deep_review_selection_submit`. Candidates remain unscheduled until that tool records complete decisions. The capability is not returned by session lookup or batch manifest and must not be passed to selector subagents.

**Errors**
- `wrong_phase` when the session is not in `persona_review`.
- `persona_mismatch` when the submitted persona is not the current slot. The response includes the expected and submitted names.
- `persona_not_in_deck` when the persona name is not parseable from the deck files.
- `mandate_not_in_deck` when the canonicalized quote does not match the deck mandate. The response includes the deck paths and the deck mandate text.
- `line_out_of_range` and `line_quote_mismatch` for citation errors. The response carries a `details` array listing every citation failure.
- `invalid_severity` when severity is not in the allowed set.
- `finding_text_too_short` for a blank finding.
- `finding_text_below_prescriptive_mandate` when the Substance Enforcement shape is unmet. Response carries `details` naming missing or placeholder fields and a `hint` pointing at the Substance Enforcement section.
- `review_provenance_required` when `review_source` is omitted.
- `invalid_review_source` when `review_source` is not one of `subagent`, `self_review_user_requested`, or `self_review_subagent_unavailable`.
- `reviewer_agent_id_required` when `review_source` is `subagent` and no reviewer id is supplied.
- `self_review_reason_required` when a self-review fallback omits the reason.
- `reviewer_effort_policy_version_mismatch`, `reviewer_effort_evidence_required`, `review_start_disclosure_required`, `review_start_disclosure_order_invalid`, `selected_concurrency_exceeds_observed_safe_cap`, and related structured evidence errors are returned before mutation.
- `amend_proposal_invalid` when the amend op list is malformed. Response carries `details` enumerating each invalid op.
- `state: "plan_body_changed"` when the plan file's hash differs from the session's recorded hash. Response carries `current_hash`, `stored_hash`, and `next_actions` pointing to `agentlaw_plan_review_session_invalidate` and `agentlaw_plan_review_session_reconcile`.
- `session_not_found` when the id does not match any row.

**Success-payload additions**
- `transparency_echo` — `[round R / Persona / severity / first 80 chars of finding_text / amend_ops=N]` line for inline display in the user-facing channel.
- `amend_proposal_missing_legacy_warning` — present when severity is must-change / should-change and `amend_proposal` was omitted while the parameter remains optional. When the server schema requires `amend_proposal`, omission is rejected instead of returning this warning.

---

### `agentlaw_plan_review_batch_findings_submit`
Submit an ordered batch of persona findings collected from host-run subagents.
The complete batch and its effort/disclosure evidence are validated before all
items run through `agentlaw_plan_review_finding_submit` inside one transaction.
Any invalid item rolls back earlier items, findings, and convergence state. Batch
findings require reviewer provenance. Use `review_source: subagent` with
`reviewer_agent_id` by default. Use `self_review_user_requested` or
`self_review_subagent_unavailable` only with an explicit `self_review_reason`.
New subagent submissions require version-2 evidence. Persisted pre-v2 findings
remain readable; there is no silent legacy submission mode.

**Parameters**
- `session_id` (string, required).
- `findings` (array of objects, required) — ordered finding payloads using the same fields accepted by `agentlaw_plan_review_finding_submit`.
- `batch_effort_evidence` (object, required for subagent findings) — batch id, reviewer count, selected and observed-safe concurrency, peak concurrency, host-capability basis, and bounded start/completion timestamps.
- `review_start_disclosure` (object, required for subagent findings) — manifest-derived disclosure id, batch id, actual model or model class, reasoning effort, fallback status, selected concurrency, user-visible channel, display timestamp, and first-reviewer-start timestamp. Stored values remain `host_asserted` unless provider or runtime telemetry supplies stronger evidence; user acknowledgment is not an accuracy attestation.
- `policy_version_echo` (integer, optional) and `exceptional_confirmation_id` (string, required only for exceptional tier) follow the single-submit contract.

**Returns**
- `session_id`, `phase`, `round_number`, `current_persona`.
- `accepted_count`, `accepted_personas`.
- `next_action_message` — present when the last accepted item reaches a round boundary.
- `transition_payload` — carried through from the first accepted single-submit response that emits it.
- `last_result` — the final successful single-submit response.
- `reviewer_effort_policy_version` and normalized `review_start_disclosure` when subagent evidence is stored.

**Errors**
- `findings_empty` when `findings` is missing, not a list, or empty.
- `finding_payload_not_object` when an item is not an object.
- `review_provenance_required` when a batch item omits `review_source`.
- `invalid_review_source` when `review_source` is not one of `subagent`, `self_review_user_requested`, or `self_review_subagent_unavailable`.
- `reviewer_agent_id_required` when `review_source` is `subagent` and no reviewer id is supplied.
- `self_review_reason_required` when a self-review fallback omits the reason.
- Any error or state response from `agentlaw_plan_review_finding_submit`, enriched with `failed_index`, `accepted_count: 0`, `accepted_personas: []`, and `rolled_back: true` when validation reached the transactional submission phase.

---

### `agentlaw_plan_review_round_check`
Evaluate the just-completed round. Before convergence, refresh structured persona requirements from the current plan. Undispositioned candidates block with `deep_review_selection_required`; selected requirements without evidence reopen `persona_review` at the first pending persona. Only a fully dispositioned and requirement-complete round can advance to synthesis or Outcome Sufficiency. `agentlaw_plan_review_session_finalize` remains the only tool that writes the reviewed block.

**Parameters**
- `session_id` (string, required).

**Returns**
- `session_id`, `phase`, and `review_stage` identify the current review state.
- `synthesis_required: true`, `expected_plan_hash`, and `open_findings` are
  returned when substantive findings require one integrated amendment.
- When no substantive finding remains, `review_stage=outcome_sufficiency` and
  `next_action_message` direct the host to the independent outcome review.

**Errors**
- `personas_pending` when called from `persona_review` while a `current_persona` is still set (host must submit findings or invalidate first).
- `wrong_phase` when called outside `persona_review` and `persona_review_round_check`.
- `session_not_found` when the id does not match any row.

---

### `agentlaw_plan_review_self_challenge_submit`
Record the Self-Challenge response that `session_finalize` requires. There are two response modes:

- **Plan-amending challenge** (`type: "weakest_with_amend"`): name the weakest current finding and provide one `strengthening_amend_op`; `agentlaw_plan_review_session_finalize` applies that edit to the plan before writing reviewed state.
- **No-amend justification** (`type: "full_justification"`): use only when the current plan already covers every persisted must-change / should-change finding. It cites the plan body and explains why no edit is needed; when there are no must-change / should-change findings, it must still include `all_clear_challenge` so the all-clear is a real challenge, not an empty pass.

**Parameters**
- `session_id` (string, required).
- `response` (object, required) — plan-amending challenge: `{ type: "weakest_with_amend", weakest_finding_id: string, weakness_sentence: string, strengthening_amend_op: { type, target, content, rationale } }`. No-amend justification: `{ type: "full_justification", entries: [{ finding_id: string, plan_body_citation: string, justification: string }], all_clear_challenge?: { weakest_review_axis: string, plan_body_citation: string, challenge_question: string, justification: string } }`. The plain string `"none"` is rejected.

**Returns**
- `session_id`, `self_challenge_recorded: true`, `next_action_message`.

**Errors**
- `self_challenge_invalid` with a `details` list naming each missing or invalid field. Subcodes: `self_challenge_missing`, `self_challenge_invalid:plain_none`, `self_challenge_invalid:unknown_type:*`, `plan_amending_missing_*`, `no_amend_entries_not_a_list`, `no_amend_entry_*_missing_*`, `no_amend_entry_*_justification_below_three_sentences`, `no_amend_incomplete_coverage:*`, `no_amend_all_clear_*`.
- `session_not_found` when the id does not match any row.

---

### `agentlaw_plan_review_session_finalize`
Mark the session finalized and write the reviewed state into the plan body. Finalization projects the actual persisted reviewer results into `Personas applied`, `Revised after review`, and `Separate Persona Review Passes`, then writes `Plan reviewed: yes` and `Plan contract hash`. It does not invent evidence for a session with no findings. Finalization recomputes the current structured persona requirements and rejects `required_persona_reviews_pending` when a callable required persona lacks submitted review evidence. It also retains the plan-hash, Review Coverage Matrix, active-plan preflight, and review-evidence readiness gates.

Before writing the reviewed block, finalize re-validates the stored Self-Challenge response against the current persisted findings list. For a plan-amending challenge, finalize first verifies the plan body still matches the stored hash, applies `strengthening_amend_op`, refreshes the stored hash, and then evaluates Review Coverage Matrix and active-plan preflight against the amended body. If that edit cannot be applied, finalize returns `self_challenge_amend_apply_failed` and leaves the session unfinalized. For a no-amend justification, finalize returns `self_challenge_stale_after_new_finding` when the response no longer covers every current must-change / should-change finding or lacks the required all-clear challenge for a no-finding session.

**Parameters**
- `session_id` (string, required).

**Returns**
- `session_id`, `phase` (`finalized`).
- `plan_content_hash` — refreshed hash after the body write.
- `plan_contract_hash` — SHA-256 hash of the reviewed contract sections. The contract hash excludes only explicit mutable evidence/status sections: `Status`, `Plan Review Evidence`, `Separate Persona Review Passes`, `Plan Amendment Authorizations`, `Plan Oracle Evidence`, and `Implementation Deviation Records`.

**Errors**
- `wrong_phase` when the session is neither convergence-pending `persona_review_round_check` nor legacy `finalized`.
- `self_challenge_required` when no Self-Challenge response has been recorded for the session. Response carries a `hint` pointing at `agentlaw_plan_review_self_challenge_submit`.
- `self_challenge_amend_apply_failed` when a plan-amending challenge's `strengthening_amend_op` cannot be applied to the current plan body. Response carries amend markers in `details` and a `next_action_message` instructing the host to repair the target or submit a fresh Self-Challenge response.
- `self_challenge_stale_after_new_finding` when the recorded Self-Challenge response fails validation against the current findings list. Response carries `details` from Self-Challenge validation and `next_action_message` telling the host to re-submit Self-Challenge.
- `review_coverage_matrix_open` when the plan's Review Coverage Matrix is missing, has invalid statuses, contains `needs_user_answer`, lacks required evidence/rationale, or lacks required `crit-*` linkage. Response carries `details` plus `next_action_message` instructing the host to ask the user for unresolved unknowns and update the plan before retrying.
- `active_plan_preflight_incomplete` when the plan would still fail active-plan preflight or review-evidence readiness after the reviewed block is written. Response carries `details` plus `next_action_message` instructing the host to repair the plan and rerun affected review before retrying.
- `state: "plan_body_changed"` when the plan body's current hash differs from the stored hash.
- `state: "plan_not_found"` when the plan file is missing on disk.
- `session_not_found` when the id does not match any row.

---

### `agentlaw_plan_review_session_invalidate`
Archive the current session without persisting findings to the plan. Used when the host decides to start fresh after a plan-body change or a process error. After invalidate, `agentlaw_plan_review_session_start` can open a new session for the same plan path.

**Parameters**
- `session_id` (string, required).

**Returns**
- `session_id`, `phase` (`archived`).

**Errors**
- `already_archived` when the session is already in the archived state.
- `session_not_found` when the id does not match any row.

---

### `agentlaw_plan_review_session_reconcile`
Update the session's `plan_content_hash` to match the current plan body without discarding accumulated findings. Used when the host has re-read a plan body that was edited externally and wants to continue the in-flight review against the new body. For finalized or `oracle_evaluation` sessions that carry `plan_contract_hash`, reconcile rejects reviewed contract-section changes and allows only mutable evidence/status section changes.

**Parameters**
- `session_id` (string, required).

**Returns**
- `session_id`, `phase` (unchanged), `round_number`, `current_persona`.
- `plan_content_hash` — refreshed value.
- `plan_contract_hash` — stored contract hash when present.
- `findings` — preserved finding list.

**Errors**
- `plan_contract_changed` when a finalized or `oracle_evaluation` session carries `plan_contract_hash` and the current plan body's reviewed contract sections no longer match it. Response carries `current_contract_hash`, `stored_contract_hash`, and `next_actions`.
- `already_archived` when the session is in the archived state.
- `plan_not_found` when the plan file is missing on disk.
- `session_not_found` when the id does not match any row.

---

### `agentlaw_plan_review_session_resume`
Resume a stalled session after the user has resolved the stagnation or round-cap condition. Begins a fresh round with `current_persona` reset to the first selected persona and `stagnation_signal` cleared.

**Parameters**
- `session_id` (string, required).
- `user_intervention_note` (string, required, non-empty after stripping whitespace) — short note describing what the user changed before resuming.

**Returns**
- `session_id`, `phase` (`persona_review`).
- `round_number` — the new round (previous + 1).
- `current_persona` — first selected persona, or null when the session has an empty selected list.

**Errors**
- `wrong_phase` when the session is not in `stalled`.
- `user_intervention_note_too_short` for a blank note.
- `session_not_found` when the id does not match any row.

---

### `agentlaw_plan_review_session_abandon`
Archive an in-progress review session without moving the plan file. This is the
recovery path when the user or host ends the current attempt while retaining
the plan artifact. Finalized and oracle-evaluation sessions are not abandonable.

**Parameters**
- `session_id` (string, required).
- `reason` (string, optional) — concise audit reason; a compatibility default is
  used when omitted.

**Returns**
- `session_id`, `phase`, `prior_phase`, `reason`, `audit_log_entry_id`, and
  `idempotent_replay`. Repeating an already successful abandon returns
  `idempotent_replay: true` without another audit event.

**Errors**
- `session_not_abandonable` for finalized or oracle-evaluation sessions.
- `abandon_reason_required` when an explicitly supplied reason is blank.
- `session_not_found` when the id does not match any row.

---

### `agentlaw_plan_review_session_enter_oracle_phase`
Transition a finalized plan-review session into `oracle_evaluation` phase. After this transition, `agentlaw_plan_review_oracle_check` prepares project-owned verification jobs and reports their status. The host runs each returned `agentlaw verify-run` command in a visible project terminal. `agentlaw_plan_review_oracle_user_confirm` records genuinely manual criteria. `agentlaw_plan_archive` permits completion only when every criterion is `pass` or `user_confirmed`.

**Parameters**
- `session_id` (string, required).

**Returns**
- `session_id`, `phase` (`oracle_evaluation`), `next_action_message` (operator hint).

**Errors**
- `wrong_phase` when the session is not in `finalized`.
- `session_not_found` when the id does not match any row.

---

### `agentlaw_plan_review_oracle_check`
Parse the plan body's `crit-*` acceptance criteria and idempotently prepare one persisted job per distinct direct-argv command. This tool never executes project code and never launches a worker. It returns the exact visible command the host must run:

```text
agentlaw verify-run --target <project-root> --job <job-id> -- <project-command> <args...>
```

The project command is opaque argv. Agentlaw does not choose a language runtime, rewrite `python`, inject project dependencies, or invoke a shell. The CLI validates the target, job, reviewed plan hash, and declared argv; claims the job once; runs it from the registered project root with `shell=False`; bounds time and output; terminates the process tree on timeout; and atomically records declared/executed argv, result, cleanup state, output paths, and timestamps. This separation keeps Agentlaw's standalone environment independent from the governed project's toolchain.

Repeated calls return the same live or terminal job for the same plan hash and command. A changed reviewed plan invalidates unfinished jobs. Criteria marked `user_confirms` and criteria without an explicitly extractable direct command remain `pending`. Shell composition and nested shell executables are not accepted as direct argv.
After correcting a failed project result, rerun the returned command with `--retry` before `--target`. Retry is accepted only for failed, errored, interrupted, or cleanup-failed jobs whose target, plan hash, and argv still match; it increments `retry_count` and preserves prior run evidence.

**Parameters**
- `session_id` (string, required).
- `timeout_seconds` (integer, optional; default `60`) — timeout stored in each prepared job.
- `output_cap_bytes` (integer, optional; default `1048576`) — per-stream output cap.

**Returns**
- `session_id`, `phase`, `oracle_last_run_at`, `criteria_total`, `results_summary`, `oracle_results`, `jobs`, and `progress`.
- Each job includes declared argv and `cli_command`. `progress` reports criterion and job counts; archive readiness still comes from per-criterion terminal results.

**Errors**
- `wrong_phase` when the session is not in `oracle_evaluation`.
- `session_not_found` when the id does not match any row.
- `plan_not_found` when the plan body file is missing.
- `no_acceptance_criteria_with_oracle` when the plan body has no `crit-*` bullets carrying an `Oracle:` marker.

---

### `agentlaw_plan_review_oracle_user_confirm`
Record a user confirmation for one acceptance criterion. Used for criteria whose oracle is satisfied by manual review — either because the oracle text contains the `user_confirms` marker, or because `oracle_check` recorded `pending` with reason `no_runnable_command_extracted`. Sets the criterion's status to `user_confirmed` so the archive gate accepts it.

**Parameters**
- `session_id` (string, required).
- `crit_id` (string, required) — must match a key already present in `oracle_results` (so `oracle_check` must have run at least once).
- `confirmation_note` (string, required) — free-text description of what the user verified; non-empty.

**Returns**
- `session_id`, `phase`, `crit_id`, `status` (`user_confirmed`), `results_summary` (refreshed status → count map).

**Errors**
- `wrong_phase` when the session is not in `oracle_evaluation`.
- `session_not_found`, `crit_id_required`, `confirmation_note_required`, `crit_not_in_oracle_results`.

---

### `agentlaw_plan_review_clarification_check`
Run the plan-review-system ambiguity gate against a live session. If ambiguity is above the threshold, the session enters `clarification_pending` and the response returns surfaced questions.

**Parameters**
- `session_id` (string, required).
- `intent_text` (string, required).
- `threshold` (number, optional; default `0.5`).

**Returns**
- `session_id`, `phase`, `requires_clarification`, `ambiguity_score`, `ambiguity_flags`, `surfaced_questions`, `retrieval_hits`.

**Errors**
- `session_not_found` when the id does not match any row.

---

### `agentlaw_plan_review_clarification_answer`
Record the user's answer for a `clarification_pending` session and return the session to the normal `interview` flow.

**Parameters**
- `session_id` (string, required).
- `user_answer` (string, required).
- `answered_questions` (list of strings, optional).

**Returns**
- `session_id`, `phase` (`interview`), `clarification_resolved`, `next_action_message`.

**Errors**
- `wrong_phase` when the session is not in `clarification_pending`.
- `user_answer_too_short` for a blank answer.
- `session_not_found` when the id does not match any row.

---

### `agentlaw_plan_review_preventer_lint`
Run the `plan_review_system` failure-preventer dispatcher against a single-file plan or Pattern 2 directory bundle. Findings are filtered by the plan's declared task classes and recorded in `plan_review_preventer_audit`.

**Parameters**
- `plan_path` (string, required) — repo-relative path under `agentlaw_docs/plans/active/` or `agentlaw_docs/plans/draft/`.
- `session_id` (string, optional).

**Returns**
- `task_classes`, `finding_count`, `findings`, `audit_id`.

**Errors**
- `plan_outside_active_or_draft`.
- `plan_not_found`.

---

### `agentlaw_failure_pattern_check`
Scan correction memory logs for recurring keyword patterns and surface promotion candidates for future preventers.

**Parameters**
- `threshold` (integer, optional; default `3`).
- `limit` (integer, optional; default `20`).

**Returns**
- `threshold`, `log_count`, `hit_count`, `hits`.

**Errors**
- (none).

---

### `agentlaw_plan_review_step_critique_check`
Validate and execute one `verification_tool_call` payload for a step-level finding. In-process tool kinds (`file_exists`, `grep`, `regex_in_file`) execute against the target workspace; `test_run` remains shape-validated for the oracle path.

**Parameters**
- `verification_tool_call` (object, required) — `{kind, args, expected_outcome}`.

**Returns**
- `ok`, `actual_outcome`, `matched_expectation`, `detail`.

**Errors**
- `verification_tool_call_not_a_dict`.
- `verification_tool_call_invalid`.

---

### `agentlaw_plan_review_req_create`
Create the next REQ-N requirement record for a plan.

**Parameters**
- `plan_path` (string, required).
- `statement` (string, required).
- `status` (string, optional; default `active`) — `active`, `withdrawn`, or `superseded`.

**Returns**
- `req_id`, `plan_slug`, `n`, `statement`, `status`.

**Errors**
- `statement_required`.
- `invalid_req_status`.
- `req_id_invalid`.

---

### `agentlaw_plan_review_conf_create`
Create the next CONF-N confirmation record and optionally bind it to existing REQ-N records.

**Parameters**
- `plan_path` (string, required).
- `confirmation_method` (string, required).
- `user_statement` (string, required).
- `confirmed_req_ids` (list of strings, optional).
- `scope_description` (string, optional).
- `applies_to_work_steps` (list of strings, optional).

**Returns**
- `conf_id`, `plan_slug`, `n`, `confirmation_method`, `confirmed_req_ids`, `scope_description`, `applies_to_work_steps`.

**Errors**
- `confirmation_method_invalid`.
- `user_statement_required`.
- `conf_id_invalid`.
- `req_id_invalid`.
- `req_ids_not_found`.

---

### `agentlaw_plan_review_req_bind`
Bind an existing CONF-N confirmation to one existing REQ-N requirement.

**Parameters**
- `conf_id` (string, required).
- `req_id` (string, required).

**Returns**
- `conf_id`, `req_id`, `bound`.

**Errors**
- `conf_id_invalid`, `req_id_invalid`.
- `conf_id_not_found`, `req_id_not_found`.

---

### `agentlaw_plan_archive`
Move a plan from `agentlaw_docs/plans/active/` or `agentlaw_docs/plans/draft/` to `agentlaw_docs/plans/completed/` and archive any matching session row in the same operation. The path may name either a single Markdown plan file or a Pattern 2 directory bundle. When no session exists for the path, a synthetic archived row is recorded so the verifier's plan-DB consistency check has an artifact to reconcile against.

**Archive gate.** When the active session for the plan is in `oracle_evaluation` phase, the gate refuses the move unless every criterion in `oracle_results` resolves to `pass` or `user_confirmed`. Sessions never advanced to `oracle_evaluation` bypass this gate and archive under the original path for backward compatibility.

For `oracle_evaluation` sessions, archive writes `Completed Closure Evidence` and `Plan Oracle Evidence` before moving the file. `Completed Closure Evidence` must include a parseable `Affected surfaces` line. The archive tool writes that line from `completed_closure_evidence.affected_surfaces` when supplied; otherwise it falls back to the reviewed plan body and copies the backticked tokens from its `Affected surfaces` field. `Plan Oracle Evidence` includes an `Evidence provenance` line. The default provenance for archive-generated oracle results is `mcp_oracle_results`; callers may supply `oracle_evidence.provenance` / `oracle_evidence.evidence_provenance` as `user_manual_confirmation`, `interrupted_manual_recovery`, or `legacy_archive_compatibility` when a manual or recovery path is explicitly justified. It validates the completed body shape first and leaves the plan in place when closure evidence is invalid.

**Parameters**
- `plan_path` (string, required) — repo-relative POSIX path to the plan file in active or draft.
- `completed_closure_evidence` (object, optional) — caller-supplied closure fields. Supported keys:
  - `affected_surfaces` (string or list of strings, optional): repo-relative paths or globs, with or without backticks. When omitted, archive uses the reviewed plan body `Affected surfaces` tokens.
  - `note` (string, optional): compact closure note.
- `oracle_evidence` (object, optional) — caller-supplied oracle note fields to include in the generated oracle evidence section. Supported keys:
  - `note` (string, optional): compact oracle note.
  - `provenance` / `evidence_provenance` (string, optional): one of `mcp_oracle_results`, `user_manual_confirmation`, `interrupted_manual_recovery`, `legacy_archive_compatibility`.
  - `reason` / `evidence_reason` (string, optional): required by convention for manual or interrupted recovery evidence so the evidence cannot masquerade as MCP oracle execution.

**Returns**
- `phase` — always `archived`.
- `from_path` — the original plan path (echoed).
- `to_path` — the new path under `agentlaw_docs/plans/completed/`.

**Errors**
- `plan_outside_active_or_draft` when the path is not under `agentlaw_docs/plans/active/` or `agentlaw_docs/plans/draft/`.
- `plan_not_found` when the file or directory bundle does not exist on disk.
- `completed_destination_exists` when the target completed path already exists.
- `archive_gate_oracle_results_empty` when the session is in `oracle_evaluation` but `oracle_check` has not yet been run.
- `archive_gate_blocked` when one or more criteria are still `fail`, `error`, or `pending`. The error payload includes a `blocking_criteria` list naming each unresolved criterion and its current status.
- `completed_closure_validation_failed` when generated evidence would still fail completed-plan closure checks.

---

### `agentlaw_substance_deck_list`
List the substance decks registered at MCP server startup. Each deck is loaded from a `agentlaw_docs/planning-protocol/substance-deck-*.md` file at server startup and (re-)loaded on every call so deck file edits take effect without restart. Plans declaring `- substance: <name>` in their `## Domain Coverage` section bind to one of these decks for verification command defaults; the `code` substance deck registers `pytest`, `mutmut`, and `hypothesis`.

**Parameters**
- (none)

**Returns**
- `deck_count` — number of decks loaded.
- `decks` — list of `{name, source_path, trigger_marker, verification_commands}` per loaded deck.

**Errors**
- (none — degraded environments without the deck directory return `deck_count: 0`).

---

### `agentlaw_plan_review_deep_review_selection_submit`
Submit the independent Deep Review Selection decision after Trigger Coverage. The call must disposition every current candidate. Only `selected` candidates enter the durable roster, pending requirements, and current round queue; `not_applicable` candidates remain auditable without consuming reviewer turns. Decisions are bound to a candidate fingerprint, so a changed activation contract becomes undecided again instead of reusing stale selection evidence.

**Parameters**
- `session_id` (string, required).
- `decisions` (array of objects, required) — exactly one object per current candidate: `{ persona, status, reason }`. `status` is `selected` or `not_applicable`; `reason` must be substantive.
- `review_source` (string, required) — `subagent`, `self_review_user_requested`, or `self_review_subagent_unavailable`.
- `reviewer_agent_id` (string, required when `review_source: subagent`).
- `self_review_reason` (string, required for either self-review fallback).
- `host_submission_capability` (string, required) — opaque value returned once in the Trigger transition payload. The parent host retains it and submits selector output; selector subagents must not receive it.

**Returns**
- `selected_personas`, `selected`, `not_applicable`, `current_round_queue`, and `pending_persona_requirements`.
- `phase`, `review_stage`, and `current_persona`; selected candidates reopen persona review, while an all-N/A decision remains at round check.
- `undispositioned_persona_candidates` — empty after a complete accepted decision set.
- `audit_log_entry_id` — append-only decision record id.

**Errors**
- `wrong_review_stage` unless the session is at `persona_review_round_check` / `deep_review_selection`.
- `invalid_deep_review_selection` for missing, duplicate, unknown, invalid-status, or reason-poor decisions.
- `deep_review_selection_not_required` when no candidate awaits disposition.
- `host_submission_capability_required` when the parent omits the Trigger-issued capability.
- `invalid_host_submission_capability` when the supplied capability does not match the current selection transition.
- Review-provenance, plan-hash, and session lookup errors use the same contracts as finding submission.

---

### `agentlaw_plan_review_selected_personas_extend`
Add host-justified personas outside the current derived candidate decision set to the durable review roster and current round queue. Use Deep Review Selection for the ordinary Trigger-to-specialist transition. Already-selected names use set semantics. When called from `persona_review_round_check`, the tool atomically reopens `persona_review` at the first unreviewed added persona, so an accepted extension cannot become unreachable. Each extension persists its rationale and writes a `memory_logs` audit row.

**Parameters**
- `session_id` (string, required).
- `add_personas` (list of strings, required; non-empty).
- `rationale` (string, required; non-empty) — explains why the specialized personas were selected.

**Returns**
- `session_id`, `selected_personas`, `current_round_queue`, `pending_persona_requirements`, `appended`, `scheduled`, `phase`, `current_persona`, and `audit_log_entry_id`.

**Errors**
- `session_not_found`, `wrong_phase` (must be `persona_review` or `persona_review_round_check`), `add_personas_empty`, `rationale_required`.
- `personas_not_in_deck` (the response payload includes the list of unknown names).

---

### `agentlaw_plan_review_session_prune_proposal`
Propose archived `plan_review_session` rows older than `threshold_days` as prune candidates. Read-only — returns the candidate list with each row's plan-file lifecycle status (`draft` / `active` / `completed` / `renamed_in_<slot>` / `absent`) so the host can judge safety. Sessions already pruned (`pruned_at IS NOT NULL`) are excluded.

**Parameters**
- `threshold_days` (integer, optional; default `90`) — non-negative.

**Returns**
- `threshold_days`, `cutoff_iso`, `candidate_count`, `candidates` (list of `{session_id, plan_path, archived_at, age_days, plan_lifecycle_status}`).

**Errors**
- `threshold_days_must_be_non_negative`.

---

### `agentlaw_plan_review_session_prune_confirm`
Apply the prune to one or more archived `plan_review_session` rows. Mode `soft` (default) sets `pruned_at` and `pruned_reason` and preserves the row body so the prune is reversible by clearing `pruned_at`. Mode `hard` requires `irreversibility_ack` and DELETEs the row. Cross-reference cascade protection refuses any prune whose candidate `session_id`s are cited in plan bodies or memory entries unless `cascade_authorization` is `cascade`; the response then enumerates the full reference chain. Each successful confirm appends one `memory_logs` row recording the operation, mode, candidate ids, cascade chain, and `user_authorization_note`.

**Parameters**
- `session_ids` (list of strings, required; non-empty).
- `user_authorization_note` (string, required; non-empty).
- `mode` (string, optional; default `soft`; one of `soft`, `hard`).
- `irreversibility_ack` (string, optional; required when `mode=hard`).
- `cascade_authorization` (string, optional; `cascade` to authorize the full reference chain in one decision).

**Returns**
- `mode`, `applied_session_ids`, `applied_count`, `cascade_chain` (map of cited candidate → list of citing artifacts), `audit_log_entry_id`.

**Errors**
- `session_ids_empty`, `user_authorization_note_required`, `invalid_mode`, `hard_mode_requires_irreversibility_ack`.
- `session_not_found`, `session_not_archived`.
- `cascade_authorization_required` (response payload includes `cited_candidates`, `cascade_size`, `cascade_artifacts`).

---

### `memory_prune_proposal`
Propose `memory_items` and `memory_logs` rows older than `threshold_days` as prune candidates. Read-only — returns the candidate list split into items and logs. Items are filtered to status in `{superseded, stale, suppressed}`; live status (`active`, `tentative`, etc.) is excluded so the proposal cannot suggest deleting an in-use entry. Logs have no status filter — they are append-only operational records and the threshold is the only inclusion gate.

**Parameters**
- `threshold_days` (integer, optional; default `180`) — non-negative.

**Returns**
- `threshold_days`, `cutoff_iso`, `items_count`, `logs_count`, `items` (list of `{id, type, scope, status, path, updated_at, age_days}`), `logs` (list of `{id, kind, scope, path, recorded_at, age_days}`).

**Errors**
- `threshold_days_must_be_non_negative`.

---

### `memory_prune_confirm`
Apply the prune to one or more `memory_items` and/or `memory_logs` rows. Items and logs may be passed in the same call but are listed in separate parameters since their soft-delete shapes differ — items get `status='suppressed'` and logs get a `pruned` tag because `memory_logs` has no status column. Mode `soft` is reversible: clear status back (items) or remove the `pruned` tag (logs). Mode `hard` DELETEs the row and its derived chunks / tags; `irreversibility_ack` is required. Cross-reference cascade protection mirrors `agentlaw_plan_review_session_prune_confirm`.

**Parameters**
- `user_authorization_note` (string, required; non-empty).
- `item_ids` (list of strings, optional).
- `log_ids` (list of strings, optional).
- `mode` (string, optional; default `soft`; one of `soft`, `hard`).
- `irreversibility_ack` (string, optional; required when `mode=hard`).
- `cascade_authorization` (string, optional; `cascade` to authorize the full reference chain in one decision).

**Returns**
- `mode`, `applied_item_ids`, `applied_log_ids`, `applied_count`, `cascade_chain`, `audit_log_entry_id`.

**Errors**
- `no_candidates`, `user_authorization_note_required`, `invalid_mode`, `hard_mode_requires_irreversibility_ack`.
- `item_not_found`, `log_not_found`.
- `cascade_authorization_required` (response payload includes `cited_candidates`, `cascade_size`, `cascade_artifacts`).

---

## Open Items

- Schema refinements that clarify parameter shapes, default values, or edge-case handling. Revisions go through normal governance update.
- Whether `memory_propose_promotion` should also create an entry in a dedicated proposals queue beyond the log entry (deferred until promotion review process is exercised in practice).
