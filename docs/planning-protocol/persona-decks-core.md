# Core Persona Deck

## Purpose

Universal review bench. Apply to every non-trivial plan-required task per
`docs/law/PLANNING_AND_REVIEW_RULES.md` § Required Planning Workflow. The
universal personas evaluate the plan's foundational dimensions independently
of which substance domains the plan touches.

For trivial plan-required work, only the Trigger Coverage Verifier persona
runs.

## Mandate Authoring Pattern

New persona mandates should follow the recommended structure in
`docs/planning-protocol/review-method.md` § Mandate Authoring Pattern
(sub-check decomposition + illustrative anchors + structured output
schema + recommend-not-require framing). Existing prose-only
mandates in this deck remain valid; retrofitting them is a separate
plan candidate.

## Sequential Application Contract

Each universal persona runs in **its own review turn**. Lens injection from
one persona must not cross-contaminate the next persona's review. This
preserves persona-specific output qualities that are sensitive to prompt
priming.

The **Trigger Coverage Verifier always runs first**. Its job is to validate
that the plan's classification (trivial / non-trivial) and Domain Coverage
self-mark match the plan's actual content. If it finds a missed trigger or
missing domain mark, the workflow restarts from plan revision.

After Trigger Coverage Verifier passes, the remaining universal personas
run in any order; the order does not matter as long as each persona's
review is recorded separately.

## Universal Personas (27)

The Trigger Coverage Verifier always runs first. The remaining personas are
grouped by domain for navigation; **within and across domain groups, order
of execution does not matter** (each persona runs in its own turn — see
§ Sequential Application Contract above).

## Execution Modes

Universal concerns are always checked, but not every universal persona is
always run as an isolated deep-review turn.

- **Always isolated**: Trigger Coverage Verifier; Form-vs-Substance Auditor;
  Cross-Section Coherence Reviewer.
- **Usually linted; isolated if flagged**: Intent Statement Reviewer,
  Underlying-Need Reviewer, Artifact Shape Reviewer, Acceptance Criteria
  Reviewer, Temporal Scope Reviewer, Confidence Label Reviewer, Undecided Item
  Reviewer, Internal Continuity Reviewer, Code-Fact Claim Verifier, and
  Rule-on-Self Reviewer when no rule-system artifact is modified.
- **Judgment reviewers; trigger-selected isolated**: Affected Surface Reviewer,
  Stakeholder Map Reviewer, Downstream Impact Reviewer, Indirect Effect
  Reviewer, Premise & Source Reviewer, Assumption-Path Reviewer, Prerequisite
  Chain Reviewer, Prior Art Reviewer, Risk Classification Reviewer, User Gate
  Reviewer, Rollback Path Reviewer, Fallback Plan Reviewer, Verification
  Reviewer, and Governance & Compliance Reviewer.

Any usually-linted persona is promoted to isolated review when plan-lint flags
its mapped section, the plan changes governance, introduces irreversible action,
touches external/public artifacts, supports a high-impact decision, or Trigger
Coverage Verifier explicitly requests it.

## Default Response Shapes

Unless a persona entry overrides these fields, every core persona uses these
response shapes.

**Problem-Found Response shape:** Evidence (≥3 sentences citing plan-body lines that demonstrate the problem). Plan risk (≥3 sentences naming the concrete consequence if left). Required plan change (≥3 sentences specifying the amendment). Verification (≥3 sentences naming how a re-review would confirm the fix).

**No-Problem Response shape:** Inspected sections (the plan-body anchors checked). Evidence (≥3 sentences explaining why this persona's coverage items are satisfied — must include the PASS token). Residual risk (≥1 sentence on caveats future iterations should watch for).

### Classification Gatekeeper (runs first)

#### Trigger Coverage Verifier

**Mandate:** Read the plan and validate that (a) cited risk triggers from `task-classification.md` § Non-Trivial Triggers actually match plan content, (b) the trivial / non-trivial classification is correct, and (c) for non-trivial plans, the Domain Coverage self-mark covers all conditional domains the plan content touches. Flag missing or wrong markings; recommend reclassification.

**Coverage:**

- Cited risk triggers from `task-classification.md` § Non-Trivial Triggers are matched against actual plan body content.
- The plan's trivial / non-trivial classification is correct given the matched triggers.
- For non-trivial plans, the Domain Coverage self-mark covers all conditional domains the plan content touches.
- Missing or wrong markings are flagged with a concrete reclassification recommendation.

### Domain 1 — Intent (4)

#### Intent Statement Reviewer

**Mandate:** Verify the plan opens with a restatement of the user's ask in the author's own words, in a form the user can confirm. Reject vague restatements.

**Coverage:**

- The plan opens with a restatement of the user's ask in the author's own words.
- The restatement is in a form the user can confirm (concrete, specific, unambiguous).
- Vague or generic restatements are flagged for rejection.
- The restatement faithfully captures the user's literal request without re-framing.

#### Underlying-Need Reviewer

**Mandate:** Verify the plan distinguishes the literal ask from the user's underlying need. Reject platitudes ("improve productivity") in the underlying-need field; require a specific outcome.

**Coverage:**

- The plan distinguishes the literal ask from the user's underlying need.
- The underlying-need field names a specific outcome, not a platitude.
- Generic phrasings like "improve productivity" or "make better" are flagged for rejection.
- The underlying need is traceable to a concrete future state the user can recognize.

#### Artifact Shape Reviewer

**Mandate:** Verify the plan names the deliverable's specific form (format, length, structure). Reject vague shape descriptions.

**Coverage:**

- The plan names the deliverable's specific format (file type, schema, medium).
- The plan names the deliverable's expected length or size envelope.
- The plan names the deliverable's structural composition (sections, fields, components).
- Vague shape descriptions ("a document", "some code") are flagged for rejection.

#### Acceptance Criteria Reviewer

**Mandate:** Verify the plan names concrete criteria the user (or a reasonable tool) can check. Reject abstract phrasings like "works well" or "no problems". Verify Oracle marker selection per `plan-template.md` § Oracle Marker Selection: mechanically-checkable criteria use runnable Oracle markers (test-exists branch wires existing tests; test-absent branch requires plan Work Breakdown to add the test); `user_confirms` is reserved for genuinely subjective judgment. Reject `user_confirms` on criteria that are mechanically checkable.

**Coverage:**

- Each acceptance criterion is concrete and checkable by the user or a reasonable tool.
- Abstract phrasings like "works well" or "no problems" are flagged for rejection.
- Mechanically-checkable criteria carry runnable Oracle markers per `plan-template.md` § Oracle Marker Selection.
- For test-absent branches, the plan Work Breakdown includes adding the corresponding test.
- `user_confirms` is reserved for genuinely subjective judgment and is rejected on mechanically-checkable criteria.

### Domain 2 — Scope (5)

#### Affected Surface Reviewer

**Mandate:** Verify the plan enumerates every type of surface it touches (work artifacts, related external systems and people, durable records, environmental and contextual assumptions). Reject single-category lists.

**Coverage:**

- The plan enumerates work artifacts it touches.
- The plan enumerates related external systems and people it touches.
- The plan enumerates durable records it touches.
- The plan enumerates environmental and contextual assumptions it depends on.
- Single-category lists are flagged for rejection.

#### Stakeholder Map Reviewer

**Mandate:** Verify stakeholders are segmented by role and interest in a way meaningful to the plan's context. Reject single generic labels like "users".

**Coverage:**

- Stakeholders are segmented by distinct roles relevant to the plan's context.
- Each stakeholder segment is annotated with its interest in the plan's outcome.
- Single generic labels like "users" or "everyone" are flagged for rejection.
- The segmentation is meaningful for downstream-impact and notification decisions.

#### Downstream Impact Reviewer

**Mandate:** Verify the plan presents evidence (search/query results, dependency listings) of downstream consumers. Reject claims without evidence ("none affected").

**Coverage:**

- The plan presents concrete evidence (search results, dependency listings) of downstream consumers.
- Claims like "none affected" without supporting evidence are flagged for rejection.
- The evidence is recent and methodologically credible (named queries, named tools).
- The plan's downstream-consumer enumeration is consistent with affected-surface enumeration.

#### Indirect Effect Reviewer

**Mandate:** Verify the plan enumerates second-order effects across multiple categories (related artifact updates, stored state, alerts, environmental settings, user expectations). Reject single-category enumeration.

**Coverage:**

- The plan enumerates related artifact updates as second-order effects.
- The plan enumerates stored-state mutations as second-order effects.
- The plan enumerates alerts and environmental-setting changes.
- The plan enumerates shifts in user expectations triggered by the change.
- Single-category enumeration is flagged for rejection.

#### Temporal Scope Reviewer

**Mandate:** Verify the plan declares its temporal scope (one-time, ongoing, time-bounded) and the operational meaning of that choice.

**Coverage:**

- The plan declares its temporal scope (one-time, ongoing, or time-bounded).
- The operational meaning of the chosen scope is named (what continues, what stops, what recurs).
- Time-bounded scopes name the bounds explicitly.
- Ongoing scopes name a review or renewal cadence.

### Domain 3 — Premise (universal subset, 4)

#### Premise & Source Reviewer

**Mandate:** For each factual claim, verify the plan cites (a) a verification artifact reference (observation, source ID, system response, measurement), (b) a specific source identifier, and (c) a freshness threshold (when the source becomes stale). All three together. Reject method-name-only references.

**Coverage:**

- Each factual claim carries a verification artifact reference (observation, source ID, system response, measurement).
- Each factual claim carries a specific source identifier.
- Each factual claim carries a freshness threshold naming when the source becomes stale.
- All three elements appear together for each claim.
- Method-name-only references (without artifact or identifier) are flagged for rejection.

#### Assumption-Path Reviewer

**Mandate:** For each assumption, verify it is labeled (assumed / unknown / TBD), the basis is cited, and a path to verification is named (what evidence/event would convert the assumption into a verified fact).

**Coverage:**

- Each assumption carries an explicit label (assumed / unknown / TBD).
- Each assumption cites the basis for the label.
- Each assumption names a path to verification (what evidence or event converts it to verified fact).
- Unlabeled or basis-less assumptions are flagged for rejection.

#### Prerequisite Chain Reviewer

**Mandate:** Verify the plan presents an explicit prerequisite tree (or graph), with each node verified.

**Coverage:**

- The plan presents an explicit prerequisite tree or graph.
- Each prerequisite node carries a verification status.
- Unverified prerequisite nodes are flagged.
- The prerequisite graph is acyclic and traceable to plan steps.

#### Prior Art Reviewer

**Mandate:** Verify the plan presents a search trace (queries used, locations checked) for prior solutions or reusable artifacts. Reject "checked, none" without trace.

**Coverage:**

- The plan presents a search trace (queries used, locations checked) for prior solutions.
- The plan presents a search trace for reusable artifacts.
- "Checked, none" without an accompanying trace is flagged for rejection.
- The search trace is reproducible by another reviewer.

### Domain 4 — Risk (4)

#### Risk Classification Reviewer

**Mandate:** Verify each step is classified by reversibility (reversible / hard-to-reverse / irreversible) and blast radius is named.

**Coverage:**

- Each step is classified by reversibility (reversible / hard-to-reverse / irreversible).
- Each step's blast radius is named.
- Unclassified or partially classified steps are flagged.
- The reversibility classification is consistent with downstream rollback-path expectations.

#### User Gate Reviewer

**Mandate:** Verify user gates are explicit before each non-reversible step; specify when and what the user is being asked to approve. For irreversible steps, require explicit irreversibility acknowledgment.

**Coverage:**

- Each non-reversible step has an explicit user gate before it.
- Each gate specifies when the user is being asked to approve.
- Each gate specifies what the user is being asked to approve.
- Irreversible steps require an explicit irreversibility acknowledgment from the user.

#### Rollback Path Reviewer

**Mandate:** Verify each non-reversible step names a rollback or recovery path.

**Coverage:**

- Each non-reversible step names a rollback or recovery path.
- The rollback path is concrete (specific commands, artifacts, or actions).
- The rollback path is feasible given the step's actual side effects.
- Steps without a rollback path are flagged for rejection or for explicit irreversibility acknowledgment.

#### Fallback Plan Reviewer

**Mandate:** Verify each critical assumption has a fallback for failure. Require cascading fallback (what happens if the first fallback also fails).

**Coverage:**

- Each critical assumption has a fallback for failure.
- A cascading fallback is specified (what happens if the first fallback also fails).
- Fallback paths are concrete and actionable.
- Critical assumptions without any fallback are flagged for rejection.

### Domain 5 — Verification (1)

#### Verification Reviewer

**Mandate:** Verify the plan names pre-action and post-action checks at execution boundaries, and a verification strategy (tests, oracles, controlled checks) matched to risk level, including negative or adversarial cases for new mechanisms. Reject normal-case-only strategies.

**Coverage:**

- The plan names pre-action checks at execution boundaries.
- The plan names post-action checks at execution boundaries.
- The verification strategy (tests, oracles, controlled checks) is matched to risk level.
- The strategy includes negative or adversarial cases for new mechanisms.
- Normal-case-only verification strategies are flagged for rejection.

### Domain 12 — Continuity (universal subset, 1)

#### Internal Continuity Reviewer

**Mandate:** Verify the plan provides handoff state (what the next session or worker needs to resume) and a decision log (decisions made plus their rationale).

**Coverage:**

- The plan provides explicit handoff state describing what the next session or worker needs to resume.
- The plan provides a decision log listing decisions made.
- Each decision log entry includes a rationale.
- Handoff state and decision log together are sufficient to reconstruct the plan's reasoning offline.

### Domain 13 — Governance (1)

#### Governance & Compliance Reviewer

**Mandate:** Verify the plan checks consistency among the rules, policies, and agreements it cites or applies. Where multiple rules apply, require explicit cross-rule consistency check and conflict-resolution decision.

**Coverage:**

- The plan checks consistency among the rules, policies, and agreements it cites or applies.
- Where multiple rules apply, the plan performs an explicit cross-rule consistency check.
- The plan names a conflict-resolution decision when rules conflict.
- Silent rule selection (picking one rule without acknowledging another) is flagged for rejection.

### Domain 15 — Confidence (universal subset, 2)

#### Confidence Label Reviewer

**Mandate:** Verify each non-trivial claim is labeled with confidence (verified / assumed / unknown / TBD), the basis for the label is cited, and (for non-verified labels) a verification path is named.

**Coverage:**

- Each non-trivial claim is labeled with confidence (verified / assumed / unknown / TBD).
- The basis for each label is cited.
- For non-verified labels, a verification path is named.
- Unlabeled claims or labels without cited basis are flagged for rejection.

#### Undecided Item Reviewer

**Mandate:** Verify the plan lists undecided / TBD items explicitly, names unblocking conditions for each, and assigns a decision owner.

**Coverage:**

- The plan lists undecided / TBD items explicitly.
- Each undecided item names its unblocking condition.
- Each undecided item is assigned a decision owner.
- Implicit or hidden TBDs (deferred without listing) are flagged for rejection.

### Domain 16 — Self-Application (universal subset, 1)

#### Rule-on-Self Reviewer

**Mandate:** When the plan introduces or modifies a rule, verify the plan's body itself follows the rule (or invokes explicit bootstrap exemption per `docs/law/PLANNING_AND_REVIEW_RULES.md` § Bootstrap Transitional Exemption). For plans that do not modify rules, this persona PASSes with reason "plan does not modify rules".

**Coverage:**

- When the plan introduces or modifies a rule, the plan's body is checked against that rule.
- Bootstrap transitional exemption is invoked explicitly when self-application is impossible.
- Plans not modifying rules PASS with reason "plan does not modify rules".
- Silent self-violation (the plan body breaking the rule it introduces) is flagged for rejection.

### Domain 17 — Substance Coherence (universal subset, 3)

Three personas that catch form-vs-substance gaps surviving form-correct
review.

#### Form-vs-Substance Auditor

**Mandate:** Audit each plan-body section for substance against its form. Reject sections that satisfy structural template requirements while inline prose admits the substance is incomplete. Flag hedging phrases ("checked, none", "pending", "partial", "TBD without verification path", "deferred without unblocking condition") paired with form-correct presence in a load-bearing field.

**Coverage:**

- Each plan-body section is audited for substance against its form.
- Sections satisfying structural template requirements while inline prose admits incompleteness are flagged.
- Hedging phrases ("checked, none", "pending", "partial") in load-bearing fields are flagged.
- "TBD without verification path" and "deferred without unblocking condition" patterns are flagged.
- Form-correct presence paired with substance-empty content is treated as a false-readiness violation.

#### Cross-Section Coherence Reviewer

**Mandate:** Identify pairs (or triples) of plan-body sections whose claims constrain each other and verify the constraints are satisfied. Examples: Verification section's stated TDD strategy ↔ Work Breakdown step body verbs; Intent's artifact shape ↔ Scope's affected surfaces enumeration; Acceptance Criteria's per-`crit-*` count ↔ Step 10 list count; Risk Rollback paths ↔ components Steps actually create. Reject local-coherent plans that fail macro coherence.

**Coverage:**

- Pairs or triples of plan-body sections whose claims constrain each other are identified.
- Verification section's TDD strategy is consistent with Work Breakdown step body verbs.
- Intent's artifact shape is consistent with Scope's affected-surfaces enumeration.
- Acceptance Criteria's per-`crit-*` count matches Step 10 list count.
- Risk Rollback paths are consistent with the components Steps actually create.
- Local-coherent plans that fail macro coherence are flagged for rejection.

#### Code-Fact Claim Verifier

**Mandate:** Scan plan-body sections that assert specific code behavior (drift suspicion sites, acceptance-criterion bodies that name code paths or symbols, Premise facts about implementation, Confidence Register entries citing code) and verify each claim carries a `Verification trace: <file:line \| test:<test_name> \| unverified-hypothesis>` token. Missing-trace claims are must-change findings. The persona enforces `docs/law/PLANNING_AND_REVIEW_RULES.md` § Code-Fact Claim Verification Trace at plan-review time so unverified hypotheses about code do not survive into the consolidated plan body.

**Coverage:**

- Plan-body sections asserting specific code behavior are scanned (drift suspicion sites, acceptance-criterion bodies, Premise facts, Confidence Register entries).
- Each code-fact claim carries a `Verification trace: <file:line | test:<test_name> | unverified-hypothesis>` token.
- Missing-trace claims are recorded as must-change findings.
- The persona enforces `docs/law/PLANNING_AND_REVIEW_RULES.md` § Code-Fact Claim Verification Trace at plan-review time.
- Unverified hypotheses about code are prevented from surviving into the consolidated plan body.

## Minimum Use

- **Trivial plan-required work**: only Trigger Coverage Verifier runs. The
  remaining 26 universal personas are skipped.
- **Non-trivial plan-required work**: Trigger Coverage Verifier always runs
  isolated first. The remaining universal personas are checked by plan-lint,
  review-lint, and Deep Review Selection; only selected deep-review personas
  run as isolated turns. Substance-triggered personas run when selected by
  Domain Coverage, lint findings, Trigger Coverage Verifier, or Deep Review
  Selector per `persona-decks-specialized.md`.

Never add a persona that cannot produce a concrete plan change. A persona
that always returns "no findings" for a given class of plan should be
marked as "PASS — no applicable concern" rather than skipped silently.

## Section Mapping

Each universal persona's primary and optional plan-template sections are
recorded in `docs/planning-protocol/persona-section-map.md`. When a plan
revision affects only specific sections, only the personas mapped to those
sections need re-invocation.

## Cross-References

- Law authority: `docs/law/PLANNING_AND_REVIEW_RULES.md`
- Triggers and classification: `docs/planning-protocol/task-classification.md`
- Workflow and severity: `docs/planning-protocol/review-method.md`
- Substance-triggered and field-specific personas:
  `docs/planning-protocol/persona-decks-specialized.md`
- Section mapping: `docs/planning-protocol/persona-section-map.md`
- Plan template: `docs/planning-protocol/plan-template.md`
