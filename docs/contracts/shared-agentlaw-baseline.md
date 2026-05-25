# Shared Harness Baseline

## Authority
Source of truth for the distribution contract between `agentlaw` and target
projects. The bundled scaffold shares this record, and `agentlaw verify`
checks consistency with the scaffold tree and target layout.

Governing law: `docs/law/REPOSITORY_ARTIFACT_RULES.md`. Amendments land
through a plan that updates this file and any dependent law
clause in the same change.

## Purpose
Current shared-kit baseline reflected in this bootstrapped workspace.

## Baseline Record
- Shared source repository: `https://github.com/paranmir/agentlaw.git`
- Baseline commit SHA: `0af5f9b`
- Baseline tag: `unknown`
- Baseline recorded on: `2026-04-27`
- Recorded by: `v0.1.1 deployment execution plan`
- Notes:
  - Root constitution and root control documents are synchronized into `src/agentlaw/scaffold/*`.
  - `src/agentlaw/scaffold/*` is the active package distribution source.
  - This update includes: failure taxonomy expanded to 7 classes, Restore/Save Request Protocols added to REPOSITORY_ARTIFACT_RULES, genericization gap detection added to FIX/UPDATE tools.
