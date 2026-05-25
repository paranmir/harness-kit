---
name: agentlaw-post-task-retrospective
description: |
  Post-task retrospective route for agentlaw. Activate before the final
  response after meaningful fix, implementation, plan/oracle/archive, publish,
  verification, or governance work; after repeated mistakes, user corrections,
  or workflow friction; and when prompts mention retrospective, 회고, skill,
  스킬, skill creation/update, durable lesson, or whether a lesson should become
  a skill, law, test, verifier, memory, reference, tracker entry, or chat-only
  note.
---

# agentlaw Post-Task Retrospective

Use this skill near task closeout, before the final user response, when the
work produced a reusable lesson or exposed a process gap.

Do not create or update a skill by default. First decide whether the lesson
needs any durable route at all.

## Closeout Route

1. Name the trigger evidence: user correction, repeated friction, failed check,
   governance gap, useful reusable tactic, missing verifier, or stale guidance.
2. Score these signals as present or absent:
   - repeated or likely-to-repeat agent procedure
   - user-visible correction or explicit preference
   - rule, governance, memory, MCP, verifier, or publish boundary risk
   - missing or weak mechanical test/verifier
   - durable project fact, preference, decision, or source
   - skill telemetry or stale-skill concern, if such telemetry exists
   - available `agentlaw_skill_lifecycle_report` evidence, including
     invocation count, last-used timestamp, and stale-candidate status
3. Choose one primary route and any justified secondary route:
   - `skill`: reusable agent procedure that teaches future agents what to do.
   - `law/contract`: rule, public contract, authority, or policy boundary.
   - `test/verifier`: invariant that should fail mechanically when broken.
   - `memory/reference`: durable fact, preference, decision, source, or context.
   - `tracker`: known debt or future lifecycle metric that is not fixed now.
   - `chat-only`: low-risk, one-off, or already covered by existing artifacts.
4. If the route is a real correction, gap, bypass, or repeated failure, start
   from `AGENTLAW_FIX_TOOL.md` before changing files.
5. Persist durable evidence through MCP memory tools when the session produced
   a new fact, rule, preference, decision, correction, verification result, or
   next-action state. Do not edit memory files directly.
6. When the skill lifecycle MCP tools are available, record meaningful use of
   this retrospective route with `agentlaw_skill_event_record`. Record actual
   use such as `invoked` or `applied`; do not inflate telemetry for mere
   reminders or unused matches.

## Skill Decision Rule

Create or update a skill only when the lesson is a reusable agent workflow and
the right behavior cannot be reliably recovered from existing law, contracts,
tests, references, or memory. Prefer updating an existing skill over adding a
new one when the scope is the same.

Do not use a skill for project facts, one-off decisions, public contracts,
mechanical invariants, or source material. Route those to memory, references,
contracts, tests, verifiers, or tracker entries instead.

Skill lifecycle telemetry is evidence, not authority. A stale-skill candidate
report may inform this decision, but it must not trigger automatic skill
creation, deletion, archive, quarantine, or file rewrites.

## Completion Note

In the final response, report the durable route taken, the skill create/update
decision, and the verification performed. The skill decision must be explicit:
state whether a skill was created, updated, or deliberately left unchanged, and
give the short reason. If no durable route was warranted, say that the
retrospective found no promotion target and that no skill change was made.
