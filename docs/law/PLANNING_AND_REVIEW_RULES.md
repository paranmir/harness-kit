# Planning And Review Rules

## Purpose

This document defines when agents must plan, how draft plans must be reviewed,
and where the operational planning protocol lives.

The law layer owns the obligation. The operational details live in
`docs/planning-protocol/*` so planning rules stay usable without bloating
`docs/law/*`.

## Authority

This document governs planning behavior for all non-trivial agent work, not only
code work.

When this document and a protocol document appear to conflict, this document
wins. Protocol documents may clarify classification, review method, and persona
selection, but they must not weaken the obligations here.

## Planning Gate

Before meaningful work begins, the agent must classify the user's request.

The agent may skip a repository-tracked plan only when the request is clearly
low-risk, answer-only, short, non-current, non-mutating, and does not involve a
high-stakes domain or durable process change.

The agent must create or extend a plan when the request is multi-step,
state-changing, high-risk, externally consequential, freshness-sensitive,
governance-affecting, release/deploy oriented, or likely to require verification
through tests, citations, calculations, previews, dry-runs, or other evidence.

Before drafting or executing a plan, the agent must run a **Clarification
Gate**. The agent should proceed without asking the user only when all
material execution choices are either stated, safely inferable from repository
authority, or reversible within the approved plan. The agent must ask the user
a focused follow-up question before execution when a missing answer would
change the deliverable, scope, safety boundary, public contract, release state,
cost, data handling, or user-facing recommendation. Clarification questions
must be limited to the smallest set needed to unblock the decision; they must
not be used as a delay tactic for ordinary discoverable facts.

For plan-required work, the agent must further classify the task as **trivial**
or **non-trivial** by checking the structural triggers listed in
`docs/planning-protocol/task-classification.md` § Non-Trivial Triggers. Trivial
plan-required work still produces a plan record but skips the full persona
review; non-trivial plan-required work runs the full review per
§ Required Planning Workflow.

## Plan Request Default

When the user asks the agent to "make a plan" / "write a plan" / "draft a
plan" / "plan to do X" (or the Korean equivalents: "플랜 만들어", "계획 짜",
"플랜 작성", "플랜 세워"), the default deliverable is a **reviewed
execution plan** — a plan that has run through the Clarification Gate
and (for non-trivial work) through the Persona Review Loop, ready to
execute pending only the Execution Gates listed in the plan body. The
agent must not stop at an unreviewed draft and call the obligation
complete.

The agent may produce an **unreviewed draft only when the user
explicitly requests draft-only output** — phrasings like "just a
draft", "rough plan", "outline only", "skip review", "draft
without review", or the equivalent Korean "초안만", "리뷰 없이",
"러프하게". An explicitly draft-only plan must be labeled in its
`## Status` field as `Status: draft (review skipped per user request,
not executable)`. The label is the parseable signal that the plan
has not passed Persona Review and must not be acted on as if
reviewed.

Two failure-family obligations attach to plan requests, and they
share the same surface:

1. **Gate-before-draft**: the Clarification Gate (above) must run
   before plan drafting begins. The agent must not draft a non-
   trivial plan body before the gate's questions are resolved.
2. **Review-before-execution**: the Persona Review Loop (below)
   must run before plan execution begins (for non-trivial plans).
   `Plan reviewed: yes` must not be written before the persona
   passes are actually performed.

Both obligations are violations of equal weight: a draft authored
before the gate is the same failure family as a plan executed
before review. The runtime reminder packet
(`agentlaw_session_restore` / `agentlaw_session_save`) surfaces both
obligations together at session start so the agent encounters them
before composing the response that interprets the user's plan
request.

## Required Planning Workflow

When planning is required, the agent must follow this workflow before executing
the plan:

1. Classify the request and identify task class(es).
2. Run the Clarification Gate and ask the user only for missing answers that
   materially change the plan, permission boundary, output contract, or safety
   posture. Record any user gate in the plan's `Execution Gates` section.
3. Determine importance/risk by checking against non-trivial triggers (per
   `docs/planning-protocol/task-classification.md` § Non-Trivial Triggers).
4. Draft the plan using the structure in
   `docs/planning-protocol/plan-template.md`. Cite each matched trigger in the
   plan's preflight fields.
5. Run plan-lint checks. Until a standalone `plan-lint` command exists,
   `agentlaw verify` is the canonical mechanical enforcement surface for
   parseable plan structure.
6. For non-trivial plans:
   a. Self-mark the applicable conditional domains in the template's Domain
      Coverage section.
   b. Run the Trigger Coverage Verifier first in an isolated review pass; it validates
      the plan's trigger marking and Domain Coverage self-mark against the
      plan content.
   c. Run the Deep Review Selector per `docs/planning-protocol/review-method.md`.
      Universal concerns are always checked, but not every universal persona
      is always run as an isolated deep-review turn.
   d. Run only the selected deep-review personas in isolated passes. Mandatory
      isolated reviewers still run when their concern is triggered: trust
      boundary, permission, sensitive data, state migration, external contract,
      irreversible action, rollback/user gate, governance/rule-system change,
      or high-impact legal/financial/medical decision.
   e. Run review-lint checks against the recorded review evidence.
   f. Consolidate findings.
   g. Revise the plan once. When revisions affect a specific subset of plan
      sections, re-invoke only the personas whose primary or optional
      sections (per `docs/planning-protocol/persona-section-map.md`) include
      the revised sections.
7. For trivial plans:
   a. Run only the Trigger Coverage Verifier persona to confirm the trivial
      classification is correct.
   b. Run the minimum plan-lint profile.
   c. If the verifier finds a trigger that was missed, reclassify as
      non-trivial and restart from step 4.
8. Execute the revised plan or ask for user approval when required by
   `Execution Gates`.

The revised plan is the execution plan. Persona review comments are not the
goal; they are inputs for improving the plan.

Plan documents must not preserve long duplicate "draft plan" and "revised plan"
bodies by default. That duplicates content and makes plans harder to maintain.
Instead, review-required plans must record compact review evidence fields:

- `Risk triggers matched` (cited triggers from the non-trivial list)
- `Importance/Risk` (trivial / non-trivial)
- `Domain self-mark` (conditional domain checklist; non-trivial only)
- `Review required`
- `Plan reviewed`
- `Personas applied`
- `Revised after review`
- `Deep Review Selection` (non-trivial only, unless explicitly not applicable)
- `Execution Gates`

When review is not required, the plan must record `Review exemption reason`.

Review-required plans must also include a short `Separate Persona Review
Passes` section. Each persona pass should use the review-method fields:
status, severity, inspected sections, evidence, plan risk found, required plan
change, verification or gate to add, and residual risk if accepted. Legacy
plans that were authored before these strict fields landed may use the prior
five-field shape or compact PASS shape until migrated, but new plans should not
record evidence-free PASS/N/A.

When a persona completes its pass and finds no concrete plan change, the plan
may record compact no-finding evidence instead of the full five-field finding
shape. Compact evidence must still name the persona and state that the pass
found no concrete plan change. Compact evidence is not valid for `must-change`
or `should-change` findings.

The plan may include short review notes when useful, but the evidence fields
and the separate persona-pass section are the parseable contract that proves
the plan was not treated as ready before review. An agent must not mark
`Plan reviewed: yes` before those persona passes are actually performed and
recorded.

`Execution Gates` are the parseable contract for user re-questioning and
approval. A plan must name whether execution can proceed without user approval,
which actions require approval, and stop conditions that force reclassification
or re-questioning. Agents must not ask follow-up questions for every ordinary
unknown; they must ask when the missing fact materially changes safety,
contract, scope, cost, irreversible action, or the user's intended outcome.

If the agent discovers during implementation that:

- review was skipped or compressed,
- a plan was falsely marked reviewed before persona passes ran,
- a trivial classification was wrong (a non-trivial trigger matches but was
  not cited), or
- the scope has expanded mid-execution to match a non-trivial trigger that
  was not present at the original classification,

implementation must stop. The agent must reclassify under the current plan
content, update the plan's preflight fields and Domain Coverage to match the
reclassification, re-run persona review per the new classification (full
review for non-trivial; Trigger Coverage Verifier only for trivial), record
the correction in the active plan when governance-relevant, and resume
execution only from the revised plan.

## Persona Review Loop Tool Sequence

Non-trivial plan review must run through the persona-review-loop MCP tools
when an agentlaw runtime is available. Prose review without the tool
sequence does not satisfy the planning gate for new plans, because a single
LLM author has no mechanism to switch persona viewpoints across the review
beyond the sequence the tools enforce.

Required tool sequence:

1. `agentlaw_plan_review_session_start` opens a session for the plan path
   and returns the first interview question and the ambiguity threshold.
2. `agentlaw_plan_review_interview_answer_submit` records each interview
   turn (user answer, host clarity scores, opposite scenario).
3. `agentlaw_plan_review_interview_self_verify_submit` advances to persona
   review when the host's own counter-scoring confirms ambiguity ≤ 0.2.
4. `agentlaw_plan_review_finding_submit` records each persona's finding,
   carrying a verbatim mandate quote from the persona deck and exact-byte
   plan-line citations.
5. `agentlaw_plan_review_round_check` evaluates round convergence,
   stagnation, and the round cap.
6. `agentlaw_plan_review_session_finalize` writes the `Plan reviewed: yes`
   block to the plan body once two consecutive rounds produce no new
   findings.
7. `agentlaw_plan_archive` moves the plan to `docs/plans/completed/` when
   the work it governs is complete.

Completed plans must carry enough closure evidence to be audited without
reconstructing the whole session from chat history. For new plans under the
Plan Closure Matrix, completion requires: acceptance criteria or `crit-*`
records, affected-surface evidence, `Plan reviewed: yes` review evidence,
and non-pending `Plan Oracle Evidence` showing how each criterion was
satisfied or explicitly user-confirmed. Work that has only an unreviewed
draft plan may be local work-in-progress, but it must not be represented as
completed, archived, pushed, released, or otherwise public-ready.

Plans authored after the Review Coverage Matrix rule lands, and older plans
that opt in with `Review Coverage Matrix required: yes`, must close a
`## Review Coverage Matrix` before `Plan reviewed: yes` is written. The matrix
is a review-completeness contract: every review axis must be classified as
`covered`, `not_applicable`, `needs_user_answer`, `accepted_risk`, or
`out_of_scope`. Unknowns do not become assumptions by default. A
`needs_user_answer` row blocks finalization until the user answers and the row
is updated. `covered` and `accepted_risk` rows require evidence and a `crit-*`
link; `not_applicable` and `out_of_scope` rows require rationale. This rule is
prospective for plans authored after 2026-05-18 unless a future plan revises
the cutoff through the bootstrap transitional protocol.

The verifier check `_test_plan_review_session_consistency` cross-references
each active plan's `Plan reviewed` field against the persistent session
state. For sessions carrying `plan_contract_hash`, reviewed contract
sections must match that stored contract hash; mutable status/evidence
sections may change as execution evidence accumulates. Legacy sessions
without `plan_contract_hash` retain the older whole-body content-hash
check. An active plan that claims `Plan reviewed: yes` without a finalized
session, or with changed reviewed contract sections, fails verification.
Plans that predate this mechanism and have not yet been re-reviewed must
live under `docs/plans/draft/` until they pass through the loop.

When a runtime is unavailable (offline-only environments, partial
installs, or while bootstrapping the loop itself), the agent must record
the unavailability in the plan body, fall back to the prose review
workflow above, and label the resulting plan as draft until a runtime
becomes available to re-review through the tools.

## Operational Sources

The canonical operational planning sources are:

- `agentlaw-plan-authoring` Agent Skill for agent-facing activation and
  routing to these canonical sources; it is a reminder channel only, not a
  separate plan format or authority layer
- `docs/planning-protocol/task-classification.md` for deciding whether
  planning is required, identifying task class(es), and determining trivial
  vs non-trivial via structural triggers
- `docs/planning-protocol/review-method.md` for the plan review and
  consolidation procedure, including Deep Review Selection
- `docs/planning-protocol/persona-decks-core.md` for the universal review
  bench
- `docs/planning-protocol/persona-decks-specialized.md` for substance- and
  class-specific review personas
- `docs/planning-protocol/plan-template.md` for the plan document structure
  that non-trivial plans must follow
- `docs/planning-protocol/persona-section-map.md` for the persona ↔ plan-
  section mapping that drives section-based revision re-review

Agents should read only the protocol sections needed for the current task
class and importance/risk level unless ambiguity or risk requires broader
review.

## Runtime Reminder Contract

Runtime reminder packets should point agents at the planning protocol when they
surface plan discipline guidance.

The `plan_discipline_reminder` returned by `agentlaw_session_restore` and
`agentlaw_session_save` must include these operational pointers:

- `classification_source`: `docs/planning-protocol/task-classification.md`
- `review_method_source`: `docs/planning-protocol/review-method.md`
- `persona_deck_sources`: `docs/planning-protocol/persona-decks-core.md` and
  `docs/planning-protocol/persona-decks-specialized.md`
- `plan_template_source`: `docs/planning-protocol/plan-template.md`
- `persona_section_map_source`: `docs/planning-protocol/persona-section-map.md`
- `review_evidence_fields`: the compact field names that make a plan ready
  plus the `Separate Persona Review Passes` evidence section name (which now
  includes `Risk triggers matched`, `Importance/Risk`, and `Domain self-mark`
  in addition to the prior fields)
- `clarification_gate`: ask the user before execution only when a missing
  answer materially changes scope, safety, contract, irreversible action, cost,
  or the intended outcome
- `plan_request_default_obligation`: when the user asks the agent to "make /
  write / draft a plan" (or KR equivalents), the default deliverable is a
  reviewed execution plan, not an unreviewed draft. Draft-only output is
  produced only when the user explicitly asks for it, and the file is labeled
  `Status: draft (review skipped per user request, not executable)`. See
  § Plan Request Default
- `plan_request_gate_before_draft_obligation`: the Clarification Gate runs
  before plan-body drafting (not after). Gate-before-draft and
  review-before-execution are violations of equal weight
- `execution_gates_source`: `docs/planning-protocol/plan-template.md`
- `review_coverage_matrix_source`: `docs/planning-protocol/plan-template.md`
  `## Review Coverage Matrix`; unknown review states require user answer and
  block finalization while unresolved
- `non_trivial_triggers_obligation`: every plan-required task is classified
  as trivial or non-trivial by checking the structural triggers in
  `task-classification.md`; matched triggers must be cited
- `trivial_classification_safety_obligation`: trivial plans still require one
  Trigger Coverage Verifier pass to confirm the classification
- `domain_self_mark_obligation`: non-trivial plans must self-mark the
  applicable conditional domains in the plan-template's Domain Coverage
  section
- `bootstrap_exemption_pattern`: plans that introduce or revise this rule
  system itself may invoke transitional exemption per § Bootstrap
  Transitional Exemption
- `section_revision_rereview_obligation`: when a plan is revised after
  review, only the personas whose mapped sections changed are re-invoked
- `selected_deep_review_obligation`: universal concerns are always checked by
  plan-lint / review-lint / selector reasoning, but isolated persona turns are
  selected by triggers, lint findings, high-stakes concerns, or selector
  reasons
- `before_action_authority_check`: before substantive action, identify and
  consult the governing law, protocol, tool, plan, or contract when the action
  can change files, plans, memory, governance, runtime behavior, verification,
  release state, or other durable/external state

Runtime surfaces the reminder; the agent remains responsible for judging
whether planning is required and applying the workflow.

## Review Discipline

Persona review must produce concrete plan improvements.

Useful findings identify a missing step, wrong sequence, missing verification,
missing user gate, missing source, rollback gap, scope leak, or safe
simplification.

Plans define the work contract, not a mandatory local execution script. A
valid plan states the final goal, necessary intermediate goal states, success
criteria, constraints, user gates, stop conditions, and verification boundary.
The agent may choose the execution path, local order, batching, and
parallelization unless a fixed order is required by a correctness dependency,
irreversible or hard-to-reverse action, external side effect, user approval
gate, security/privacy/money/deployment/legal/governance risk, or
validation-before-mutation boundary. Do not require a fixed step-by-step script
when goal-level constraints and verification are sufficient.

Weak findings that only restate generic caution should not survive
consolidation.

## Trivial Work Boundary

This rule must not turn every response into a ceremony.

For plan-exempt work, the agent may answer directly after a lightweight
internal check.

For plan-required work, the binary classification of trivial vs non-trivial
controls review depth:

- **Trivial plan-required work**: a plan record is still produced (using the
  template's required minimum sections), but the full persona review is
  skipped. The Trigger Coverage Verifier persona is the only required pass,
  to confirm trivial classification is correct.
- **Non-trivial plan-required work**: the full persona review applies, with
  Trigger Coverage Verifier first, then universal bench, then substance-
  triggered personas.

If a task starts plan-exempt but gains file edits, external effects,
current-source dependence, high-stakes consequences, or durable process
impact, it escalates into the planning workflow.

If a trivial-classified task gains complexity mid-execution that would have
matched a non-trivial trigger, implementation must stop and the task is
reclassified per § Required Planning Workflow.

## Bootstrap Transitional Exemption

When a plan introduces or revises this rule system itself, or revises any
artifact under `docs/planning-protocol/` that defines the rule system's
operational details, the plan may invoke transitional exemption:

- The plan is reviewed under the rule version current at authoring time, not
  under the version the plan introduces. Reviewing a rule under itself before
  it lands is a chicken-and-egg failure mode.
- The exemption must be declared explicitly in the plan body, naming the
  rule version under which review applies and the specific rule revisions
  that the plan introduces.
- After the introducing plan lands, the new rules apply prospectively. The
  introducing plan and any other plans authored before the new rules land
  are grandfathered: they are not retroactively re-reviewed under the new
  rules.
- The Bootstrap & Transition Reviewer persona verifies that the exemption
  declaration is explicit, well-formed, and limited to the rule revisions
  the plan actually introduces.
- Bootstrap exemption is the only valid form of skipping review obligations
  imposed by new rules; all other skips constitute a violation per
  § Required Planning Workflow.

## Completion And Handoff

For repository-tracked work, the plan must name affected surfaces,
verification, rollback or recovery, non-goals, and any new law or reference
artifacts.

When the work completes, move the active plan to the completed plan
directory with enough context for a later agent to reconstruct the intended
behavior, review decisions, verification, and residual risks.

## Two-Diamond Verified Delivery (Second Diamond Obligation)

The persona-review loop has two diamonds. The first diamond — interview
→ self-verify → persona_review → round_check → finalized — validates a
plan as a document. The second diamond — finalized → oracle_evaluation
→ archive — validates that the plan's promises are actually realized
in the artifacts the plan governs.

Plans whose work is implementable must pass through the second diamond
before archiving. The MCP tool sequence is
`agentlaw_plan_review_session_enter_oracle_phase` →
`agentlaw_plan_review_oracle_check` →
`agentlaw_plan_review_oracle_user_confirm` (for criteria the plan body
marks `user_confirms`) → `agentlaw_plan_archive`. The archive gate is
all-or-nothing per Q6=a — every acceptance criterion in
`oracle_results` must resolve to `pass` or `user_confirmed` before
the move into `docs/plans/completed/` is permitted. Sessions never
advanced to `oracle_evaluation` (legacy plans, or simple plans that
deliberately skip the second diamond) bypass this gate for backward
compatibility.

Acceptance criteria in plans that activate the second diamond must
adopt the `(criterion id, oracle)` paired shape — each `crit-*`
identifier carries an `Oracle:` marker that names a runnable command
from the executable allowlist (pytest, python, mutmut, hypothesis,
agentlaw) or the explicit `user_confirms` marker.

## Substance-Deck Registration

A plan's `## Domain Coverage` section may declare one or more
substance markers (e.g., `- substance: code`) that bind the plan to
a registered substance deck under
`docs/planning-protocol/substance-deck-*.md`. The deck declares the
default verification commands the substance applies (the code
substance binds pytest + mutmut + hypothesis, with the configured
thresholds). The MCP tool `agentlaw_substance_deck_list` lists the
loaded decks so the host can verify which substances are available.
A new substance deck file lands via a follow-up plan; modifying an
existing deck's commands or thresholds requires Q7=c authorization
because the change reaches every plan that has bound to it.

## Entry-Gate Clarification (Interview Cross-Check)

The `interview` phase of `agentlaw_plan_review_session_start` is the
canonical entry-gate clarification mechanism. The interview phase
gains real user-touchpoint via the optional
`user_answer_memory_log_entry_id` parameter on
`agentlaw_plan_review_interview_answer_submit`: when supplied, the
tool resolves the id via `memory_get`, requires the entry's body to
contain `user_answer` verbatim, and requires the entry's
`recorded_at` to be at least 5 seconds AFTER the plan's authoring
timestamp — the plan body's `- Date authored:` line, with git
first-commit fallback when absent or unparseable (anti-self-
fabrication friction). When omitted, the tool
records the `user_answer_source: llm-self-submitted` marker and the
verifier's `_test_plan_review_session_consistency` sub-check 5
emits a warn-only signal naming the session_id and plan_path. The
warning preserves backward compatibility for plans authored before
this enhancement landed.

## Bootstrap Exemption Recording

A plan that introduces or revises the persona-review loop or the
planning protocol invokes the bootstrap transitional exemption: the
plan is reviewed under the rule version current at authoring time
(pre-this-plan), and the new mechanisms apply prospectively to plans
authored after this plan archives. Every invocation must include a
`## Bootstrap Transitional Exemption` section that names (a) the
specific mechanisms exempted, (b) the prospective-application cutoff,
and (c) the revision-protocol re-invocation pattern (how a future
plan that revises the same mechanism re-invokes the exemption
without infinite regress). Verifier checks plan-body amendment
provenance against the recorded exemption sections — see also
`## Plan Amendment Authorizations`.

## Implementation Deviation Records

After `Plan reviewed: yes`, agents must preserve the reviewed plan
contract. If implementation must take a different local route while still
satisfying the reviewed intent, the agent records the reason in
`## Implementation Deviation Records` instead of rewriting contract
sections to make the plan appear retrospectively exact. The deviation
record must name the planned contract reference, implemented behavior,
reason for divergence, authority or user authorization, verification, and
residual risk. Changes to the reviewed contract itself still require
explicit amendment authorization and re-review.

## Code-Fact Claim Verification Trace

Plan bodies that assert specific code behavior — drift suspicion
sites, acceptance-criterion bodies that name code paths or symbols,
Premise facts about implementation, and Confidence Register entries
citing code — must carry a `Verification trace: <file:line |
test:<test_name> | unverified-hypothesis>` token alongside each
such claim. The token has exactly three permitted forms: a
`file:line` reference to inspected code, a `test:<test_name>`
reference to a test that pins the behavior, or the literal
`unverified-hypothesis` marker that records the claim is an
agent-authored hypothesis not yet checked against code. The
Code-Fact Claim Verifier persona (universal core deck, Domain 17 —
Substance Coherence) enforces this at plan-review time; missing
traces are must-change findings. This rule is prospective: plans
authored before the rule lands are grandfathered, and the rule
applies to plans authored after the introducing plan archives.
