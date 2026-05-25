# User Intent Alignment

## Purpose

This document defines how an agent preserves the user's actual
request intent during execution. It does not replace any existing
scope, contract, oracle, planning, memory, or mechanical enforcement
law. It binds those laws to each concrete user request through a
compact runtime contract that the plan-template / plan-review
session already provides for plan-required work, and through a
lightweight plan path for non-plan-required-but-non-trivial work.

## Core Rule

For every non-trivial request, the agent must hold an explicit
operational contract before execution. The contract records — at
minimum — the user's goal, the deliverable's specific form, the
explicit requirements the user stated, the constraints, the
non-goals, the named assumptions, the clarification policy, the
acceptance checks the agent will compare the final output against,
and the evidence the agent will or will not produce. The contract
is not a new source of truth above law; it is the per-request
operational shape of the same laws.

The contract may be either:

- **The plan body itself**, when the work is plan-required per
  `docs/planning-protocol/task-classification.md` §Non-Trivial
  Triggers. The plan-template's existing sections (Intent, Scope,
  Acceptance Criteria, Assumptions, Risk + Rollback, Execution
  Gates, Clarification Policy, Verification) carry the contract
  with the persona-review loop enforcing it.
- **A lightweight plan**, per
  `docs/planning-protocol/lightweight-plan-template.md`, when the
  work is non-trivial but does not match any plan-required trigger.
  The lightweight plan carries the same fields in compact form and
  does not run through the persona-review session.
- **An inline working contract held in conversation**, when the
  work is trivial. No artifact is required; the agent's response
  records the goal, deliverable, and acceptance checks in plain
  prose.

## Clarification Rule

The agent should ask a clarifying question only when missing
information would materially change one of:

- correctness of the output
- safety, irreversibility, or reversibility of the action
- the user-visible result
- the cost or time burden the user will absorb
- a legal, security, or governance risk

If the missing information is low-risk, reversible, or inferable
from existing context, the agent proceeds with an explicit
assumption. The plan-template's `## Clarification Policy` section
codifies this rule with two sub-fields: `ask_now` (the explicit
question list, each with reason) and `proceed_with_assumption`
(the assumption list, each with rationale).

## Execution Rule

During execution, the agent must not silently expand scope beyond
the operational contract. If new information changes the task,
constraints, risk, affected surfaces, or verification needs, the
agent updates the plan body (or escalates a lightweight plan to a
full plan), records the change in the contract, and either
notifies the user or asks for confirmation if the change crosses
the Clarification Rule threshold.

## Completion Rule

Before the final response, the agent compares the output against
the contract's acceptance checks. Unmet checks must be disclosed,
not hidden. The plan-review session's oracle phase (when active)
mechanically enforces this for plan-required work; lightweight and
trivial work rely on the agent's own self-check against the
contract's acceptance list.

## Concise Accuracy Rule

User-facing answers and governed current-state artifacts must use the
shortest form that preserves substance. Keep exact code, commands, URLs,
paths, identifiers, schema names, error strings, and quoted source text.

Remove pleasantries, filler, performative hedging, authoring narration,
release-era labels, and internal decision codes unless the artifact is a
historical record or the user explicitly asks for that tone.

Do not compress away load-bearing evidence: verification results, failure
conditions, acceptance criteria, user gates, ordering constraints, safety
warnings, permission boundaries, legal/security impact, and next actions
remain explicit.

LLM-facing current-state artifacts must keep only certain, reusable,
load-bearing information. Preserve invariants, contracts, failure conditions,
execution order, verification commands, user decisions, identifiers, paths,
and exact command syntax. Remove authoring history, process narration,
release timing, speculative rationale, and decorative explanation unless the
artifact's purpose is historical recordkeeping.

## Application Scope

This law applies in three modes, distinguished by the work's
classification under `docs/planning-protocol/task-classification.md`:

1. **Plan-required work** (any §Non-Trivial Trigger matches):
   the operational contract is the plan body. The plan-review
   session's interview gate + persona review + oracle phase carry
   enforcement. This law's content does not duplicate the
   plan-template; it names the binding semantics.

2. **Non-plan-required-but-non-trivial work**: the operational
   contract is a lightweight plan. The contract is shorter, lives
   alongside the work (e.g., a brief note attached to the change,
   or a section in the agent's response), and does not run through
   the persona-review session. The lightweight plan still records
   user_goal, deliverable, explicit_requirements, non_goals,
   acceptance_checks, clarification_policy, and law_bindings.

3. **Trivial work**: no artifact required. The agent's response
   itself names the goal, deliverable, and acceptance checks in
   prose. The Clarification Rule still applies — questions that
   would materially change correctness or safety are not skipped
   because the task is trivial.

## Failure Tags

This law introduces or owns the following LLM-behavior-level
failure tags. Their relationship to the existing 10-category
taxonomy in `docs/law/FAILURE_TAXONOMY.md` is recorded in that
file's cross-reference table.

- **requirement_miss** — explicit user-stated requirement absent
  from output.
- **constraint_violation** — agent acted against a user-stated or
  law-stated constraint.
- **over_questioning** — agent asked when proceed-with-assumption
  would have sufficed.
- **under_questioning** — agent proceeded with assumption when the
  missing information would have materially changed correctness,
  safety, reversibility, or user-visible result.
- **plan_drift** — execution diverged from the operational
  contract without updating it.
- **fake_verification** — agent claimed verification (test
  passed, confirmed, 검증함, 확인함) without evidence trace.
- **verification_skip** — agent acted on a load-bearing claim
  without running the available verification step.

These tags name behaviors, not artifacts. The mapping table in
`docs/law/FAILURE_TAXONOMY.md` connects each tag to the broader
category (Hierarchy / Scope / Contract / Oracle / Execution Drift
/ Shared Artifact Drift / Artifact Drift / Installer Drift /
Memory Authority Failure / Recursive Promotion Failure) that
serves as the structural anchor.

## Relationship To Existing Law

- `docs/law/INPUT_OUTPUT_CONTRACT.md` defines minimum request and
  output discipline; this law adds the per-request operational
  contract as the binding medium.
- `docs/law/ORACLE_AND_JUDGMENT.md` defines what counts as
  evidence; this law's Completion Rule requires evidence to back
  any verification claim.
- `docs/law/PLANNING_AND_REVIEW_RULES.md` defines when tracked
  plans and review are required; this law's Application Scope
  declares the contract format per work class.
- `docs/law/FAILURE_TAXONOMY.md` records repeated failures and
  promotion candidates; this law's failure tags map to that
  taxonomy through the cross-reference table.
- `docs/law/MECHANICAL_ENFORCEMENT_POLICY.md` governs promotion
  to deterministic checks; this law's Clarification Rule and
  Completion Rule are currently prose-enforced and may promote to
  mechanical enforcement (verifier sub-checks, hook patterns)
  when recurrence justifies.

This law does not duplicate the plan-template. The plan-template
carries the structural form; this law carries the binding
semantics that connect that form to per-request execution.
