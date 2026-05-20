# Repository Guidelines

This file is a routing map only. Governing content belongs in `docs/law/*`.

## What This Repository Is
This repository is governed by the agentlaw kit — a law-first governance harness for AI coding agents.

## Governance Reminder

Before starting fix / init / upgrade / governance work (tracker-policy violation, repeated mistake, rule bypass, new project setup, kit upgrade), read the matching root control document first: `AGENTLAW_FIX_TOOL.md` for fix-class work, `AGENTLAW_INIT_TOOL.md` for init, `AGENTLAW_UPDATE_TOOL.md` for kit upgrades. The `.claude/skills/agentlaw-governance/SKILL.md` and `.agents/skills/agentlaw-governance/SKILL.md` skills (Agent Skills open standard) carry the same reminder for Claude Code and Codex respectively.

## Source of Truth
Document priority is:

1. `AGENTLAW_CONSTITUTION.md`
2. Root control documents:
   - `AGENTLAW_INIT_TOOL.md`
   - `AGENTLAW_UPDATE_TOOL.md`
   - `AGENTLAW_FIX_TOOL.md`
3. `docs/law/*`
4. Approved structured repository artifacts such as `docs/plans/*`, `docs/references/*`, and stable generated facts when they exist
5. Approved continuity or memory records
6. `AGENTS.md` (this file)

## Read First
Check the current agreement in this order:

1. `AGENTLAW_CONSTITUTION.md`
2. Root control tools when relevant
3. Workspace law layer:
   - `docs/law/SCOPE.md`
   - `docs/law/INPUT_OUTPUT_CONTRACT.md`
   - `docs/law/ORACLE_AND_JUDGMENT.md`
   - `docs/law/CODE_AUTHORSHIP_AND_STEWARDSHIP_RULES.md`
   - `docs/law/FAILURE_TAXONOMY.md`
   - `docs/law/REPOSITORY_ARTIFACT_RULES.md`
   - `docs/law/PLANNING_AND_REVIEW_RULES.md`
   - `docs/law/MEMORY_AND_CONTINUITY_RULES.md`
   - `docs/law/STARTER_SPECIALIZATION_RULES.md`
   - `docs/law/MECHANICAL_ENFORCEMENT_POLICY.md`
   - `docs/law/USER_INTENT_ALIGNMENT.md`
4. This file (`AGENTS.md`)

For session recovery, also read:
- `memory/working-set.md`
- `memory/LOOKUP_RULES.md`
- `docs/references/project-overview.md`

## Worktree Map

Routing only — see referenced documents for governing content.

- `README.md` — installation, usage, and project description.
- `LICENSE` — license.
- `docs/law/*` — workspace law (constitution-derived governance). Read order in §Read First above.
- `docs/contracts/*` — MCP tool surface (`agentlaw-mcp-tools.md`), shared baseline, update workflow.
- `docs/planning-protocol/*` — review-method, task-classification, persona decks (core + specialized), persona-section map, plan template.
- `docs/references/*` — orientation and design notes; `project-overview.md` is the worktree-at-a-glance entry for this project.
- `docs/plans/draft/*` — in-development plans pre-review.
- `docs/plans/active/*` — plans past review, in implementation.
- `docs/plans/completed/*` — archived plans.
- `docs/plans/tech-debt-tracker.md` — open and resolved tech-debt entries (#NN format).
- `src/{your-project}/` — this project's source code (governed by `docs/law/CODE_AUTHORSHIP_AND_STEWARDSHIP_RULES.md`). Path varies by project.
- `tests/` — this project's test suite (when present).
- `references/*` — this project's local reference files (notes, design drafts, PDFs).
- `memory/` — canonical markdown memory layer (source-of-truth): `working-set.md`, `LOOKUP_RULES.md`, `preferences.md`, `known-facts/*.md`, `logs/YYYY-MM/*.md`, `rules/*.md`.
- `.harness/` — derived runtime/index state (rebuildable from `memory/*`). `.harness/index/meta.db` holds SQLite + FTS5 + sqlite-vec.

MCP tool surface routing: `docs/contracts/agentlaw-mcp-tools.md` (tool descriptions, parameters, error contracts, §Tool Description Format).

Code architecture and module connections: `docs/references/project-overview.md` § Code architecture map (when this project documents one).

## Current Priority
1. Keep this project's law, references, plans, and memory aligned with actual
   practice.
2. Keep project-specific facts in the approved project artifacts named above.
3. Run the matching root control document before fix, init, or update work.
