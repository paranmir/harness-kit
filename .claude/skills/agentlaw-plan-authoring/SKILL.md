---
name: agentlaw-plan-authoring
description: |
  Agent-facing plan authoring procedure reminder. Activate when creating,
  reviewing, revising, executing, or archiving agentlaw execution plans,
  especially requests mentioning "plan", "draft", "review plan", "archive
  plan", "oracle", "acceptance criteria", "플랜", "계획", "검토", or
  "오라클".
---

# agentlaw Plan Authoring Reminder

This skill is for agents writing or changing harness execution plans. It is
not a user-facing plan-writing guide.

Do not create a parallel plan guide, checklist, or new plan format. Do not
create a parallel source of truth. The existing planning protocol documents
own the format, review method, persona selection, and oracle rules.

## Read Before Drafting Or Amending

Read the relevant law and protocol sources before drafting, amending,
reviewing, executing, or archiving a plan:

- `agentlaw_docs/law/PLANNING_AND_REVIEW_RULES.md`
- `agentlaw_docs/law/REPOSITORY_ARTIFACT_RULES.md`
- `agentlaw_docs/planning-protocol/task-classification.md`
- `agentlaw_docs/planning-protocol/plan-template.md`
- `agentlaw_docs/planning-protocol/review-method.md`
- `agentlaw_docs/planning-protocol/persona-decks-core.md`
- `agentlaw_docs/planning-protocol/persona-decks-specialized.md`
- `agentlaw_docs/planning-protocol/persona-section-map.md`
- `agentlaw_docs/contracts/agentlaw-mcp-tools.md`

## Pre-Write Checks

Before writing or changing a plan, verify these points against the source
documents above:

1. Classify the task and cite matched non-trivial triggers.
2. Fill the active-plan preflight fields required by
   `agentlaw_docs/law/REPOSITORY_ARTIFACT_RULES.md`.
3. Mark Domain Coverage according to the plan content, not convenience.
4. Keep acceptance criteria parser-compatible using the exact examples below.
5. Use `Oracle:` with an allowlisted command for mechanically checkable
   criteria; `runnable Oracle:` is an accepted explicit synonym.
6. Keep `user_confirms` only for genuinely subjective confirmation.
7. Include `Plan Review Evidence` and `Separate Persona Review Passes` in
   the shape required by `agentlaw_docs/planning-protocol/review-method.md`.
8. For code-changing plans, mark `- substance: code` and fill the Code Plan
   Fidelity profile before execution.
9. Run the MCP persona-review loop before execution for non-trivial plans.
10. Do not edit reviewed contract sections after review unless the changed
   sections are re-reviewed.
11. After implementation, run the oracle phase and archive gate before
    treating the plan as complete.

## Persona Reviewer Effort

Read `host_orchestration.reviewer_effort_policy` from the current batch
manifest before spawning persona reviewers. Policy version `2` uses
provider-neutral tiers:

- Use `standard` by default. Prefer a fast cost-efficient capability with low
  or default reasoning for deterministic format, mapping, and schema checks;
  prefer a balanced capability with middle or default reasoning for ordinary
  semantic or documentation judgment.
- Use `deep` only for a materially difficult architecture, data-loss,
  security, concurrency, irreversible-action, or external-contract judgment,
  and record the escalation reason.
- Use `exceptional` only when the user explicitly requests the strongest
  available model and reasoning.
- Keep reviewer coverage and accepted concurrency independent from effort.
- Before or as reviewer work begins, tell the user the actual model or model
  class, reasoning effort, fallback status, and selected concurrency. Submit
  the same bounded values with the findings; do not wait until review ends.
  Treat them as host assertions unless provider or runtime telemetry verifies
  them. Do not ask the user to attest their accuracy or use acknowledgment as a
  readiness gate.
- Choose capability from the hardest material judgment in the assigned task,
  not from the persona name. Mixed tasks use the highest materially required
  class; record the reason when that requires `deep`.
- Choose the greatest currently safe concurrency by reasoning from the current
  host's observable policy, capability, availability, and workload. Do not use
  a provider-specific or fixed numeric shared limit. Reassess when the host
  changes, and never reduce the required reviewer roster to fit concurrency.
- If the host cannot set or report a requested control, use the closest host
  default and disclose the unavailable control and actual observable choice.
- Report unsupported existing policy values and use the documented `standard`
  fallback or reject them. Do not guess. Unknown additive fields do not change
  fallback behavior.
- Validate and submit the complete batch atomically. A late invalid item must
  not leave earlier findings or convergence changes persisted.

## Canonical Format

Scalar fields may keep the value on the same line, wrap plain continuation
text, or use child bullets indented deeper than the owning field. A peer-level
bullet or heading ends the field.

```markdown
- Risk triggers matched:
  - `2. External-interface contract change`.
  - `9. Behavioral or functional change`.
- Importance/Risk: non-trivial
```

Every code criterion carries a verification trace and one Oracle form:

```markdown
- `crit-1`: The behavior is mechanically verified.
  Verification trace: `test:test_behavior`
  Oracle: `py -3.11 -m pytest agentlaw_tests/test_behavior.py -q`
```

Use `Oracle: user_confirms` only when the criterion genuinely requires human
judgment. Stable section headings and documented status values remain exact.

## Operating Rule

This skill is a reminder and router only. If this file conflicts with
`agentlaw_docs/law/*` or `agentlaw_docs/planning-protocol/*`, the law and protocol documents
win.
