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
- `.agentlaw/` path checks for governed runtime/index/cache/job purposes
- memory manifest/config shape, authority-label, source-precedence, and status checks when memory formats exist
- leak checks that prevent product-specific, project-specific, or authoring-only facts from entering generalized shared artifacts
- required code-authorship law presence and read-first routing checks
- tests, lint rules, verifier checks, or CI checks for repeated code-authorship violations when they become mechanically detectable
- plan-preflight checks that scan `agentlaw_docs/plans/active/*.md` for the required fields enumerated in `REPOSITORY_ARTIFACT_RULES.md` §"Active Plan Preflight Fields", failing the verifier when a plan omits a field without an explicit "not applicable" note
- plan-review-evidence checks that scan `agentlaw_docs/plans/active/*.md` for the compact review fields and `Separate Persona Review Passes` section enumerated in `REPOSITORY_ARTIFACT_RULES.md` §"Active Plan Preflight Fields", failing the verifier when a review-required plan lacks recorded review evidence, lacks per-persona evidence fields, or when a review-exempt plan lacks an exemption reason
- plan-review finalize preflight checks that run the same active-plan preflight and review-evidence readiness checks before `agentlaw_plan_review_session_finalize` writes `Plan reviewed: yes`; incomplete plans remain unfinalized so execution cannot proceed from a reviewed-looking plan that would fail `agentlaw verify`. The preflight includes parseable `Affected surfaces` coverage: prose-only affected-surface entries do not satisfy the gate.
- completed-plan closure checks that scan prospective `agentlaw_docs/plans/completed/*.md` plans and fail when a completed plan lacks acceptance criteria or `crit-*` records, affected-surface evidence, `Plan reviewed: yes`, completed plan-review evidence, or completed oracle evidence. The check preserves historical plans but blocks new false-readiness claims at the completed/public boundary.
- archive-tool closure checks that make `agentlaw_plan_archive` write completed closure/oracle evidence for oracle-evaluation sessions and reject the move when the resulting completed body would fail closure shape checks.
- verification-cost discipline checks that distinguish focused implementation checks from final readiness checks: focused checks are run while editing changed behavior, and the full project pytest suite is run once before commit, push, release, or other public-ready representation.
- publish-gate oracle pairing checks that, when the `pyproject.toml` version differs from a recorded baseline (the bump commit), enumerate `git diff <bump-commit>..HEAD` and fail when modifications appear without paired oracle changes. Two checks per `CODE_AUTHORSHIP_AND_STEWARDSHIP_RULES.md` §"Publish Gate Oracle Pairing": runtime-source pairing (`src/<package>/*.py` modifications must be paired with `agentlaw_tests/` modifications) and harness-content pairing (modifications under `agentlaw_docs/law/`, `agentlaw_docs/contracts/`, `HARNESS_*.md`, `AGENTS.md`, or `src/<package>/scaffold/` must be paired with `agentlaw_tests/` modifications **or** `src/<package>/verify_cmd.py` modifications, the latter counting a new verifier check as the oracle for a new mechanical rule)
- plan-coverage check that, when the `pyproject.toml` version-bump baseline is resolvable, enumerates `git diff <bump-commit>..HEAD` for non-trivial-surface files (the same surfaces the publish-gate oracle pairing checks consider) and fails when any such file is not listed in the `Affected surfaces` field of an active plan or a completed plan within the lookback window. Anchors to `REPOSITORY_ARTIFACT_RULES.md` §"Active Plan Preflight Fields": each `Affected surfaces` bullet must contain at least one repo-relative path or glob in backticks, which the verifier extracts and matches via Python's `pathlib.PurePath.match` (with directory-prefix shortcut for tokens ending in `/`). The check closes the discipline gap where an agent commits non-trivial work without writing or extending a plan that names the touched files.
- plan-review host architecture guard that limits further responsibility growth in `src/agentlaw/server/tools/plan_review.py`. New domain, service, or persistence behavior belongs in owned modules such as `src/agentlaw/plan_review_system/` or narrow helpers; the MCP host file should remain a registration/adapter surface.
- plan-review session path consistency checks that treat a finalized session's `plan_path` as part of reviewed-plan identity. A reviewed active plan at a different path fails as unreviewed, and a non-archived session row whose path no longer exists fails as an orphan session.
- Review Coverage Matrix closure checks that fail opted-in or prospective plans when review axes remain unresolved, evidence-free, rationale-free, or unlinked from acceptance criteria. `agentlaw_plan_review_session_finalize` also blocks `Plan reviewed: yes` when the matrix contains `needs_user_answer` or other closure failures, making the user-question gate mechanical instead of prose-only.
- plan-review interview provenance checks that fail prospective active reviewed plans when the last interview turn carries `user_answer_source: llm-self-submitted` instead of the `user_answer_memory_log_entry_id` cross-check. Historical sessions remain warning-compatible, but new active plans must use the user-touchpoint memory-log path or remain unreviewed.
- code-substance test-rigor persona checks that apply only to code-modification-related plans. Active-plan preflight fails a non-trivial plan whose affected surfaces name executable runtime source, tests, scripts, CLI/MCP runtime paths, or schema/runtime state behavior unless the plan marks `- substance: code` or records a narrow false-positive rationale. Plans that do mark `substance: code` must account for the code test-rigor personas in review evidence. Documentation-only, law-only, memory-only, and reference-only plans are not required to add test files by this check.
- memory scope contract checks that fail when write-tool JSON schema, runtime validation, contract docs, law docs, and scaffold disagree on local write scopes. Write surfaces must advertise only `repository` and `session`; docs must not present `user` as a valid scope, and any `global` mention must be marked as legacy local-target compatibility rather than cross-project memory.
- document self-narration checks that fail governed current-state surfaces when they carry implementation-history prose instead of reusable current contract. The check scans law, contract, reference, root-control, and scaffold documents for patterns such as dated "added/changed" narration, promotion cutoffs, release-phase labels, and internal decision codes. Historical artifacts whose purpose is dated recordkeeping, such as completed plans, release notes, changelogs, and memory logs, are exempt.
- document filler checks that fail governed current-state surfaces when they carry conversational padding rather than concise operational content. The check targets high-confidence filler phrases such as pleasantries, performative willingness, and "note that" scaffolding while leaving technical qualifiers and exact quoted examples alone.
- LLM-facing influence-section concision checks that fail root and scaffold README source-attribution bullets when they exceed the concise current-state envelope. Attribution should name the source and reusable idea without carrying long rationale, process history, or local implementation narration.
- form-vs-substance detection check that scans plan bodies and rule-document deltas for the false-readiness pattern: a section satisfies structural template requirements while inline prose admits the substance is incomplete. The check fires on hedging phrases ("checked, none", "pending without verification path", "deferred without unblocking condition", "partial", "TBD") paired with form-correct presence in load-bearing fields, AND on cross-section pairs whose claims constrain each other but conflict (e.g., Verification section's stated TDD strategy ↔ Work Breakdown step body verbs; Acceptance Criteria `crit-*` count ↔ Work Breakdown checklist count). Implemented by the Form-vs-Substance Auditor + Cross-Section Coherence Reviewer personas at review time, plus verifier coverage that mechanically replays the documented family instances as a regression corpus.
- tech-debt-tracker resolved-entry placement check that scans `agentlaw_docs/plans/tech-debt-tracker.md` and fails the verifier when an entry under `## Current Entries` carries a resolved-class status marker (`resolved`, `addressed by`, `closed by`) or an entry under `## Resolved Entries` carries an open-class status marker (`open`). The check enforces the tracker's prose policy at line 33 ("When an entry is decisively resolved, move it to `## Resolved Entries` ...") as a mechanical fail condition. Promotion follows repeated instances per Constitution §"Repeated stable constraints should be promoted from prose into mechanical enforcement when practical".

## Code Authorship Enforcement
Code-authorship rules are governed by `agentlaw_docs/law/CODE_AUTHORSHIP_AND_STEWARDSHIP_RULES.md`.

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
