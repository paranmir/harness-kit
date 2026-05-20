# Mechanical Enforcement Policy

## Purpose
This document defines when repository rules must be promoted into executable enforcement instead of remaining prose-only guidance.

## Core Rule
Mechanically detectable implementation and structure rules must be promoted into executable enforcement.

They must not remain prose-only unless executable enforcement is currently infeasible.

## Default Enforcement Principle
If the repository can make a forbidden path fail directly, it must prefer that over relying on agent instruction-following alone.

## Enforcement Evaluation Requirement
Every implementation-facing or structure-facing rule must be evaluated for:
- mechanical detectability
- repetition
- cost of failure
- feasibility of enforcement

## Mandatory Promotion Rule
A rule must be promoted into executable enforcement when all of the following are true:
- the rule affects implementation behavior or repository structure
- violation of the rule is mechanically detectable
- violation would create meaningful repair cost, structural drift, or unreliable future work

When these conditions hold, prose-only treatment is not allowed as the default response.

## Allowed Fallback
A rule may remain prose-only only when:
- the rule is not yet mechanically detectable
- enforcement would currently be unreliable
- the rule is still unstable and not yet ready for automation

## Typical Enforcement Targets
Executable enforcement may include:
- lint rules
- CI checks
- structural tests
- document existence checks
- cross-reference checks
- tests that fail forbidden behavior directly
- validation of stable derived facts such as a recorded shared-harness baseline when later update flow depends on it
- package-template or installer-asset sync checks when installable distribution exists
- `.harness/` path checks for governed runtime/index/cache/job purposes
- memory manifest/config shape, authority-label, source-precedence, and status checks when memory formats exist
- leak checks that prevent product-specific, project-specific, or authoring-only facts from entering generalized shared artifacts
- required code-authorship law presence and read-first routing checks
- tests, lint rules, verifier checks, or CI checks for repeated code-authorship violations when they become mechanically detectable
- plan-preflight checks that scan `docs/plans/active/*.md` for the required fields enumerated in `REPOSITORY_ARTIFACT_RULES.md` §"Active Plan Preflight Fields", failing the verifier when a plan omits a field without an explicit "not applicable" note
- plan-review-evidence checks that scan `docs/plans/active/*.md` for the compact review fields and `Separate Persona Review Passes` section enumerated in `REPOSITORY_ARTIFACT_RULES.md` §"Active Plan Preflight Fields", failing the verifier when a review-required plan lacks recorded review evidence, lacks per-persona evidence fields, or when a review-exempt plan lacks an exemption reason
- completed-plan closure checks that scan prospective `docs/plans/completed/*.md` plans and fail when a completed plan lacks acceptance criteria or `crit-*` records, affected-surface evidence, `Plan reviewed: yes`, completed plan-review evidence, or completed oracle evidence. The check preserves historical plans but blocks new false-readiness claims at the completed/public boundary.
- publish-gate oracle pairing checks that, when the `pyproject.toml` version differs from a recorded baseline (the bump commit), enumerate `git diff <bump-commit>..HEAD` and fail when modifications appear without paired oracle changes. Two checks per `CODE_AUTHORSHIP_AND_STEWARDSHIP_RULES.md` §"Publish Gate Oracle Pairing": runtime-source pairing (`src/<package>/*.py` modifications must be paired with `tests/` modifications) and harness-content pairing (modifications under `docs/law/`, `docs/contracts/`, `HARNESS_*.md`, `AGENTS.md`, or `src/<package>/scaffold/` must be paired with `tests/` modifications **or** `src/<package>/verify_cmd.py` modifications, the latter counting a new verifier check as the oracle for a new mechanical rule)
- plan-coverage check that, when the `pyproject.toml` version-bump baseline is resolvable, enumerates `git diff <bump-commit>..HEAD` for non-trivial-surface files (the same surfaces the publish-gate oracle pairing checks consider) and fails when any such file is not listed in the `Affected surfaces` field of an active plan or a completed plan within the lookback window. Anchors to `REPOSITORY_ARTIFACT_RULES.md` §"Active Plan Preflight Fields": each `Affected surfaces` bullet must contain at least one repo-relative path or glob in backticks, which the verifier extracts and matches via Python's `pathlib.PurePath.match` (with directory-prefix shortcut for tokens ending in `/`). The check closes the discipline gap where an agent commits non-trivial work without writing or extending a plan that names the touched files.
- plan-review host architecture guard that ratchets `src/agentlaw/server/tools/plan_review.py` against further responsibility growth. New domain, service, or persistence behavior belongs in owned modules such as `src/agentlaw/plan_review_system/` or narrow helpers; the MCP host file should remain a registration/adapter surface.
- Review Coverage Matrix closure checks that fail opted-in or prospective plans when review axes remain unresolved, evidence-free, rationale-free, or unlinked from acceptance criteria. `agentlaw_plan_review_session_finalize` also blocks `Plan reviewed: yes` when the matrix contains `needs_user_answer` or other closure failures, making the user-question gate mechanical instead of prose-only.
- form-vs-substance detection check that scans plan bodies and rule-document deltas for the false-readiness meta-family pattern: a section satisfies structural template requirements while inline prose admits the substance is incomplete. The check fires on hedging phrases ("checked, none", "pending without verification path", "deferred without unblocking condition", "partial", "TBD") paired with form-correct presence in load-bearing fields, AND on cross-section pairs whose claims constrain each other but conflict (e.g., Verification section's stated TDD strategy ↔ Work Breakdown step body verbs; Acceptance Criteria `crit-*` count ↔ Step 10 list count). Implemented by the Form-vs-Substance Auditor + Cross-Section Coherence Reviewer personas at review time, plus a verifier sub-check that mechanically replays the documented family instances as a regression corpus.
- tech-debt-tracker resolved-entry placement check that scans `docs/plans/tech-debt-tracker.md` and fails the verifier when an entry under `## Current Entries` carries a resolved-class status marker (`resolved`, `addressed by`, `closed by`) or an entry under `## Resolved Entries` carries an open-class status marker (`open`). The check enforces the tracker's prose policy at line 33 ("When an entry is decisively resolved, move it to `## Resolved Entries` ...") as a mechanical fail condition. Promotion driven by 6 instances accumulated across two session clusters (2026-04-26 entry 6; 2026-05-11 entries 19–23) per Constitution §"Repeated stable constraints should be promoted from prose into mechanical enforcement when practical".

## Code Authorship Enforcement
Code-authorship rules are governed by `docs/law/CODE_AUTHORSHIP_AND_STEWARDSHIP_RULES.md`.

Repeated violations of test anchoring, behavioral-oracle use, public-surface coverage, schema migration coverage, or high-risk-code verification must be evaluated for mechanical enforcement.

If a violation can be detected through committed tests, static checks, verifier rules, lint rules, or CI, the repository should prefer that executable enforcement over repeated prose reminders.

## Governance Failure Rule
If a mechanically detectable implementation or structure rule repeatedly remains prose-only without justification, treat that as a governance failure.

## Next Update Trigger
Update this document when:
- enforcement criteria change
- enforcement targets expand
- prose-only fallback is being overused
- repeated violations show that stronger executable policy is needed
