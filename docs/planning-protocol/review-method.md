# Review Method

## Purpose

Turn a draft plan into a safer revised plan by reviewing it from targeted expert
perspectives.

Personas improve the plan. They do not execute the task, replace tests, or
create an endless review loop.

## Plan-Request Handling

When the user asks the agent to "make / write / draft a plan" (or KR
equivalents per `docs/law/PLANNING_AND_REVIEW_RULES.md` § Plan Request
Default), the default deliverable is a **reviewed execution plan**, not
an unreviewed draft.

Operational order: run the Clarification Gate before drafting; draft only
after material gates are resolved or explicitly deferred; run the Persona
Review Loop before execution; surface the reviewed plan path as the result.
`Plan reviewed: yes` belongs in the plan only after persona passes run.

Explicit draft-only requests ("just a draft", "rough plan", "초안만", etc.)
must label the plan `Status: draft (review skipped per user request, not
executable)`, which downstream verifier and archive paths treat as
non-executable.

## Workflow

Use this sequence when `task-classification.md` says planning is required:

1. Classify the request and identify task class(es).
2. Run the Clarification Gate before drafting. Ask only for answers that
   materially change scope, safety, permission, irreversible action, public
   contract, cost, or intended outcome; record gates in `Execution Gates`.
3. Determine importance/risk by checking against
   `task-classification.md` § Non-Trivial Triggers. Cite each matched
   trigger.
4. Draft the plan using `plan-template.md`. For non-trivial work, self-mark
   the applicable conditional domains in the Domain Coverage section.
5. Run plan-lint checks. Until a standalone `plan-lint` command exists, the
   active-plan checks in `agentlaw verify` are the repository's mechanical
   enforcement surface.
6. Run the **Trigger Coverage Verifier** first in an isolated review pass. It
   validates the trigger marking and the Domain Coverage self-mark against the
   plan content.
7. For non-trivial work, run the **Deep Review Selector** and selected
   deep-review personas sequentially, one persona per turn.
8. Run review-lint checks over review outputs.
9. Consolidate findings.
10. Revise the plan. **Section-based re-review**: when revisions affect a
   specific subset of plan sections, re-invoke only the personas whose
   primary or optional sections (per `persona-section-map.md`) include the
   revised sections. Personas whose sections were not revised do not need
   re-invocation.
11. Execute the revised plan or ask for user approval when required by
    `Execution Gates`.

For trivial plan-required work, only step 5's Trigger Coverage Verifier
runs. If it confirms the trivial classification, no further personas
execute. If it finds a missed trigger, the plan is reclassified as
non-trivial and the workflow restarts from step 4.

## Persona Review Loop Tool Sequence

Steps 6 through 10 above describe the review at the protocol layer. When
an agentlaw runtime is available, those steps must run through the
persona-review-loop MCP tools so the loop is enforced by an external state
machine rather than by host self-discipline:

1. `agentlaw_plan_review_session_start(plan_path, intent_text,
   selected_personas?)` opens the session, returns the first interview
   question, and locks the plan body's content hash for the session.
2. `agentlaw_plan_review_interview_answer_submit(user_answer,
   clarity_scores, opposite_scenario)` records each interview turn. The
   tool computes ambiguity from the host's clarity scores and the
   greenfield or brownfield weights.
3. `agentlaw_plan_review_interview_self_verify_submit(verdict, reason)`
   confirms or rejects the host's own scoring. Persona review begins
   only when verdict is `pass` and ambiguity is at or below 0.2.
4. `agentlaw_plan_review_finding_submit(persona, mandate_quote,
   finding_text, plan_line_citations, severity)` is called once per
   selected persona per round. The tool checks the persona matches the
   current slot, the mandate quote matches the persona deck verbatim
   (after whitespace canonicalization), and each plan-line citation
   matches the plan body byte-for-byte.
5. `agentlaw_plan_review_round_check()` evaluates the round at its
   boundary. Two consecutive zero-finding rounds run the Review Quality
   Gate and, when it passes, leave the session in
   `persona_review_round_check` with a finalize-pending marker; a score
   below the threshold restarts the review from round 1. Stagnation (same
   persona citing the same plan line in consecutive rounds) or hitting the
   round cap stalls it.
6. `agentlaw_plan_review_session_finalize()` first verifies any applicable
   `## Review Coverage Matrix` is closed: no `needs_user_answer` rows,
   no invalid statuses, evidence on `covered` / `accepted_risk`, rationale on
   `not_applicable` / `out_of_scope`, and `crit-*` linkage for rows that claim
   coverage. Only then does it set phase `finalized`, write
   `Plan reviewed: yes` and `Plan contract hash` into the plan body, then
   refresh both the whole-body content hash and reviewed-contract-section
   hash.
7. `agentlaw_plan_archive(plan_path)` moves the plan to
   `docs/plans/completed/` and archives the session row in one
   operation when the work the plan governs is done.

### Plan-review session atomicity

Once `agentlaw_plan_review_session_start` opens a session, the host drives
the flow through `agentlaw_plan_review_session_finalize` and
`agentlaw_plan_archive` (or explicit `agentlaw_plan_review_session_invalidate`
/ stall) without mid-flow status check-ins. The atomic unit is **session-
start → archive (or explicit termination)**.

Host-initiated pauses are limited to declared `Execution Gates`, substantive
scope ambiguity the plan cannot resolve, imminent destructive irreversible
action without a gate, or a user-issued halt. Other mid-flow check-ins are a
false-readiness pattern: they move harness control from the MCP/law layer into
host-local judgment.

Auxiliary tools handle exceptional flow:

- `agentlaw_plan_review_session_invalidate` archives a session without
  writing review evidence. Use after a plan-body change the host does
  not want to carry forward.
- `agentlaw_plan_review_session_reconcile` updates the content hash to
  match the current body while preserving accumulated findings. For
  finalized or `oracle_evaluation` sessions with `plan_contract_hash`,
  it permits mutable evidence/status section changes and rejects reviewed
  contract-section changes.
- `agentlaw_plan_review_session_resume(user_intervention_note)` exits a
  stalled session and starts a fresh round once the user has resolved
  the stagnation or cap.
- `agentlaw_plan_review_session_abandon` archives a stalled session
  without moving the plan file when the host gives up on the current
  attempt.

The verifier's `_test_plan_review_session_consistency` check enforces
that any active plan claiming `Plan reviewed: yes` is backed by a
finalized session. With `plan_contract_hash`, reviewed contract sections must
match while mutable evidence/status sections may change; legacy sessions keep
the older whole-body hash check. Failing active plans return to
`docs/plans/draft/` until re-reviewed.

## Review Coverage Matrix Closure

The Review Coverage Matrix is the review-completeness ledger. It does not
try to prove that every possible requirement is known. It proves that every
declared review axis has a closed state and that unknown states did not slip
through as agent assumptions.

Valid statuses are:

- `covered`: the axis has evidence and at least one `crit-*` linkage.
- `not_applicable`: the axis is not relevant and the rationale is stated.
- `needs_user_answer`: the axis is unknown and must be asked of the user.
- `accepted_risk`: the axis is not fully covered, but the risk is explicit and
  linked to at least one criterion or gate.
- `out_of_scope`: the axis is excluded and the rationale is stated.

`needs_user_answer` is a hard review-closure blocker. The host asks the user,
records the answer in the plan or an MCP-backed clarification record, updates
the row, and re-invokes only the personas mapped to the changed sections.
Silent conversion from unknown to assumption is invalid unless the user has
explicitly delegated that judgment.

When the runtime is unavailable (offline environments, partial installs,
or while bootstrapping the loop itself), the prose review steps in the
Workflow section are the fallback. Plans reviewed by prose alone must
live under `docs/plans/draft/` until they can be re-reviewed through the
tools.

Do not store long duplicate draft and revised plan bodies unless preserving
both is itself useful evidence. Default to the final executable plan plus
compact review evidence and short notes.

Required review evidence fields for review-required plans:

```text
Risk triggers matched: <list of cited triggers from § Non-Trivial Triggers>
Importance/Risk: trivial / non-trivial
Domain self-mark: <conditional domain checklist; non-trivial only>
Deep Review Selection: <selected/skipped review summary; non-trivial only>
Review required: yes
Plan reviewed: yes
Personas applied: <non-empty persona list>
Revised after review: yes
Execution Gates: <approval and stop-condition summary>
```

Review-required plans must also include a short section:

```text
## Separate Persona Review Passes

### <Persona name>

- Status: PASS / FAIL / N/A
- Severity: must-change / should-change / note
- Inspected sections: <plan sections read>
- Evidence: <specific plan text or absence that supports the finding>
- Plan risk found: <concrete risk>
- Required plan change: <specific plan edit>
- Verification or gate to add: <test, check, approval, or explicit none>
- Residual risk if accepted: <remaining risk>
```

Legacy no-finding passes may use compact PASS evidence, but only when the
persona found no concrete plan change. New plans should use detailed fields
with `Status`, `Inspected sections`, and `Evidence`; every `must-change` or
`should-change` finding requires the detailed shape.

For trivial plans, the only required persona pass is the Trigger Coverage
Verifier:

```text
Risk triggers matched: (none — trivial)
Importance/Risk: trivial
Review required: yes (Trigger Coverage Verifier only)
Plan reviewed: yes
Personas applied: Trigger Coverage Verifier
Revised after review: yes / no changes required
```

If review is not required (plan-exempt), record:

```text
Review required: no
Review exemption reason: <short reason>
```

Use `Revised after review: no changes required` only when the separate
persona passes found no must-change or should-change items.

Do not mark `Plan reviewed: yes` until the separate persona passes are
actually performed and recorded.

Stop implementation if review was skipped, `Plan reviewed: yes` was written
before persona passes, trivial classification was wrong, or scope expands into
a non-trivial trigger. Reclassify, update fields and Domain Coverage, rerun the
required review depth, record governance-relevant correction, then resume only
from the revised plan.

## Persona Review Contract

Each persona review must produce plan edits, not general commentary.

Expected output per persona:

```text
Persona:
Severity: must-change / should-change / note
Plan risk found:
Required plan change:
Verification or gate to add:
Residual risk if accepted:
```

## Severity

Severity is a calibration signal — it tells the plan author and any
downstream archive gate how to interpret the finding. The level
definitions below are a **rubric**, not an algorithm: reviewers retain
judgment for novel cases, but the rubric ensures that level selection
is anchored rather than arbitrary.

### Level rubric

| Level | Selection criterion (one-line) | Illustrative anchor (not exhaustive) |
| --- | --- | --- |
| `must-change` | execution-blocking — the plan would produce a wrong, unsafe, or non-compliant result if executed as-is, or the plan contains a load-bearing claim/structure that cannot be verified by the listed oracles | Acceptance Criteria has no runnable or user-confirmable oracle; Premise asserts code behavior that contradicts a cited file:line; Risk section names an irreversible step with no rollback or user gate; Domain Coverage self-mark omits a domain the plan content visibly touches |
| `should-change` | execution-permissible with residual risk — execution can proceed but the plan would be materially stronger with the fix, and accepting the risk should be explicit | Verification section names tests for the happy path only and omits adversarial cases for a new mechanism; Stakeholder Map lists "users" generically when role segmentation would materially change scope; Premise fact carries an assumption label but the verification path is vague |
| `note` | non-blocking observation — useful context for the plan author or future reader but does not change executability or material risk | Wording improvement; cross-reference to a sibling plan; suggestion to add a follow-up in Continuity; section ordering preference |

Anchor examples above are **illustrative not exhaustive** — they
indicate the kind of finding that fits each level, not the only
findings that do. When a finding does not match an anchor cleanly,
reviewers select the level based on the criterion (one-line) and
record the reasoning in the finding text. Anchors that bias reviewers
toward only-checking-anchor-cases (Wang et al. 2023, anchor-bias) are
mitigated by this exhaustiveness disclaimer and by the criterion-based
fallback.

### Severity and the archive gate

The two-phase completion path treats severity as follows:
`must-change` findings block archive (oracle-evaluation will not let
the plan exit `oracle_evaluation` phase until the finding is
addressed); `should-change` findings do not block archive but should
be either accepted in writing (a Status note or Continuity decision)
or fixed before archive; `note` findings are recorded without further
gating.

## Review Quality Gate

`round_check` scores the persona review itself only at the would-finalize
edge: two consecutive rounds with zero `must-change` or `should-change`
findings at round 2 or later. Self-Challenge is not scored by this gate.

The returned `review_quality_gate` object reports `score`, `threshold`,
`decision`, `components`, and `reasons`. The default threshold is `0.80`.
Components are deterministic: coverage of selected personas, substance of
the submitted findings, novelty across same-persona rounds, and severity
calibration.

If `score >= threshold`, `round_check` records finalize-pending convergence
and includes the quality report in the success payload and
`convergence_state`; `session_finalize` remains responsible for setting phase
`finalized` and writing the reviewed block. If
`score < threshold`, `round_check` does not finalize; it records a rejected
attempt summary in `convergence_state.review_quality_restarts`, clears the
current attempt findings, resets `round_number` to `1`, sets
`current_persona` to the first selected persona, and returns
`review_quality_gate.decision = "restart"`.

Sessions with no selected personas are treated as `not_applicable` for
backward compatibility and pass the gate.

### Oracle Marker WARN

`agentlaw_plan_review_oracle_check` emits a non-blocking WARN when an
acceptance criterion uses `user_confirms` while its body text contains
terms that suggest a mechanical check would be feasible (e.g., "grep",
"diff", "pytest", "verifier output", "byte-equal", "exit code", "FAIL
count", "PASS count"). The WARN surfaces a suspected Oracle-marker
misuse without blocking the loop; reviewers see the suggestion and
can amend the criterion to runnable form, or accept the
`user_confirms` if the surface match was coincidental (e.g., a
subjective criterion that happens to mention "verifier" semantically
rather than mechanically). See `docs/planning-protocol/plan-template.md`
§ Oracle Marker Selection for the authoring rule the WARN derives from.

## Mandate Authoring Pattern

New persona mandates **should** follow this recommended pattern. Existing
prose-only mandates remain valid; retrofitting them is separate work. The
pattern distills four elements common to decomposed rubric and LLM-as-judge
systems: sub-checks, anchors, structured output, and explicit flexibility.

### Recommended structure

A persona mandate that follows the pattern should specify:

1. **Sub-check decomposition** — 3-5 independently judgeable checks.
2. **Concrete anchors** — at least one pass anchor and one fail anchor;
   anchors are illustrative, not exhaustive.
3. **Structured output schema** — normally Status, Severity, Inspected
   sections, Evidence, Plan risk found, Required plan change, Verification or
   gate to add, and Residual risk if accepted. The MCP tool already enforces
   `mandate_quote`, `plan_line_citations`, and `severity`.
4. **Recommend-not-require framing** — domains may diverge when a different
   structure is justified; name the reason inline.

### Cross-reference

Examples already following the pattern include Trigger Coverage Verifier,
Premise & Source Reviewer, Cross-Section Coherence Reviewer, Rule-on-Self
Reviewer, Code-Fact Claim Verifier, Form-vs-Substance Auditor, Confidence Label
Reviewer, and the Domain 5 test-adequacy personas in
`persona-decks-specialized.md`. Other existing personas are grandfathered.

## Selection Rule

1. Run the classification gate.
2. If planning is not required, do not run persona review.
3. For trivial plan-required work, run only the Trigger Coverage Verifier
   persona and the minimum plan-lint profile.
4. For non-trivial plan-required work: run Trigger Coverage Verifier first,
   run Deep Review Selector, then run selected universal, substance-triggered,
   and sensitive-domain personas sequentially; finish with review-lint.
5. If a plan-exempt class escalates, treat it as non-trivial plan-required
   and apply step 4.
6. If the class is conditional, use the corresponding deck per the
   triggers in `task-classification.md`.
7. If a task has multiple classes, union applicable personas, deduplicate, and
   run sequentially.
8. Universal concerns are always checked, but not always isolated turns.
9. Always isolate trust boundary, permission, sensitive data, state migration,
   external contract, irreversible action, rollback, governance/rule-system
   change, or high-impact legal/financial/medical concerns when touched.
10. Every isolated reviewer needs an activation reason in Deep Review
   Selection: trigger, lint flag, Trigger Coverage finding, or selector
   rationale. Do not activate personas without reasons.
11. Prefer fewer strong personas over many shallow personas.
12. Use Split-task only when scope is artificially bundled; cohesive large
   scope may remain one plan.

## Deep Review Selection

For non-trivial plans, add a compact `Deep Review Selection` section or field
before separate persona passes:

```text
Universal concerns checked by plan-lint:
Isolated universal reviewers selected:
Specialized reviewers selected:
Sensitive-domain reviewers selected:
Reviewers not run with N/A reason:
Persona count:
Split-task warning: yes / no
```

Activate every persona whose §Selection Rule reason holds. Record each reason
verbatim; skipped reviewers need an N/A reason such as trigger absent, section
not touched, or lint passed. Do not use "not run" to hide a triggered
high-risk concern.

## Consolidation

After persona reviews, consolidate into: must change before execution, nice to
improve if cheap, and accepted residual risk. Revise once. Avoid another loop
unless a must-change item creates new irreversible or high-stakes risk. Keep
the consolidation short: must-change findings, changes applied, residual risks.

Avoid copying the full pre-review plan into the final plan body.

## Finding Quality Bar

A useful finding includes at least one of:

- a missed step
- a wrong sequence
- a missing verification
- a missing user gate
- a missing source or authority check
- a rollback or recovery gap
- a scope leak
- a safe simplification
- over-planning, including fixed step sequences that can safely be left to the
  agent
- procedural detail that does not affect correctness, safety, verification, or user value
- repeated checks that can be batched, or expensive probes that should follow
  cheaper prerequisite checks

Do not keep weak findings that only restate generic caution. See
§Substance Enforcement (mechanical) for the server-side checks
that translate this bar into rejection codes.

## Substance Enforcement (mechanical)

The MCP server enforces a sentence-count and field-presence shape
on every persona finding to keep the false-readiness pattern from
recurring (entry 18 family in tech-debt-tracker.md).

- `finding_submit` returns
  `error: "finding_text_below_prescriptive_mandate"` with a
  `details` list when the payload fails the shape:
  - `severity` in `{must-change, should-change}` requires at
    least 3 sentences in each of `Evidence`, `Plan risk`,
    `Required plan change`, `Verification` (extracted by
    `<Field>:` prefix substring search);
  - `severity = note` requires the literal token `PASS`
    (case-insensitive) and a total of at least 3 sentences;
    if the persona deck declares Coverage items, at least one
    Coverage item must appear by verbatim string in the note
    body.
- Sentence count splits the field text on the regex
  `[.!?。][\s]+|[.!?。]$|\n{2,}` (English `.!?` plus CJK `。`)
  and drops empty segments.
- `finding_submit` also accepts an optional `amend_proposal:
  list[op]` field. Each op is a dict
  `{type, target, content, rationale}` where `type` is one of
  `insert_after | replace | delete`, `target` is
  `{section_header, line_offset_in_section}`, and `rationale`
  is a non-empty string. Missing `amend_proposal` on
  must-change / should-change calls yields a non-blocking
  `amend_proposal_missing_legacy_warning` in the success
  payload (Phase 1).
- `round_check` aggregates each round's amend ops, calls
  `detect_op_conflicts`, and on no-conflict batch-applies via
  `apply_ops_to_plan`, updating `plan_content_hash` on the
  session row. A conflict (two ops on the same section header
  from different personas) is returned as `pending_conflicts`
  and blocks phase advance until
  `agentlaw_plan_review_resolve_amend_conflict` clears it.
- `session_finalize` refuses with
  `error: "self_challenge_required"` unless
  `agentlaw_plan_review_self_challenge_submit` has recorded a
  response. The response is either path-A
  (`type="weakest_with_amend"` carrying `weakest_finding_id`,
  `weakness_sentence`, and a `strengthening_amend_op`) or
  path-B (`type="full_justification"` whose `entries` cover
  every persisted must-change / should-change finding with a
  `plan_body_citation` plus a `justification` of at least 3
  sentences). Empty body or the literal string `"none"` is
  rejected with `self_challenge_invalid`.
- `finding_submit` success payloads carry a `transparency_echo:
  str` field shaped
  `[round R / Persona / severity / first 80 chars / amend_ops=N]`
  so the human watching the conversation sees substance as
  it lands.

The residual gap: a finding whose three sentences are
syntactically valid but semantically empty still passes the
sentence-count check. Future semantic-similarity scoring will
tighten this; for now the gap is pinned by the regression test
suite as a known limit.
