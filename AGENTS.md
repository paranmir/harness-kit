# Repository Guidelines

This file is a routing map only. Governing content belongs in `agentlaw_docs/law/*`.

## What This Repository Is
This repository is governed by the agentlaw kit — a law-first governance harness for AI coding agents.

## Governance Reminder

Before starting agentlaw harness fix / init / upgrade / align / governance work (tracker-policy violation, repeated harness mistake, agentlaw rule bypass, agentlaw setup, kit upgrade, local harness routing drift), read the matching root control document first: `AGENTLAW_FIX_TOOL.md` for fix-class harness work, `AGENTLAW_INIT_TOOL.md` for agentlaw init, `AGENTLAW_UPDATE_TOOL.md` for kit upgrades, `AGENTLAW_ALIGN_TOOL.md` for local routing/readme alignment. Ordinary project bug fixes, features, dependency updates, and app setup use the project's normal planning, review, verification, and memory rules unless the issue affects the agentlaw harness itself. The `.claude/skills/agentlaw-governance/SKILL.md` and `.agents/skills/agentlaw-governance/SKILL.md` skills (Agent Skills open standard) carry the same reminder for Claude Code and Codex respectively.

## Source of Truth
Document priority is:

1. `AGENTLAW_CONSTITUTION.md`
2. Root control documents:
   - `AGENTLAW_INIT_TOOL.md`
   - `AGENTLAW_UPDATE_TOOL.md`
   - `AGENTLAW_FIX_TOOL.md`
   - `AGENTLAW_ALIGN_TOOL.md`
3. `agentlaw_docs/law/*`
4. Approved structured repository artifacts such as `agentlaw_docs/plans/*`, `agentlaw_docs/references/*`, and stable generated facts when they exist
5. Approved continuity or memory records
6. `AGENTS.md` (this file)

## Read First
Check the current agreement in this order:

1. `AGENTLAW_CONSTITUTION.md`
2. Root control tools when relevant
3. Workspace law layer:
   - `agentlaw_docs/law/SCOPE.md`
   - `agentlaw_docs/law/INPUT_OUTPUT_CONTRACT.md`
   - `agentlaw_docs/law/ORACLE_AND_JUDGMENT.md`
   - `agentlaw_docs/law/CODE_AUTHORSHIP_AND_STEWARDSHIP_RULES.md`
   - `agentlaw_docs/law/FAILURE_TAXONOMY.md`
   - `agentlaw_docs/law/REPOSITORY_ARTIFACT_RULES.md`
   - `agentlaw_docs/law/PLANNING_AND_REVIEW_RULES.md`
   - `agentlaw_docs/law/MEMORY_AND_CONTINUITY_RULES.md`
   - `agentlaw_docs/law/STARTER_SPECIALIZATION_RULES.md`
   - `agentlaw_docs/law/MECHANICAL_ENFORCEMENT_POLICY.md`
   - `agentlaw_docs/law/USER_INTENT_ALIGNMENT.md`
4. This file (`AGENTS.md`)

For session recovery, also read:
- `agentlaw_memory/working-set.md`
- `agentlaw_memory/LOOKUP_RULES.md`
- `agentlaw_docs/references/project-overview.md`

## Worktree Map

Routing only — see referenced documents for governing content.

- `README.md` — installation, usage, and project description.
- `LICENSE` — license.
- `AGENTLAW_ALIGN_TOOL.md` — local harness routing/readme alignment procedure; use with `agentlaw align --check --target .` and `agentlaw align --write --target .`.
- `agentlaw_docs/law/*` — workspace law (constitution-derived governance). Read order in §Read First above.
- `agentlaw_docs/contracts/*` — MCP tool surface (`agentlaw-mcp-tools.md`), shared baseline, update workflow.
- `agentlaw_docs/planning-protocol/*` — review-method, task-classification, persona decks (core + specialized), persona-section map, plan template.
- `agentlaw_docs/references/*` — orientation and design notes; `project-overview.md` is the worktree-at-a-glance entry for this project.
- `agentlaw_docs/plans/draft/*` — in-development plans pre-review.
- `agentlaw_docs/plans/active/*` — plans past review, in implementation.
- `agentlaw_docs/plans/completed/*` — archived plans.
- `agentlaw_docs/plans/tech-debt-tracker.md` — open and resolved tech-debt entries (#NN format).
- `src/{your-project}/` — this project's source code (governed by `agentlaw_docs/law/CODE_AUTHORSHIP_AND_STEWARDSHIP_RULES.md`). Path varies by project.
- `agentlaw_tests/` — this project's test suite (when present).
- `references/*` — this project's local reference files (notes, design drafts, PDFs).
- `agentlaw_memory/` — canonical markdown memory layer (source-of-truth): `working-set.md`, `LOOKUP_RULES.md`, `preferences.md`, `known-facts/*.md`, `logs/YYYY-MM/*.md`, `rules/*.md`.
- `.agentlaw/` — derived runtime/index state (rebuildable from `agentlaw_memory/*`). `.agentlaw/index/meta.db` holds SQLite + FTS5 + sqlite-vec.

MCP tool surface routing: `agentlaw_docs/contracts/agentlaw-mcp-tools.md` (tool descriptions, parameters, error contracts, §Tool Description Format).

Code architecture and module connections: `agentlaw_docs/references/project-overview.md` § Code architecture map (when this project documents one).

## Current Priority
1. Keep this project's law, references, plans, and memory aligned with actual
   practice.
2. Keep project-specific facts in the approved project artifacts named above.
3. Run the matching root control document before agentlaw harness fix, init, update, or align work.
