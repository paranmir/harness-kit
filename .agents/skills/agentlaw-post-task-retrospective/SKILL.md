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

Do not create or update a skill by default. First score whether the lesson
needs a durable route at all. Existing law, tests, docs, or memory do not by
themselves justify `chat-only` when the same friction is recurring, costly, or
likely to repeat.

## Closeout Route

1. Name the trigger evidence: user correction, repeated friction, failed check,
   governance gap, useful reusable tactic, missing verifier, or stale guidance.
2. Score these signals with the Promotion Score below:
   - repeated or likely-to-repeat agent procedure
   - user-visible correction or explicit preference
   - rule, governance, memory, MCP, verifier, or publish boundary risk
   - command/tool friction: wrong command, wrong working directory, wrong shell
     form, wrong Python invocation, wrong pytest selector, MCP/raw-tool route
     confusion, tool schema mistake, or repeated retries
   - missing or weak mechanical test/verifier
   - durable project fact, preference, decision, or source
   - skill telemetry or stale-skill concern, if such telemetry exists
   - available `agentlaw_skill_lifecycle_report` evidence, including
     invocation count, last-used timestamp, and stale-candidate status
3. Choose one primary route and any justified secondary route:
   - `skill`: reusable agent procedure that teaches future agents what to do.
   - `law/contract`: rule, public contract, authority, or policy boundary.
   - `test/verifier`: invariant that should fail mechanically when broken.
   - `memory/reference`: durable memory item or reference artifact.
   - `tracker`: known debt or future lifecycle metric that is not fixed now.
   - `chat-only`: low-risk, one-off, and already covered with no meaningful
     future-prevention value.
4. If the route is a real correction, gap, bypass, or repeated failure, start
   from `AGENTLAW_FIX_TOOL.md` before changing files.
5. Persist durable evidence through MCP memory tools when the session produced
   a new fact, rule, preference, decision, correction, verification result, or
   next-action state. Do not edit memory files directly.
6. When the skill lifecycle MCP tools are available, record meaningful use of
   this retrospective route with `agentlaw_skill_event_record`. Record actual
   use such as `invoked` or `applied`; do not inflate telemetry for mere
   reminders or unused matches.

## Promotion Score

Use this score to prevent both extremes: noisy skill/rule creation and
over-conservative `chat-only` closeout.

- +2 user correction or explicit preference.
- +2 repeated or likely-to-repeat workflow shape.
- +2 MCP/tool-route ambiguity, fallback, bypass, or governance-route confusion.
- +1 verification failure, retry, or recovery step with low impact.
- +2 recurrence likely in a future session.
- +1 compaction/restart risk if evidence is not saved.
- +2 existing guidance was present but the agent did not actually apply it.
- +1 route clarity: the lesson clearly belongs in skill, law/contract,
  test/verifier, memory/reference, or tracker.

Command/tool friction has its own scoring:

- +1 one wrong command or tool call was corrected locally with little cost.
- +2 repeated wrong command/tool calls, wrong selector, wrong shell form, wrong
  working directory, wrong Python invocation, MCP/raw-tool route confusion, or
  tool schema mistake caused retries.
- +3 the friction happened at a high-impact boundary such as verification,
  commit, push, publish, memory, MCP, hook, or governance.

Promotion thresholds:

- 0-1: `chat-only` is allowed when no future-prevention value remains.
- 2-3: memory log/reference is usually required.
- 4-5: tracker or test/verifier must be considered explicitly.
- 6 or more: choose a durable route or state a specific follow-up
  recommendation; do not silently close as `chat-only`.

`chat-only` is not allowed when promotion evidence includes user correction
plus recurrence likelihood. `chat-only` is also not allowed for repeated
command/tool friction, user-visible command/tool friction, or high-impact
command/tool friction.

## Friction Candidate Loop

During meaningful work, if a strange delay, repeated correction, tool timeout,
cleanup failure, route ambiguity, or likely-to-repeat agent mistake appears,
record a compact friction candidate through MCP memory or the next
`agentlaw_session_save` log entry. Use a short title plus: trigger evidence,
affected surface, current workaround, whether user approval may be needed, and
the smallest pointer to the durable artifact or command output.

At closeout, classify each candidate into exactly one outcome:

- `already-covered`: existing law, test, verifier, skill, tracker, or docs cover
  the issue and leave no meaningful future-prevention value; cite the covering
  artifact and why recurrence is already controlled.
- `chat-only`: low-risk local clarification that does not need durable change
  and does not cross the Promotion Score thresholds.
- `memory/reference`: durable fact or investigation note without a rule change.
- `tracker`: real debt or future enforcement candidate not fixed now.
- `bugfix-reference`: concrete defect needing a reference note before planning.
- `implementation-plan-needed`: approved follow-up work requires a reviewed
  plan before execution.

Do not auto-create an executable plan, mutate governance files, publish, or
perform destructive cleanup from a friction candidate alone. For non-skill fixes,
tell the user the recommended route in chat and ask for approval before turning
the candidate into a bugfix reference or implementation plan.

## Skill Decision Rule

Create or update a skill when the lesson is a reusable agent workflow, the
Promotion Score shows durable prevention value, and the right behavior cannot
be reliably recovered from existing law, contracts, tests, references, or
memory. Prefer updating an existing skill over adding a new one when the scope
is the same.

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
