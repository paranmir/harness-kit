# Plan Template

## Purpose

This is the canonical structural skeleton for repository-tracked plans. The
template carries **structure only**; classification, review obligations,
persona selection, and skip conditions live in
`agentlaw_docs/law/PLANNING_AND_REVIEW_RULES.md` and operational files under
`agentlaw_docs/planning-protocol/`.

## Layered Authority

Use this template after routing through the authority files:
`task-classification.md` decides whether a plan is needed and whether it is
trivial or non-trivial; `PLANNING_AND_REVIEW_RULES.md` decides required fill
depth; `review-method.md` selects personas; `Execution Gates` records material
follow-up questions and approval boundaries.

## Section-Fill Profile

- **Trivial plan-required work**: fill `Status`, `Classification`,
  `Intent`, `Scope`, and `Plan Review Evidence` (Trigger Coverage Verifier
  pass only). Other sections may be marked N/A.
- **Non-trivial plan-required work**: fill all applicable sections. Sections
  for unmarked conditional domains may be marked N/A.
- **Plans that introduce or revise rule-system artifacts**: include a
  `Bootstrap Transitional Declaration` section per
  `agentlaw_docs/law/PLANNING_AND_REVIEW_RULES.md` § Bootstrap Transitional Exemption.

Remove instructional bracket text from final plans unless it is being filled.

---

## Template Body

Copy from here downward into a new plan file under `agentlaw_docs/plans/active/`.

```markdown
# [Plan Title]

## Status

- Status: draft / active / completed.
  Use `draft (review skipped per user request, not executable)` when
  the user explicitly asked for draft-only output (see
  `agentlaw_docs/law/PLANNING_AND_REVIEW_RULES.md` § Plan Request Default).
  This label signals the plan is non-executable until reviewed.
  When the user explicitly authorizes skipping review and proceeding with
  execution, use `active` and record `Review required: no`, `Plan reviewed:
  no`, and the authorization in `Review exemption reason`.
- Date authored: YYYY-MM-DDTHH:MM:SS+HH:MM
- Author/agent context: [optional, free-form]

## Bootstrap Transitional Declaration

[Include this section ONLY when the plan introduces or revises an artifact
under agentlaw_docs/law/PLANNING_AND_REVIEW_RULES.md or agentlaw_docs/planning-protocol/.
Otherwise omit. See PLANNING_AND_REVIEW_RULES.md § Bootstrap Transitional
Exemption for the required form.]

## Classification

- Task class(es): [from task-classification.md]
- Plan required: yes
- Reason: [why a plan is required for this task]
- Risk triggers matched: [cited triggers from task-classification.md
  § Non-Trivial Triggers; empty list if zero matches]
- Importance/Risk: trivial / non-trivial
- Primary output contract: [shape of the deliverable]
- State changes: [what mutates during execution]
- Freshness/source requirement: [time-sensitive inputs, if any]
- User gate required: yes / no, before what

## Domain Coverage

[Non-trivial plans only. Self-mark the conditional domains that apply.
Trivial plans may omit this section.]

- [ ] Security & Trust Boundaries
- [ ] Data Stewardship
- [ ] External Contract & Compatibility
- [ ] Time & Sequencing
- [ ] Cost, Resource & Performance
- [ ] Long-term Care
- [ ] Self-Application (rule/tooling-introducing plans)
- [ ] Conversation Memory & Continuity (plans touching memory_logs / memory_items)

Universal domains — Intent, Scope, Premise, Risk, Verification, Confidence,
Continuity, Governance — always apply and do not need self-marking.

Declare substance-deck markers only when the plan actually binds that
substance. `agentlaw_substance_deck_list` lists registered markers and the
deck file carries command defaults and thresholds.

```text
- substance: code   # binds the code substance deck (pytest, mutmut, hypothesis)
```

## Clarification Gate

Fill before drafting begins. Non-trivial plans require the gate; trivial plans
may use a one-line no-question record.

- Gate run: yes / deferred (with reason)
- Source of clarified requirements: [conversation, prior plan, memory log]
- Pre-confirmed requirements: [bulleted list of constraints the user has
  already stated explicitly; each item attributable to a specific source
  so a future reviewer can verify the attribution]
- Reason for proceeding without further user questions: [why the
  remaining unknowns do not materially change scope, safety, contract,
  cost, irreversible action, or intended outcome]
- TBDs deferred to execution: [items that will be resolved during
  execution, with a stop condition naming when each TBD must escalate
  back to user]
- Stop conditions if a TBD turns into a hard requirement: [pause-and-ask
  triggers]

## Clarification Policy

[First-class field per `agentlaw_docs/law/USER_INTENT_ALIGNMENT.md` §Clarification
Rule. It records when to ask vs when to assume, both at the gate and during
execution.]

- **ask_now**: [questions whose missing answer changes correctness, safety,
  user-visible result, cost/time burden, legal/security/governance risk, or
  reversibility; name the trigger.]
- **proceed_with_assumption**: [low-risk, reversible, or inferable assumptions;
  name the rationale and escalation trigger.]

For full plans, this section is recorded by the Clarification Gate. For
lightweight plans, it is the required clarification artifact.

## Intent

- Restated ask: [in the author's own words; user-confirmable form]
- Underlying need: [if literal ask might mask a deeper goal]
- Final goal: [what must be true when the task is complete]
- Intermediate goals: [necessary milestone states, not fixed execution steps]
- Output artifact shape: [specific form, format, length]
- Acceptance criteria: [concrete verification action; no vague phrases.
  When a criterion asserts specific code behavior (named module, function,
  symbol, code path, or test name), the criterion body must carry a
  `Verification trace: <file:line | test:<test_name> | unverified-hypothesis>`
  token per `agentlaw_docs/law/PLANNING_AND_REVIEW_RULES.md` § Code-Fact Claim
  Verification Trace; the Code-Fact Claim Verifier persona enforces this at
  plan-review time. Each criterion is written as `crit-N` with an `Oracle:`
  marker (see § Oracle Marker Selection below).]

### Oracle Marker Selection

Every `crit-*` carries an `Oracle:` marker for
`agentlaw_plan_review_oracle_check`.

Use this canonical shape for mechanically checkable code criteria:

```markdown
- `crit-1`: [observable completion condition]
  Verification trace: `test:test_name` or `path/to/file.py:line`
  Oracle: `py -3.11 -m pytest path/to/test_file.py::test_name -q`
```

`runnable Oracle:` is accepted as an explicit synonym, but it is not required.
The parser reads the criterion block through the next `crit-*`; explanatory
lines may appear between the criterion and its Oracle. `Oracle: user_confirms`
is the canonical human-judgment form.

- **Runnable Oracle**: an `Oracle:` containing an explicit direct-argv command is
  required for mechanically
  checkable criteria such as
  verifier sub-checks, pytest, grep, diff, byte-equality, exit-code probes, or
  count comparisons. If the test exists, wire the Oracle to it. If the test
  must be added, Work Breakdown must include that step and the Oracle names the
  future test path.
- **`user_confirms`**: only for genuine human judgment: subjective quality,
  semantic accuracy, aesthetic match, taste, or corpus completeness a tool
  cannot scan. Mechanically-checkable criteria using `user_confirms` trigger an
  oracle_check WARN and Acceptance Criteria Reviewer scrutiny.
- `oracle_check` returns a visible `agentlaw verify-run` command. Run it in the
  project's terminal, then call `oracle_check` again to read the result.
- Test selectors must match the intended checks. A project command's nonzero
  exit is recorded without Agentlaw applying tool-specific reinterpretation.
- Long full-suite oracles should carry an execution plan for timeout handling:
  choose a justified job timeout, and treat timeout or cleanup failure as
  archive-blocking until the command is narrowed, fixed, or explicitly retried.
- Behavior claims must name the execution stratum they depend on when source,
  package, live runtime, external service, or persisted state paths can differ.
  Use the phrase `execution stratum` and one of: `source unit`, `package
  install`, `live installed CLI/MCP`, `external service`, or `persisted runtime
  state`. Source-only tests may satisfy only source-stratum claims unless the
  plan records an explicit `accepted-risk` row for the omitted stratum.
- Write the project command that is valid in the project environment. Agentlaw
  preserves that argv and environment; it does not inject source paths or
  substitute its own Python. Do not encode
  shell-specific `PYTHONPATH=src` prefixes in the plan body unless the criterion
  is explicitly testing shell invocation behavior.
- While editing implementation behavior, use focused checks for the changed
  surface. Run the full project pytest suite once at final readiness before
  commit, push, release, or any public-ready claim.

## Code Plan Fidelity

[Required only when `## Domain Coverage` marks `- substance: code`. Omit for
non-code plans.]

### Repository Discovery

- Inspected surfaces: [backticked files, symbols, commands, tests, fixtures,
  or contracts read before implementation]
- Existing tests/checks: [what currently verifies related behavior]
- Existing structure to follow: [local pattern or boundary to preserve]
- Structure-fit decision: [reuse, extend, or introduce new boundary; reason]

### Behavior Contract

- Current behavior:
- Target behavior:
- Explicitly unchanged behavior:
- Public contract impact:
- Failure cases:
- Negative/adversarial cases:

### Change Matrix

| Surface | Symbol/contract | Change intent | Public impact | Verification pairing |
| --- | --- | --- | --- | --- |
| `...` | `...` | `...` | yes/no | `...` |

### Test and Verification Contract

- Focused tests/checks:
- Regression or characterization tests:
- Broader verification:
- Manual-only checks, if any:
- Verification commands:

### Stop Conditions

- Stop if repository discovery contradicts the plan premise.
- Stop if public contract impact expands.
- Stop if no runnable oracle can be attached.
- Stop if required test/check pairing cannot be added.

## Scope

- Affected surfaces: [list every surface type the plan touches before
  execution; use a bullet list with backticked paths/globs per
  agentlaw_docs/law/REPOSITORY_ARTIFACT_RULES.md]
- Project-overview impact: [when the plan creates, removes, renames, or
  reshapes a source/application root, name the required
  `agentlaw_docs/references/project-overview.md` update; otherwise record the no-impact
  rationale]
- Stakeholders (segmented): [people/roles affected, by segment]
- Downstream consumers: [systems/artifacts/agents that depend on outputs]
- Indirect effects: [enumerated by category — related artifacts, stored
  state, alerts, environment, expectations]
- Temporal scope: one-time / ongoing / time-bounded
- Explicit non-goals: [out-of-scope list]

## Work Breakdown

- Work units: [major units of work, not a mandatory step-by-step script]
- Data affected: [DB tables, files, persistent records, schemas, financial
  data, source records, or N/A]
- User/interface surface: [UI, API, CLI, document, message, report, decision
  artifact, or N/A]
- Control/API boundary: [API route, command, workflow step, external
  interface, advisory boundary, or N/A]
- Permission/authority rule: [who may do what; N/A only with reason]
- Material dependencies: [dependencies that affect correctness, safety,
  reversibility, external effects, user gates, or verification]
- Failure cases: [normal failure, missing input, invalid input, unavailable
  dependency]
- Negative/adversarial cases: [misuse, abuse, boundary violation, hostile
  input, downside scenario]
- Test/check cases: [how each important behavior or claim will be verified]
- Agent optimization authority:
  - The agent may choose execution order, batching, parallelization, and local
    tactics within this plan contract.
  - The agent must not bypass acceptance criteria, verification, user gates,
    rollback boundaries, stop conditions, or governance obligations.

## Premise

- Facts: [each with verification artifact reference. Code-behavior claims must
  carry `Verification trace: <file:line | test:<test_name> |
  unverified-hypothesis>` per `PLANNING_AND_REVIEW_RULES.md` § Code-Fact Claim
  Verification Trace.]
- Sources: [each with specific identifier + freshness threshold]
- Assumptions: [each with verification path — what would convert it to
  verified]
- Prerequisites: [tree of conditions that must hold for the plan to apply]
- Prior art: [search trace; what already exists]
- Drift suspicion sites: [code-behavior suspicions also need the Verification
  trace token.]

[Conditional under Premise — fill only if relevant:]

- Source conflicts: [how conflicting sources are reconciled]
- Runtime / live state: [active probes for current state at execution time]
- Access-premise: [evidence of permissions/access being held]

## Confidence

- Confidence labels: [each non-trivial claim labeled verified / assumed /
  unknown / TBD, with basis]
- Undecided / TBD items: [decisions deferred, with unblocking conditions]

[Conditional under Confidence — fill if relevant:]

- Bounded knowledge: [scenarios that could invalidate the plan]
- Confidence-shift protocol: [escalation triggers when confidence falls]

## Risk

- Reversibility per step: [classification per step]
- User gates: [where, what asked]
- Rollback / recovery path: [per non-reversible step]
- Counterfactual fallbacks: [if X fails, then Y; cascade if Y fails]

## Execution Gates

- May execute without user approval: yes / no
- User approval required before:
  - file mutation:
  - dependency addition:
  - DB/schema migration:
  - public API/contract change:
  - deployment/release:
  - account/payment/message/external action:
  - financial/legal/medical consequential recommendation:
- Stop conditions:
  - classification mismatch found
  - new non-trivial trigger appears
  - required source/check unavailable
  - test/lint/build failure
  - plan-lint failure
  - review-lint failure
  - scope expands beyond approved plan
- Agent permission boundary:
  - read-only:
  - edit allowed:
  - command execution allowed:
  - network/external action allowed:

## Verification

- Pre-action checks: [what to verify before execution]
- Post-action checks: [what to verify after]
- Verification strategy: [test, oracle, or adversarial case matching risk]
- Required negative cases:
- Required regression checks:
- Required adversarial checks:
- Commands/tools to run:
- Evidence expected:
- agentlaw_tests/checks not run:
- Reason if not run:

## Review Coverage Matrix

[Required for plans authored after the Review Coverage Matrix rule lands and
for any earlier plan that explicitly opts in with
`Review Coverage Matrix required: yes`. The matrix records review completeness,
not implementation status. Unknowns must not be silently converted into
assumptions.]

- Review Coverage Matrix required: yes / no

| ID | Axis | Status | Evidence | Covered by |
| --- | --- | --- | --- | --- |
| rcm-1 | user intent | covered / not_applicable / needs_user_answer / accepted_risk / out_of_scope | <user quote, file:line, test:name, doc:path, MCP result, or rationale> | crit-N / out_of_scope |

The column names define meaning; their order may change. Escape a literal pipe
inside a cell as `\|`.

Status rules:

- `covered`: evidence is required, and `Covered by` must name at least one
  `crit-*`.
- `not_applicable`: rationale is required.
- `needs_user_answer`: review cannot finalize; ask the user and update the row.
- `accepted_risk`: user authorization or explicit risk rationale is required,
  and `Covered by` must name at least one `crit-*`.
- `out_of_scope`: rationale is required.

## Domain Exception Inventory

- Domain:
- Domain-specific rules:
- Common edge cases:
- Data consistency risks:
- Permission/authority risks:
- Operational risks:
- User/reputation/legal/financial risks:
- Cases intentionally out of scope:

## (Conditional) Code Architecture & Implementation Control

[Fill if the plan includes coding, debugging, refactoring, repository edits,
tests, dependencies, build, deployment, or generated code.]

- Responsibility location:
  - UI:
  - API/controller:
  - service/use-case:
  - domain/business rule:
  - repository/data access:
  - infrastructure/adapter:
- Architecture boundary:
  - Existing abstraction/interface to use:
  - New boundary crossing introduced:
  - Forbidden shortcut/patch to avoid:
  - Smallest architecture-preserving change:
- Contract/schema:
  - Request type/schema:
  - Response type/schema:
  - Error contract:
  - DB schema/migration:
  - Public API compatibility:
- Technical risk:
  - N+1/query risk:
  - transaction/consistency need:
  - index/unique constraint need:
  - cache/CORS/network risk:
  - time complexity/resource risk:
- Test plan:
  - unit:
  - integration/API:
  - DB/migration:
  - E2E:
  - regression:
  - permission/security:

## (Conditional) Security & Trust Boundaries

[Fill if marked in Domain Coverage.]

- Trust boundaries crossed:
- Sensitive identifier handling:
- Permission boundary (minimum required):
- Adversarial paths (system + user-side):
- Supply chain considerations:

## (Conditional) Data Stewardship

[Fill if marked.]

- Information integrity & migration:
- Privacy & retention:
- Information validity decay (drift):

## (Conditional) External Contract & Compatibility

[Fill if marked.]

- Public interface changes:
- Backward compatibility policy:
- Versioning strategy:
- Deprecation path: [if removing surface]

## (Conditional) Time & Sequencing

[Fill if marked.]

- Required ordering constraints: [only constraints that materially affect
  correctness, safety, reversibility, user gates, external side effects, or
  validation-before-mutation boundaries]
- Parallelization opportunities: [work that may be done independently]
- Parallel/concurrent execution risks:
- Deadlines / expiry:

## (Conditional) Cost, Resource & Performance

[Fill if marked.]

- Optimization target: [time / token / tool calls / compute / user attention /
  money]
- Expected cost drivers: [what makes this task expensive]
- Safe shortcuts allowed: [what may be skipped, batched, reused, or
  approximated without weakening correctness]
- Unsafe shortcuts forbidden: [what must not be skipped even for speed]

## (Conditional) Long-term Care

[Fill if marked.]

- Maintainability impact:
- Continued operability / observability:
- Documentation impact:

## Continuity

- Internal continuity (handoff state + decisions): [what next session needs]
- Decisions made:
- Rationale:
- ADR required: yes / no
- ADR title if required:
- Records to update:
- Future revisit condition:

[Conditional under Continuity — fill if relevant:]

- Persistent records to update: [external state to update]

## Governance

- Existing rules involved:
- Cross-rule consistency: [are referenced rules mutually consistent for
  this plan]

## Self-Application

- Rule-on-self status: [does the plan apply rules it introduces to itself]

[Conditional under Self-Application — fill if rule-introducing:]

- Bootstrap & transition: [transitional exemption declaration if invoked]
- Coherent examples: [rule examples follow the rule]
- Self-revisability protocol: [how the rule allows itself to be revised]

## Plan Review Evidence

[Filled in after persona review, not at draft time.]

- Review required: yes / no
- Review exemption reason: [if no]
- Plan contract hash: [filled by agentlaw_plan_review_session_finalize]
- Deep Review Selection: [non-trivial plans; selected/skipped reviewers and
  reasons, or N/A for trivial]
- Personas considered: [list per deck source]
- Persona deck sources: [paths]
- Finding dispositions and synthesis evidence: [finding ids,
  alternatives, changed sections, and targeted reviewer closure]
- Outcome Sufficiency evidence: [cited causal-chain verdict]

### Separate Persona Review Passes

[For trivial plans: only the Trigger Coverage Verifier pass appears here.]
[For non-trivial plans: one block per applied persona.]

#### [Persona name]

- Status: PASS / FAIL / N/A
- Severity: must-change / should-change / note
- Mandate quote: [verbatim text from the persona deck row, with inline
  Markdown preserved; required when the persona-review-loop tools were
  used]
- Inspected sections:
- Plan line citations: [list of `{ line, quote }` pairs the finding
  refers to; required when the persona-review-loop tools were used so
  the verifier can match line numbers byte-for-byte against the plan
  body]
- Evidence:
- Plan risk found:
- Required plan change:
- Verification or gate to add:
- Residual risk if accepted:

#### [Persona name with no concrete plan change]

- Status: PASS
- Severity: none
- Mandate quote: [verbatim text from the persona deck row, when the
  loop tools were used]
- Inspected sections:
- Evidence:
- Plan risk found: none
- Required plan change: none
- Verification or gate to add: none
- Residual risk if accepted:

---

- Revised after review: yes / no changes required
```

## Plan Amendment Authorizations

[Required for plans whose body is amended after `Plan reviewed: yes`.
Every reviewed-contract amendment is recorded here; verifier checks
plan-body amendment provenance against this section.]

```text
### Authorization YYYY-MM-DD-<letter> — <short title>

- Authorizer: user (<user-name>), session of YYYY-MM-DD.
- Authorization gate: explicit plan-body amendment approval. User
  instruction verbatim: "<quoted text>".
- Scope of authorized changes:
  1. <change 1>
  2. <change 2>
- Authorization rationale: <why the amendment is in scope>.
- Phasing: <single coherent commit / staged into multiple commits / etc.>
```

## Plan Oracle Evidence

[Populated during the `oracle_evaluation` phase by
`agentlaw_plan_review_oracle_check` and written during archive by
`agentlaw_plan_archive`. The verifier compares this section against
`plan_review_session.oracle_results` and fails on mismatch.]

```text
- Last oracle run: YYYY-MM-DDThh:mm:ss.fffZ
- Evidence provenance: mcp_oracle_results | user_manual_confirmation | interrupted_manual_recovery | legacy_archive_compatibility
- Evidence reason: <required for manual or interrupted recovery evidence>
- Mutmut module list: <comma-separated paths passed to --paths-to-mutate>
- Mutmut score: <surviving>/<total> = <ratio> (threshold: 0.40)
- Hypothesis max_examples: <integer>
- Per-criterion results:
  - crit-<id>: pass / fail / user_confirmed / pending
    - Oracle command: `<argv>`
    - Exit code: <integer>
    - Started at / Finished at: <iso timestamps>
    - User confirmation note: <when status=user_confirmed>
```

## Implementation Deviation Records

[Mutable post-review execution record. Use this section when implementation
must differ from the reviewed plan contract for a concrete reason. Do not
rewrite reviewed contract sections to make the plan look as if it always
said the implemented thing; record the reason, affected work step, and
verification here. Contract changes still require explicit amendment
authorization and re-review.]

```text
### Deviation YYYY-MM-DD-<letter> — <short title>

- Planned contract reference: <section / step / criterion>.
- Implemented behavior:
- Reason implementation differs:
- User authorization or governing authority:
- Verification:
- Residual risk:
```

## Notes

- Section names are stable. Verifiers and the persona-section-map depend on
  exact section headers; do not rename them.
- Sub-bullets within a section may be added or omitted as the plan needs.
- Older `Active Plan Preflight Fields` bullet-listed plans remain valid
  when they predate this template structure. New plans must follow the
  section structure above.
