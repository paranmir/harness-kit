---
name: agentlaw-governance
description: |
  agentlaw governance procedure reminder. Activate when starting fix /
  init / upgrade / align / governance work: tracker-policy violation, repeated
  mistake, rule bypass, new project setup, agentlaw kit upgrade, or any
  case the user signals with phrases like "fix this", "고치자", "rule
  was violated", "governance gap", "init project", "set up agentlaw",
  "upgrade kit", "kit update", "align", "routing drift", "초기화", "업그레이드". Reads and
  surfaces the matching AGENTLAW_*_TOOL.md procedure before action.
---

# agentlaw Governance Reminder

Activate this skill **before** starting fix / init / upgrade / align class work
on an agentlaw-bootstrapped project. The skill exists to prevent the
recurring pattern where agents jump to plan-spawning / direct action
without first consulting the matching root-control document.

## When to read which document

| You are about to ... | Read first |
| --- | --- |
| Diagnose a governance gap, repeated mistake, or rule bypass; spawn a fix-class plan | `AGENTLAW_FIX_TOOL.md` |
| Set up a new project from the agentlaw kit; bootstrap memory / scaffold / runtime | `AGENTLAW_INIT_TOOL.md` |
| Upgrade an existing agentlaw target to a new kit version; incorporate upstream changes | `AGENTLAW_UPDATE_TOOL.md` |
| Reconcile local routing or README surfaces after laws, root controls, directories, or command surfaces changed | `AGENTLAW_ALIGN_TOOL.md` |

These documents live at the **project root** of an agentlaw-bootstrapped
target. If you are in a target project that does not have them at the
root, run `agentlaw init` first.

## Why this skill exists

The agentlaw runtime + plan-review machinery + memory layer already
surface these documents via several channels (`AGENTS.md` routing,
`MEMORY_AND_CONTINUITY_RULES.md` § Canonical Restore Route,
`PLANNING_AND_REVIEW_RULES.md` § before_action_authority_check, MCP
tool description BEFORE-CALL prereqs). Each channel depends on the
agent actively reading at the right moment. Recurrence evidence
(direct user feedback: "fix툴 안읽어? 아 이거 매번 이러네") shows
the active-read assumption fails in practice — the agent skips
straight to plan-spawning.

This skill is the **persistent reminder channel**:

1. Loaded automatically every session (Claude Code: from
   `.claude/skills/agentlaw-governance/`; Codex: from
   `.agents/skills/agentlaw-governance/`).
2. Description-matching activates when the user's request contains
   the trigger keywords listed in the frontmatter.
3. Once activated, the agent reads the relevant `AGENTLAW_*_TOOL.md`
   **before** proceeding to plan-spawning, edits, or other
   action-consequential steps.

## Operational contract

When activated, the agent must:

1. Identify which root-control document matches the current work
   (per the table above; use FIX_TOOL if uncertain — fix-class is
   the most general).
2. Read the document in full before any plan-spawning or other
   action.
3. Apply the document's procedure (e.g., FIX_TOOL Steps 1-10) in
   order, recording outputs in the plan body or memory log as the
   procedure prescribes.
4. Do **not** mark the procedure complete by reading alone — the
   procedure must be applied. "Already read" is not preservation
   across turns; re-activate this skill any time the work crosses
   into a new fix / init / upgrade / align scope.

## Skill lifecycle

- Deployed by `agentlaw init` to both `.claude/skills/` and
  `.agents/skills/` of the target project.
- Refreshed by `agentlaw init --merge` when the target is re-bootstrapped
  (additive; does not overwrite existing customizations).
- Open-standard SKILL.md format (agentskills.io) — cross-platform
  between Claude Code, Codex CLI, Cursor, Gemini CLI, and GitHub
  Copilot.

## Cross-references

- `AGENTLAW_CONSTITUTION.md` — top-level governance constitution.
- `AGENTLAW_FIX_TOOL.md` — fix-class procedure (Steps 1-10).
- `AGENTLAW_INIT_TOOL.md` — init/bootstrap procedure.
- `AGENTLAW_UPDATE_TOOL.md` — kit upgrade procedure.
- `AGENTLAW_ALIGN_TOOL.md` — local routing/readme alignment procedure.
- `AGENTS.md` — project-root agent routing map.
- `agentlaw_docs/law/PLANNING_AND_REVIEW_RULES.md` § before_action_authority_check —
  the law-layer obligation this skill surfaces as a channel.
