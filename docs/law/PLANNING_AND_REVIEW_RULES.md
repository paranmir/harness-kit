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

## Operational Sources

The canonical operational planning sources are:

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
- `execution_gates_source`: `docs/planning-protocol/plan-template.md`
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
