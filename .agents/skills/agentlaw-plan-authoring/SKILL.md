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

- `docs/law/PLANNING_AND_REVIEW_RULES.md`
- `docs/law/REPOSITORY_ARTIFACT_RULES.md`
- `docs/planning-protocol/task-classification.md`
- `docs/planning-protocol/plan-template.md`
- `docs/planning-protocol/review-method.md`
- `docs/planning-protocol/persona-decks-core.md`
- `docs/planning-protocol/persona-decks-specialized.md`
- `docs/planning-protocol/persona-section-map.md`
- `docs/contracts/agentlaw-mcp-tools.md`

## Pre-Write Checks

Before writing or changing a plan, verify these points against the source
documents above:

1. Classify the task and cite matched non-trivial triggers.
2. Fill the active-plan preflight fields required by
   `docs/law/REPOSITORY_ARTIFACT_RULES.md`.
3. Mark Domain Coverage according to the plan content, not convenience.
4. Keep acceptance criteria parser-compatible:
   `- \`crit-N\`:` followed by an indented `Oracle:` line.
5. Use runnable Oracle markers for mechanically checkable criteria.
6. Keep `user_confirms` only for genuinely subjective confirmation.
7. Include `Plan Review Evidence` and `Separate Persona Review Passes` in
   the shape required by `docs/planning-protocol/review-method.md`.
8. Run the MCP persona-review loop before execution for non-trivial plans.
9. Do not edit reviewed contract sections after review unless the changed
   sections are re-reviewed.
10. After implementation, run the oracle phase and archive gate before
    treating the plan as complete.

## Operating Rule

This skill is a reminder and router only. If this file conflicts with
`docs/law/*` or `docs/planning-protocol/*`, the law and protocol documents
win.
