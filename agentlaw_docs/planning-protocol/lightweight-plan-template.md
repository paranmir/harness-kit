# Lightweight Plan Template

## Purpose

Lightweight contract for **non-trivial-but-not-plan-required** work — that
is, work that is non-trivial in correctness/safety implication but does
not match any §Non-Trivial Trigger in
`agentlaw_docs/planning-protocol/task-classification.md`. The full
`plan-template.md` is heavy for this class; a one-page contract carrying
seven required fields is enough.

For plan-required work, use `plan-template.md` instead. For trivial work
(per `task-classification.md` §Quick Gate), no artifact is required — the
agent's response prose carries the contract.

## Authority

The lightweight plan is the operational contract per
`agentlaw_docs/law/USER_INTENT_ALIGNMENT.md` §Application Scope mode 2 (non-plan-
required-but-non-trivial work). It does not run through the persona-
review session; enforcement is the agent's own self-check against the
acceptance_checks field at completion time.

## Template Body

Copy the seven sections below into a short artifact (a section in the
agent's response, a `notes/` markdown file, an issue body, or any
durable medium the work uses). Fill each field in prose or bulleted
form; the template's value is the named fields, not the formatting.

```markdown
## user_goal

(In the author's own words. The single outcome the user actually wants.
User-confirmable form.)

## deliverable

(The specific artifact / change / behavior the work produces. Names the
format and the location.)

## explicit_requirements

(Bulleted list. Items the user named directly. Each item attributable
to a specific source if possible.)

## non_goals

(Bulleted list. Things the work does NOT do. Especially important when
adjacent work would be a natural-feeling extension.)

## acceptance_checks

(Bulleted list. Concrete checks the agent runs before claiming
completion. Each check should be either grep-runnable, test-runnable,
or a one-line manual inspection step.)

## clarification_policy

- **ask_now**: (questions raised because missing information would
  materially change correctness / safety / user-visible result / cost
  / legal risk)
- **proceed_with_assumption**: (assumptions taken because missing
  information is low-risk or inferable, with rationale)

## law_bindings

(Bulleted list of law documents that govern this work. At minimum:
`agentlaw_docs/law/USER_INTENT_ALIGNMENT.md`. Add others as applicable —
`agentlaw_docs/law/INPUT_OUTPUT_CONTRACT.md`, `agentlaw_docs/law/ORACLE_AND_JUDGMENT.md`,
`agentlaw_docs/law/CODE_AUTHORSHIP_AND_STEWARDSHIP_RULES.md`, etc.)
```

## When to escalate to a full plan

The lightweight plan is the contract for non-trivial-but-not-plan-
required work. If during execution the work matches any §Non-Trivial
Trigger (multi-file refactor, external contract change, governing-
artifact change, distribution-boundary content change, behavioral
change, etc.), escalate to a full plan per `plan-template.md` and run
the persona-review session. The lightweight plan's content carries
over to the full plan's corresponding sections.

The Clarification Policy field carries over verbatim; the other six
fields map naturally (user_goal → Intent §Restated ask, deliverable →
Classification §Primary output contract, explicit_requirements →
Intent §Restated ask + Acceptance Criteria, non_goals → Scope §Non-
scope, acceptance_checks → Acceptance Criteria, law_bindings →
implicit across Premise / Risk + Rollback / Governance).

## Worked example

```markdown
## user_goal

내 git pre-commit hook 이 unstaged change 있을 때 commit 진행을 막아주는
형태로 동작했으면 좋겠음.

## deliverable

`.git/hooks/pre-commit` 에 추가할 bash 스크립트 (또는 동등 hook
manager 설정).

## explicit_requirements

- unstaged change 가 있으면 commit 차단
- staged-only commit 은 그대로 허용
- escape hatch: `git commit --no-verify` 로 우회 가능

## non_goals

- linting / formatting check (별도)
- branch name validation (별도)

## acceptance_checks

- unstaged change 있는 상태에서 `git commit -m "test"` 실행 → exit
  non-zero, 차단 메시지 stderr
- staged change 만 있는 상태에서 같은 commit → 정상 exit 0
- `git commit --no-verify -m "..."` → 차단 우회, 정상 진행

## clarification_policy

- ask_now: (없음)
- proceed_with_assumption:
  - hook manager (pre-commit framework 등) 가 아니라 raw bash 스크립트
    로 작성. 사용자가 별도 도구를 명시 안 했으므로 minimal dependency
    경로 채택. 우회 가능 (escape hatch).

## law_bindings

- agentlaw_docs/law/USER_INTENT_ALIGNMENT.md
- agentlaw_docs/law/INPUT_OUTPUT_CONTRACT.md
```

작성 후 hook 스크립트를 실제로 작성하고 acceptance_checks 3 항목을
실행해서 통과 확인. completion 직전에 unmet check 가 있으면 그것을
숨기지 말고 disclose.
