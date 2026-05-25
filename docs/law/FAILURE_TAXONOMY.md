# Failure Taxonomy

## Purpose
This document classifies governance and execution failures in the current project and defines the expected recovery direction.

## Known
- Early-stage projects can fail before code exists.
- Missing governance is itself a failure mode.

## Unknown
- project-specific incident patterns
- later-stage severity thresholds
- domain-specific failure classes beyond the defaults below

## Assumptions
- Early failure classes should focus on governance structure before product-specific patterns.

## Failure Classes
### 1. Hierarchy Failure
The document order becomes unclear or is violated.

Examples:
- `AGENTS.md` contradicts `AGENTLAW_CONSTITUTION.md`
- a lower-order file is treated as the governing source
- a document refers to obsolete priority rules

Expected recovery:
- restore the correct governing layer
- update lower-order documents to match the higher-order source

### 2. Scope Failure
Work crosses the project boundary incorrectly.

Examples:
- a shared/public artifact becomes project-specific without explicit intent
- `docs/references/*` is treated as the active output target
- execution starts before scope is clarified

Expected recovery:
- restate the project boundary
- move criteria back to the correct context
- restore shared/public artifacts to generalized form when applicable

### 3. Contract Failure
Required inputs, outputs, or update steps are missing.

Examples:
- a request is implemented without checking document impact
- the law layer is bypassed when meaning changes
- a work result omits a required governing-document update
- a shared/public change updates only one affected artifact set when others should move together

Expected recovery:
- identify the missing contract step
- update the correct governing document first
- update the parallel artifact set if shared meaning is affected
- rerun the work in the proper order

### 4. Oracle Failure
The project lacks enough judgment criteria to decide correctness.

Examples:
- acceptance conditions are not documented
- execution or packaging starts without a complete documentation gate
- regression expectations are missing or ambiguous

Expected recovery:
- add the missing judgment rules to the active law layer
- pause work until judgment becomes explicit

### 5. Execution Drift
Execution guidance diverges from the governing documents.

Examples:
- `AGENTS.md` read order no longer matches the current ground truth
- execution instructions point to stale reference work
- execution guidance expands into long-lived rule content

Expected recovery:
- shorten and realign the execution document
- move durable rule content back into the law layer

### 6. Shared Artifact Drift
Shared/public artifacts diverge from intentionally shared meaning without a deliberate reason.

Examples:
- section requirements change in project governance but not in one or more shared/public artifact sets
- a shared/public artifact picks up local authoring assumptions
- derived output no longer reflects the intended shared source

Expected recovery:
- identify whether the divergence is intentional or accidental
- if accidental, realign the affected shared/public artifacts
- if intentional, record the boundary explicitly in the owning documents

### 7. Artifact Drift
Artifact naming, routing, or structure changes without an owning law-layer update.

Examples:
- new directories are created without checking existing artifact classes first
- local governance, shared/public artifacts, and local-only reference material lose their separation
- notes, references, or derived facts begin to carry law-layer meaning without authorization

Expected recovery:
- restore the correct artifact boundaries
- add or update artifact rules before continuing the pattern
- synchronize affected artifact sets if more than one is involved

### 8. Installer Drift
An installable distribution path creates, overwrites, or represents Harness artifacts in a way not governed by the law layer.

Examples:
- installer templates diverge from the intended shared source
- an installer silently overwrites localized law
- an installer claims semantic verification when it only performed structural setup
- default install creates runtime memory state without artifact-rule coverage or authority labeling

Expected recovery:
- restore source-of-truth clarity for installer templates
- document or repair collision handling
- correct misleading installer claims
- update artifact and contract rules before resuming installer work

### 9. Memory Authority Failure
Continuity or memory records are treated as stronger authority than file-based governance.

Examples:
- memory summary overrides `docs/law/*`
- stale memory is presented as current truth
- wrong-scope memory affects a decision
- hidden runtime state carries governing meaning that is absent from repository files
- conflicting memory remains `active` after a higher-authority source contradicts it
- indexes are used after source drift is detected

Expected recovery:
- trust repository files over memory
- mark or repair the memory item as stale, superseded, disputed, suppressed, quarantined, or excluded
- add missing provenance, scope, or conflict-handling rules
- promote durable governing meaning into law, plans, references, or generated facts only through reviewed changes

### 10. Recursive Promotion Failure
Recursive improvement moves from observation to governance without the reviewed promotion path.

Examples:
- memory-derived suggestions directly edit law
- repeated failures are applied to shared artifacts without shared/local boundary review
- improvement candidates bypass plans, trackers, or law-layer judgment

Expected recovery:
- move the candidate back into a plan, tracker, or reference
- review whether it is local or generalizable
- update law and shared artifacts only through the normal synchronization path

## Severity Interpretation
- Critical: hierarchy failure or scope failure that changes the governing source
- High: contract failure or oracle failure that allows work without proper documentation
- Medium: execution drift, shared-artifact drift, artifact drift, installer drift, or contained memory drift that misroutes future work but does not yet alter the governing source
- Low: wording or reference defects that do not currently change behavior

## Recovery And Regression Expectations
Every identified failure should produce:
- a named failure class
- the owning document layer
- the required recovery action
- a check that similar drift is less likely after the fix

Regression review should explicitly check for repeated hierarchy, scope, contract, oracle, execution-drift, shared-artifact-drift, artifact-drift, installer-drift, memory-authority, and recursive-promotion failures after each governance change.

## Runner Failure Checks
The runner flow should explicitly check for:
- failure to perform the main in-scope flow successfully
- failure to preserve the expected result across a reload, restart, repeat run, or equivalent round-trip when continuity matters
- failure to target the intended entity, record, or state during mutation
- failure to reject, block, or contain invalid input when such handling is part of the contract
- failure caused by silent scope expansion or undocumented behavior changes

## Structure Expansion Failures
The project should also classify these as governance failures when growth makes them relevant:
- `plan gap`: complex work is proceeding without a repository-tracked plan
- `reference gap`: important reference knowledge exists only outside the repository
- `enforcement gap`: a repeated invariant remains prose-only after it is known to be drift-prone
- `agents overload`: `AGENTS.md` accumulates durable knowledge that should have moved into structured artifacts

## LLM-Behavior-Level Failure Tags (cross-reference)

`docs/law/USER_INTENT_ALIGNMENT.md` introduces a behavior-level
failure-tag vocabulary that names *what the LLM did wrong in the
last turn*, distinct from the structural categories above (which
name *which layer the system failed at*). The two systems are
complementary: a single incident usually carries one behavior tag
and one (or more) structural category. The mapping below names
the natural primary-category anchor for each behavior tag so
incidents can be filed against both.

| Behavior tag | Anchored structural category | Notes |
| --- | --- | --- |
| `requirement_miss` | Contract Failure | User-stated requirement absent from output → contract gap |
| `constraint_violation` | Scope Failure / Contract Failure | Acted against user-stated or law-stated constraint |
| `over_questioning` | Execution Drift | Asked when proceed-with-assumption sufficed; cost burden imposed |
| `under_questioning` | Contract Failure / Oracle Failure | Proceeded with assumption when missing info materially changed correctness or safety |
| `plan_drift` | Execution Drift | Execution diverged from operational contract without updating it |
| `fake_verification` | Oracle Failure | Claimed verification without evidence trace |
| `verification_skip` | Oracle Failure | Acted on load-bearing claim without running available verification |
| `unsafe_action` | Execution Drift | Destructive or irreversible action without proper gate |
| `tool_misuse` | Execution Drift / Contract Failure | Invoked tool outside its documented contract |
| `source_hallucination` | Oracle Failure | Cited a source / location / fact that does not exist |
| `memory_conflict` | Memory Authority Failure | Two memory entries contradict; not reconciled |
| `persona_bloat` | Recursive Promotion Failure | Persona review added bulk without raising substantive quality |
| `cost_bloat` | Execution Drift | Work expanded beyond contract for marginal benefit |

This mapping is a routing aid, not a constraint. A single incident
may anchor to multiple categories (e.g., a `fake_verification` on
a contract-shipping deliverable anchors to both Oracle Failure and
Contract Failure). When filing a recurring-failure entry in
`docs/plans/tech-debt-tracker.md`, record both the behavior tag(s)
and the structural category for full taxonomy coverage.

## Next Update Trigger
Update this document when:
- a new recurring failure pattern appears
- severity interpretation needs adjustment
- recovery expectations change
- regression review needs additional checks
