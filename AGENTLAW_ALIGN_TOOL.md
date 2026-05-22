# Harness Align Tool

## Purpose

Use this document when an initialized project needs its agentlaw routing and
starter-facing documentation reconciled with local harness structure.

This document helps you:
- detect when entry documents no longer mention present root controls or law files
- refresh safe routing references without re-running a full bootstrap
- distinguish local alignment from upstream kit update and corrective fix work
- keep README and agent-entry surfaces discoverable after new harness commands or documents land

It does not replace `AGENTLAW_INIT_TOOL.md`, `AGENTLAW_UPDATE_TOOL.md`, or
`AGENTLAW_FIX_TOOL.md`.

## When To Use This Document

Use this document when:
- a law file, root control document, starter directory, or command surface was added or removed
- `AGENTS.md`, `CLAUDE.md`, README, or project references may be stale relative to the local harness structure
- an update or fix changed shared starter behavior and the target project needs a final routing/readme reconciliation pass
- an agent says a harness surface exists but users cannot find the command or entry path

Do not use this document when:
- the project has never opted into agentlaw — use `AGENTLAW_INIT_TOOL.md`
- the installed package has changed and local law must incorporate upstream requirements — use `AGENTLAW_UPDATE_TOOL.md`
- a repeated rule bypass or governance failure needs root-cause analysis — use `AGENTLAW_FIX_TOOL.md`
- localized law content needs semantic merging or rewriting

## Direct Procedure

1. Read `AGENTLAW_CONSTITUTION.md`, `AGENTS.md`, and this document.
2. Run:

```bash
agentlaw align --check --target .
```

3. Review every reported issue. Treat missing README command guidance as a
   documentation gap, not as a successful alignment.
4. If the report names only safe routing updates, run:

```bash
agentlaw align --write --target .
```

5. Manually update any non-autofixable surfaces named by the report, especially
   README or project reference files.
6. Run:

```bash
agentlaw verify .
```

7. If the change came from an upstream kit upgrade, return to
   `AGENTLAW_UPDATE_TOOL.md` and complete its verification and baseline steps.
   If the change came from a governance failure, return to `AGENTLAW_FIX_TOOL.md`
   and complete its sufficiency-contract checks.

## Completion Checks

An align run is complete only when:
- `agentlaw align --check --target .` reports no unresolved issues
- README or target-facing docs mention new user commands where applicable
- `AGENTS.md` routes to present root controls and law files
- `agentlaw verify .` passes or remaining failures are explicitly outside the align scope
- update/fix/init procedures are resumed when the align run was part of those larger workflows

## Failure Conditions

Treat the result as incomplete when:
- `align --write` was used as a substitute for update or fix analysis
- README still omits a command users need to run
- root control or law files were added but execution-entry routing remains stale
- local law content was overwritten instead of merged through the correct update or fix path
- verification was skipped after changing routing

## Command Reference

```bash
agentlaw align --check --target .
agentlaw align --write --target .
agentlaw align --check --target . --json
```

`--check` reports unresolved alignment issues. `--write` applies only safe,
deterministic routing updates and then re-runs the checks. It does not perform
semantic law merges, package upgrades, project initialization, schema
migrations, or README rewrites.

## Next Update Trigger

Update this document when:
- `agentlaw align` gains or loses an autofix category
- root control routing changes
- users repeatedly confuse align with init, update, or fix
