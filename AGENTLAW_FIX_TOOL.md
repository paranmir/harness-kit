# Harness Fix Tool

## Purpose
Use this document as a practical decision tool when a current problem, harness escape, repeated mistake, or governance gap appears.

This document helps you:
- describe the problem clearly
- decide whether it is already governed
- choose the right target document
- define what would make the failure class sufficiently closed
- choose the least correction that satisfies that sufficiency contract

It does not replace the constitution, the law layer, operational artifacts, or execution-entry documents.

## When To Use This Document
Use this document when one or more of the following is true:
- an agent bypassed the harness
- a repeated mistake appeared in review
- a current problem is visible but the owning document is unclear
- a known rule exists but seems too weak, too hidden, or too easy to bypass
- a localized law document retains generic starter placeholder content despite concrete project facts being available
- installer, memory, or continuity state appears to be bypassing file-based governance
- the MCP memory tool surface is being bypassed (raw SQL against `.harness/index/meta.db`, direct file reads where a memory tool exists, or hand-rolled walks where `agentlaw_session_restore` / `agentlaw_session_save` should be used)
- the canonical Markdown layer under `memory/*` and the index under `.harness/index/meta.db` have drifted apart
- a post-task retrospective identifies a reusable agent behavior correction and the owning layer is unclear
- you need to decide whether the response belongs in law, tracker, `AGENTS.md`, an agent skill, or enforcement

Do not use this document as a substitute for normal project work when no current governance problem exists.

For distribution or setup-related fixes, use the current public distribution model as the starting fact: install and upgrade come from the PyPI package `agentlaw` (`pipx install agentlaw`, `pipx upgrade agentlaw`), while `https://github.com/paranmir/agentlaw` is the public seed repository for inspecting target-facing starter content. Do not route target users to a local development workspace or a removed publish repository.

## Required Inputs
Before using this document, read:
- `AGENTLAW_CONSTITUTION.md`
- the relevant file in `docs/law/*`
- `plans/tech-debt-tracker.md` when repetition or promotion may already be tracked
- `AGENTS.md` when entry routing is part of the problem

## Direct Procedure
Use the step-by-step body below as the primary procedure.
The quick flow is a map; Steps 1 through 7 are the executable sequence.

## Completion Checks
A fix-analysis run is complete only when:
- the observed problem is described with evidence
- the failure class and likely recurrence surfaces are named
- the sufficiency contract is defined before choosing a correction
- the owning layer is explicit
- correction candidates were compared against the sufficiency contract
- the chosen correction survived adversarial review for under-fix and over-fix
- the correction type is labeled honestly
- tracker and enforcement follow-up were considered
- related synchronization targets were checked when structure changed

## Failure Conditions
Treat the result as incomplete when:
- the write-up starts with a new rule name instead of the observed problem
- a higher-order rule is changed before checking lower-order corrections
- a new rule is added without checking merge, simplification, narrowing, or deletion alternatives
- repeated or mechanically detectable problems are left prose-only without a real reason
- "smallest" is treated as the smallest text or code diff rather than the least correction that satisfies a defined sufficiency contract
- cleanup, workaround, or tracker-only treatment is presented as a fix without closing the failure class
- no rejected alternatives or adversarial review are recorded for a non-trivial fix

## Quick Use Flow
1. Record the problem in plain terms.
2. Classify and generalize the failure class.
3. Check whether the discrepancy is important enough for law, tracker, or enforcement handling.
4. Check existing rules first.
5. Define the sufficiency contract.
6. Choose the owning layer or abstraction.
7. Generate and score correction candidates.
8. Choose the least sufficient candidate and run adversarial review.
9. Apply the correction and label its type honestly.
10. Verify that references, tracker entries, synchronization targets, and enforcement follow-up were not missed.

## Step 1. Record The Problem
Write down:
- what happened
- what should not have happened
- what evidence supports that conclusion
- whether it seems one-off or repeated
- whether it looks local to this project or shared across multiple projects

Do not begin by inventing a rule name. Begin by describing the observed problem.

## Step 2. Classify And Generalize The Failure Class
Classify the current problem as one or more of:
- `structure problem`
- `scope problem`
- `contract problem`
- `oracle problem`
- `failure problem`
- `entry problem`
- `execution tracking problem`
- `installer problem`
- `memory authority problem`
- `recursive promotion problem`
- `discipline problem`

Then decide which of these best describes the gap:
- `missing rule`
- `weak rule`
- `misplaced rule`
- `ignored rule`
- `mechanical gap`
- `genericization gap`

Use this step to understand the problem before deciding where to write anything.

Then generalize the observed problem into a failure class:

- Is this a single local incident, or a class of failures?
- Where else can the same class recur?
- Which neighboring cases would make the same mistake likely?
- Does the class involve false readiness, missing authority, misplaced ownership, weak oracle, abstraction drift, enforcement absence, or only residue cleanup?

Do not choose a correction until the failure class and recurrence surfaces are visible. If the issue is only residue cleanup, label it as cleanup rather than fix.

## Step 3. Check Whether The Discrepancy Is Important
Do not assume every discrepancy belongs in the law layer.

Treat a discrepancy as important enough for law or tracker handling when one or more of the following is true:
- it changes scope, contract meaning, oracle meaning, failure interpretation, execution flow, or regression expectations
- it creates user-visible misleading behavior
- it creates maintenance risk by making false assumptions likely
- it can cause runtime breakage, invalid results, or failed flows if left implicit
- it is repeated, likely to recur, or is already a candidate for stronger enforcement

Do not treat a discrepancy as law-worthy by default when it is:
- a one-off local implementation detail with no governance impact
- obvious from code but not important to project meaning, review safety, or future maintenance
- already adequately governed by an existing law statement

## Step 4. Check Existing Rules First
Before adding a new rule, review:
- `AGENTLAW_CONSTITUTION.md`
- the relevant file in `docs/law/*`
- `AGENTS.md` if the issue concerns read-first order or short entry guardrails
- `plans/tech-debt-tracker.md` if the issue may already be tracked

The review must use **multiple synonym variants of the proposed rule's title and intent**, not a single keyword pass. A search that uses only the phrasing the agent has in mind will systematically miss rules whose original authors used different phrasing for the same intent. Concrete example: a proposed `Comment Self-Narration Prohibition` rule must also grep for `implementation history`, `changelog`, `narration`, `rename history`, `historical context`, and the specific anti-patterns the proposed rule would prohibit. Without the synonym sweep, near-duplicate § s land and the law layer drifts into self-contradiction. The full version of this obligation lives in `docs/law/REPOSITORY_ARTIFACT_RULES.md` `§New Section / Rule Addition Rule`.

Ask:
1. Does an existing rule already govern this problem?
2. Can the current rule be clarified or strengthened instead of adding a new one?
3. Is the real issue that the rule was ignored rather than missing?
4. Would a local amendment solve the problem without changing higher-order structure?
5. Can an existing rule be merged, simplified, narrowed, or deleted instead of adding another layer of text?

Default to amendment of existing text rather than creating parallel meaning.

## Step 5. Define The Sufficiency Contract
Before choosing a correction, define what would make the fix sufficient.

A sufficiency contract answers:

1. What failure class is being corrected?
2. Which recurrence paths must be closed within the owning scope?
3. Which recurrence paths are intentionally left open, and why?
4. What abstraction, rule, tool, or artifact boundary must remain coherent after the correction?
5. What oracle, negative test, verifier check, review evidence, or explicit infeasibility rationale will prove the correction?
6. What extension path must remain open so future work does not need to undo this correction before improving the system?

For code, verifier, CLI, MCP, schema, installer, or runtime changes, the sufficiency contract must respect `docs/law/CODE_AUTHORSHIP_AND_STEWARDSHIP_RULES.md`: maintainability and testability are part of correctness. "Smallest" does not authorize one-off branching, responsibility mixing, missing tests, or avoidable technical debt.

If the chosen correction does not satisfy the sufficiency contract, it is not a fix. It is a workaround, cleanup, tracker-only record, or partial fix.

## Step 6. Choose The Owning Layer Or Abstraction
Use this ownership map:
- constitutional structure and invariant starter protections -> `AGENTLAW_CONSTITUTION.md`
- starter carry-forward and project-instance preservation rules -> `docs/law/STARTER_SPECIALIZATION_RULES.md`
- scope and repository boundary -> `docs/law/SCOPE.md`
- inputs, outputs, execution flow, regression expectations, readiness -> `docs/law/INPUT_OUTPUT_CONTRACT.md`
- structural and behavioral judgment -> `docs/law/ORACLE_AND_JUDGMENT.md`
- failure classes and recovery interpretation -> `docs/law/FAILURE_TAXONOMY.md`
- artifact structure, approval, synchronization, directory growth, law/artifact/entry separation -> `docs/law/REPOSITORY_ARTIFACT_RULES.md`
- installer behavior and runtime input/output expectations -> `docs/law/INPUT_OUTPUT_CONTRACT.md`
- memory authority, layout, conflict handling, and session restore behavior -> `docs/law/MEMORY_AND_CONTINUITY_RULES.md`
- MCP memory tool surface bypass and `.harness/index/meta.db` source-drift -> `docs/law/MEMORY_AND_CONTINUITY_RULES.md`
- memory authority, installer judgment, and recursive promotion judgment -> `docs/law/ORACLE_AND_JUDGMENT.md`
- installer drift, memory authority failure, and recursive promotion failure classes -> `docs/law/FAILURE_TAXONOMY.md`
- short read-first routing and short entry guardrails -> `AGENTS.md`
- reusable agent procedure reminders or workflow execution guidance -> agent skill (`.agents/skills/*` / `.claude/skills/*`) when the correction teaches agents how to perform a recurring action without changing law, public contract, verifier behavior, or project state
- repeated debt, unresolved drift, promotion candidates -> `plans/tech-debt-tracker.md`
- multi-step active corrective work -> `plans/active/*`
- durable non-authoritative supporting context -> `references/*`

Do not move a local project problem into the constitution unless it exposes a genuinely shared structural gap.

## Step 7. Generate And Score Correction Candidates
Generate at least two plausible correction candidates for non-trivial fix-analysis runs, unless only one candidate is structurally possible.

Typical candidates include:

- cleanup only
- tracker-only record
- local prose clarification
- amendment of an existing rule or protocol
- runtime reminder or prompt support
- skill creation or skill update
- verifier, test, lint, or CI enforcement
- enabling refactor before behavior change
- broader redesign or new artifact, only when existing abstractions cannot own the failure class

Score each candidate briefly against:

- recurrence closure: how much of the failure class it closes
- abstraction fit: whether it belongs at the owning layer or boundary
- oracle strength: whether the result is executable, mechanically checkable, or otherwise verifiable
- extension safety: whether future work can extend it without undoing the correction
- cost and reversibility: whether the scope is proportionate and recoverable

Reject candidates that win only because they are smaller while failing the sufficiency contract. Also reject candidates that satisfy the contract only by overbuilding a new abstraction, broad rewrite, or policy layer when a narrower owned correction would work.

## Step 8. Choose The Least Sufficient Candidate And Run Adversarial Review
Choose the least costly candidate that satisfies the sufficiency contract.

Then attack the chosen candidate before applying it:

1. Does it close the failure class, or only remove the current symptom?
2. Can the same failure recur in a neighboring case the correction does not cover?
3. Does it bypass the owning abstraction with a special case?
4. Will future extension need to undo this correction first?
5. Is the oracle missing, too weak, or normal-case-only?
6. Was there a lower-cost candidate that satisfied the same contract?
7. Is this over-designed relative to the observed failure and recurrence risk?

If the adversarial review finds under-closure, bad abstraction fit, missing oracle, or over-design, revise the candidate or the sufficiency contract before applying a correction.

Record rejected alternatives for non-trivial runs. The record may be short, but it must explain why cleanup-only, prose-only, enforcement, or redesign was accepted or rejected when those options are relevant.

## Step 9. Apply The Correction And Label Its Type
Use this ladder after the sufficiency contract and candidate review:
1. no rule addition
2. tracker entry only
3. local law amendment
4. short `AGENTS.md` guardrail addition
5. agent skill creation or update
6. constitutional amendment
7. enforcement follow-up under `docs/law/MECHANICAL_ENFORCEMENT_POLICY.md`

Use:
- `tracker only` when the issue is real but not stable enough for a governing rule
- `local law amendment` when the meaning clearly belongs to a law document
- `AGENTS.md` only for short entry-order corrections
- `agent skill creation or update` when the correction is reusable agent-facing procedure support, such as how to perform a recurring workflow, choose review inputs, or remember to consult a root control tool
- `constitutional amendment` only for shared structural gaps or starter invariants

Do not use a skill when the failure belongs to the constitution, root control documents, `docs/law/*`, tests, verifier, lint, CI, public setup documentation, release metadata, or a tracker. A skill may remind an agent to follow those authorities, but it must not become a lower-authority substitute for them. A post-task retrospective can recommend a skill only when the observed failure is a reusable agent-action pattern rather than a one-time chat correction or a higher-authority governance gap.

Before choosing any rule-adding option, explicitly check whether the better correction is:
- merging overlapping rules
- simplifying an overgrown rule
- deleting stale text that no longer governs real behavior
- narrowing an over-broad rule instead of adding a second one

For existing-project bootstrap failures:
- use at least `local law amendment` when concrete repository facts are visible but law text remains generic
- use at least `tracker entry only` when important discrepancies exist but remain untracked

Label the correction honestly:

- `fix`: closes the named failure class within the owning scope
- `partial fix`: closes some recurrence paths and names the remaining paths
- `workaround`: mitigates the current symptom without closing recurrence
- `cleanup`: removes residue without changing recurrence risk
- `tracker-only`: records a known gap without correcting it yet
- `enforcement`: mechanically rejects recurrence through tests, verifier, lint, CI, schema validation, or equivalent checks

Do not call a cleanup, workaround, or tracker-only record a fix. It can still be the right action, but its label must preserve the residual risk.

**Owning-layer presence enforcement.** When applying FIX_TOOL, output lands in the owning layer per the Step 9 ladder above — tracker entry, local law amendment, AGENTS.md guardrail, agent skill, constitutional amendment, or mechanical enforcement. Output MUST NOT land in agent-local memory (host-private feedback files, conversation-only notes, scratch files outside `docs/` / `src/` / `tests/`). Lighter-alternative output channels bypass the ladder's owning-layer escalation and are an instance of the false-readiness meta-family (form-correct "fix-tool applied" with substance-incomplete output channel). See `docs/law/MECHANICAL_ENFORCEMENT_POLICY.md` §"form-vs-substance detection check" for the mechanical check.

## Step 10. Verify Completion
After making the correction, check:
- does the new text address the actual problem
- does it satisfy the sufficiency contract
- did adversarial review leave any unresolved under-fix or over-fix risk
- is it placed at the correct layer
- did it preserve existing protections
- did it avoid unnecessary escalation
- did it keep `AGENTS.md` short
- did another related document also need updating
- for existing-project work, were important local facts turned into readable law
- for existing-project work, do important discrepancies remain explicitly documented in law or tracker form

If document movement, splitting, renaming, or relocation happened, also check:
- `AGENTS.md`
- `AGENTLAW_CONSTITUTION.md`
- the affected files in `docs/law/*`
- tracker or plan references

Detailed structural synchronization rules live in:
- `docs/law/REPOSITORY_ARTIFACT_RULES.md`

## When To Escalate To Enforcement
If the same problem keeps recurring and is mechanically detectable, do not keep solving it only with prose.

Use:
- `docs/law/MECHANICAL_ENFORCEMENT_POLICY.md`

Especially when:
- the problem is repeated
- the cost of failure is meaningful
- the failure can be detected through lint, CI, structural checks, tests, or equivalent mechanisms

## Recommended Output Shape
When writing up the result of this process, make these explicit:
- `problem`
- `evidence`
- `analysis`
- `failure class`
- `recurrence surfaces`
- `sufficiency contract`
- `repetition status`
- `existing rule check result`
- `merge, simplify, narrow, or delete alternative check result`
- `owning layer`
- `correction candidates`
- `candidate scoring`
- `chosen correction`
- `adversarial review`
- `rejected alternatives`
- `correction type label`
- `chosen target`
- `oracle / negative test / verifier`
- `residual risk`
- `whether tracker follow-up is needed`
- `whether enforcement follow-up is needed`

## Next Update Trigger
Update this document when:
- the decision flow proves unclear in real use
- rule-placement mistakes keep recurring
- the tool stops helping users choose the right layer quickly
