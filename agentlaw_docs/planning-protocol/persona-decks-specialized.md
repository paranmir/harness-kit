# Specialized Persona Decks

## Mandate Authoring Pattern

New persona mandates should follow the recommended structure in
`agentlaw_docs/planning-protocol/review-method.md` § Mandate Authoring Pattern
(sub-check decomposition + illustrative anchors + structured output
schema + recommend-not-require framing). Existing prose-only
mandates in this deck remain valid; retrofitting them is a separate
plan candidate. The Domain 5 test-adequacy
subset and Domain 6 Security output schema already exemplify the
pattern.

## Purpose

Specialized personas review the plan from substance-specific lenses. They
are invoked **only when the relevant substance is present in the plan**:

- **Substance-triggered personas**: invoked when the plan's Domain Coverage
  self-mark (in `plan-template.md`) marks the corresponding domain as
  applicable. The Trigger Coverage Verifier (universal) validates the
  self-mark; missing marks trigger reclassification.
- **Sensitive-domain field personas**: invoked when the plan's task class is
  in the sensitive-domain family (writing/translation/personal-advice/
  legal-financial-medical/etc.). Field-specific by design; do not absorb
  into structural domains.

Selected specialized personas run **sequentially in their own review turns** per
`review-method.md` § Workflow. If more than six specialized personas are
selected, the Integrator must decide whether to split the task. More reviewers
should not be used to compensate for an over-broad task.

Every selected persona receives and may inspect the **whole current plan**.
Primary sections in `persona-section-map.md` define the persona's required
judgment and targeted re-review trigger; optional sections provide context and
do not independently trigger another pass.

## Default Response Shapes

Unless a persona entry overrides these fields, every specialized persona uses these response shapes.

**Problem-Found Response shape:** Evidence (cite the plan-body lines that demonstrate the problem). Plan risk (name the concrete consequence if left). Required plan change (specify the amendment). Verification (name how re-review confirms the fix). Each field must be substantive; no fixed sentence count applies.

**No-Problem Response shape:** Inspected sections (the plan-body anchors checked). Evidence (explain why this persona's coverage items are satisfied and include the PASS token). Residual risk (name any caveat worth retaining). Keep it concise when no issue exists.

## Substance-Triggered Personas (27)

### Domain 3 (Premise) — substance subset

#### Source Conflict Reviewer

**Trigger:** plan cites multiple sources that disagree on a load-bearing claim

**Mandate:** Require an explicit conflict-resolution rule (most recent / most authoritative / user-decision-gate). Reject plans that pick a side silently.

**Coverage:**

- Multiple cited sources disagreeing on a load-bearing claim are identified.
- An explicit conflict-resolution rule is required (most recent / most authoritative / user-decision-gate).
- Plans that pick a side silently are flagged for rejection.
- The chosen resolution rule is recorded inline next to the conflicting claim.


#### Runtime State Reviewer

**Trigger:** plan depends on the actual state of a system, environment, or context being a specific way at execution time

**Mandate:** Require an active probe at execution time to confirm the assumed state. Reject static claims about runtime state without verification step.

**Coverage:**

- Plan claims about runtime state (system, environment, context) are identified.
- An active probe at execution time is required for each runtime-state claim.
- Static claims about runtime state without a verification step are flagged for rejection.
- The probe's expected outcome and the failure-handling path are named.


#### Access-Premise Reviewer

**Trigger:** plan claims to have a permission or access right that must hold for execution to succeed

**Mandate:** Require evidence that the access is actually held (test invocation result, authentication verification, written grant). Reject claim-only assertions.

**Coverage:**

- Each access or permission premise the plan depends on is identified.
- Evidence that the access is actually held is required (test invocation result, authentication verification, written grant).
- Claim-only assertions ("I have access") without supporting evidence are flagged for rejection.
- The evidence type is appropriate to the access kind (machine access uses probes; human grants use written records).


### Design — substance subset

#### Design Alternatives Reviewer

**Trigger:** the plan makes a material design choice for which at least two
viable approaches could materially differ in architecture fit, data or control
flow, state handling, external contracts, dependencies, verification,
maintenance cost, or reversibility. Do not trigger for a purely mechanical or
cosmetic change with no meaningful alternative.

**Mandate:** Verify the plan compares the simplest sufficient design with at
least one viable alternative, selects an approach using explicit current
constraints, and records tradeoffs and reconsideration triggers. Recommend
design boundaries and decision criteria, not a mandatory implementation shape.
Reject both unexamined first-choice designs and speculative alternatives that
do not answer a current need.

**Coverage:**

- Read the whole current plan and identify the load-bearing design decision,
  current constraints, and intended outcome.
- Compare at least two viable approaches across architecture fit, complexity,
  state/concurrency where relevant, compatibility, testability, change cost,
  reversibility, and operational risk.
- Explain why the selected design is the simplest approach that satisfies the
  current contract rather than merely the smallest diff.
- Record discarded alternatives, material tradeoffs, and observable conditions
  that would justify reconsidering the decision.
- Flag premature abstraction, one-off special cases, and implementation
  prescriptions unsupported by the plan's present requirements.

**Pass anchor:** the selected approach wins against a viable alternative under
named current constraints, preserves an extension path, and states when the
decision should be revisited.

**Fail anchor:** the plan names one approach as obvious, or invokes a pattern or
abstraction without comparing current complexity, compatibility, testability,
and maintenance consequences.

**Response shape:** Status; Severity; Inspected primary sections; Material
decision; Current constraints; Alternatives compared; Selected design and
tradeoffs; Reconsideration triggers; Evidence; Required plan change;
Verification or gate to add; Residual risk.


#### Code Design & Maintainability Reviewer

**Trigger:** the plan changes executable code, runtime behavior, schema-backed
state, scripts, tests that define behavior, or a public code contract. A
documentation-only plan does not trigger this persona merely because it
mentions code.

**Mandate:** Verify the proposed code change fits the repository's current
architecture, keeps responsibilities and dependencies explicit, preserves
state, concurrency, error, and compatibility contracts, and remains testable
and economical to modify. Apply SOLID principles, design patterns, and
clean-code guidance only where they solve demonstrated current complexity; do
not require speculative abstractions, class hierarchies, broad rewrites, or a
specific implementation when multiple designs satisfy the contract.

**Coverage:**

- Read the whole current plan plus repository discovery and Code Plan Fidelity
  evidence before judging the proposed code boundary.
- Check responsibility placement, dependency direction, cohesion, coupling,
  naming/contract clarity, and reuse of established local architecture.
- Trace state ownership, concurrency behavior, idempotency, error propagation,
  rollback/recovery, and backward compatibility where each is applicable.
- Require a design that is directly testable through the affected execution
  stratum and does not hide behavior behind unobservable coupling.
- Compare the selected design with the simplest viable alternative and flag
  both one-off patches and abstractions whose maintenance cost exceeds current
  need.

**Pass anchor:** the plan identifies an architecture-fitting, testable change
boundary with explicit state/error/compatibility behavior and no avoidable
technical debt.

**Fail anchor:** the plan proposes a local branch, helper, layer, or pattern
without showing ownership fit, contract preservation, tests, or why existing
architecture cannot carry the behavior.

**Response shape:** Status; Severity; Inspected primary sections; Architecture
fit; Responsibility/dependency assessment; State/concurrency/error/
compatibility contracts; Testability; Alternatives and tradeoffs; Evidence;
Required plan change; Verification or gate to add; Residual risk.


### Domain 5 (Verification) — substance subset (code substance)

These four personas decompose test-rigor adequacy along the four
dimensions established by the test-adequacy literature (Hong Zhu et al.
1997; ISO/IEC/IEEE 29119-4): specification-based, risk-based,
defect-based + coverage-based, and property-based. They differ in
layer from the universal **Verification Reviewer** (which checks the
plan's verification *strategy* in prose) — these are the
mechanical-specification layer that runs only when the plan's
`## Domain Coverage` section marks `substance: code`. Multi-persona
decomposition is grounded by measurable detection improvement: PBT+EBT
combined gives 12.5pp detection over either alone (arxiv 2510.25297)
and matches the 4-5 specialized-agent count common in production
multi-agent review systems (Qodo, Galileo Luna-2, From-Law-to-Gherkin
4-dim rubric).

These personas are not a global project test-file requirement. They apply
to code-modification-related review only: plans that touch executable
runtime behavior, tests, scripts, CLI/MCP code paths, schema/runtime
state behavior, or that explicitly mark `substance: code`. Documentation-only,
law-only, memory-only, and reference-only plans remain outside this
persona family unless they govern a code-touching implementation path.

#### Test Specification Reviewer

**Trigger:** plan declares acceptance criteria that will be verified by tests, and the plan touches code (substance: code marker active)

**Mandate:** Verify each `crit-*` carries (a) at least one test mapping, (b) categorization across happy / edge / adversarial test classes, (c) cross-cutting concern tags (security, concurrency, error path, boundary, regression) where applicable, and (d) execution-stratum mapping when source, package, runtime, external service, or persisted-state paths differ. Reject criteria with prose-only verification when a runnable test is feasible.

For Code Plan Fidelity, also verify that the plan names repository discovery
evidence, behavior contract, change matrix, focused verification, and
test/check pairing before execution. Missing profile substance is a plan
change requirement.

**Coverage:**

- Each `crit-*` carries at least one test mapping.
- Each `crit-*` is categorized across happy / edge / adversarial test classes.
- Cross-cutting concern tags (security, concurrency, error path, boundary, regression) are applied where applicable.
- Execution-stratum mapping is present when source/package/runtime/service/persisted-state paths materially differ.
- Criteria with prose-only verification where a runnable test is feasible are flagged for rejection.
- Code Plan Fidelity fields are present and linked to concrete tests/checks.


#### Risk-Weighted Test Reviewer

**Trigger:** plan touches code with non-uniform risk distribution (some criteria are reversible, others irreversible; some are high-blast-radius, others local)

**Mandate:** Verify test depth scales with criterion risk: high-risk criteria require multiple test cases per category and explicit adversarial coverage; low-risk criteria may share a single happy-path test. Reject uniform test depth on plans with mixed-risk criteria.

**Coverage:**

- High-risk criteria require multiple test cases per category.
- High-risk criteria require explicit adversarial coverage.
- Low-risk criteria may share a single happy-path test.
- Uniform test depth on plans with mixed-risk criteria is flagged for rejection.
- The mapping between criterion risk and test depth is recorded explicitly.


#### Defect Detection Adequacy Reviewer

**Trigger:** plan touches code and declares a mutation-score or coverage threshold (or should, given the substance: code marker)

**Mandate:** Verify the plan body names (a) the mutation-score threshold and the explicit module list passed to the mutation tool, (b) assertion-effectiveness rationale (do the tests have meaningful asserts, or just exercise paths?), (c) line / branch coverage targets when applicable. Reject plans declaring `substance: code` without a recorded threshold or with a no-op assertion pattern.

**Coverage:**

- The plan names the mutation-score threshold.
- The plan names the explicit module list passed to the mutation tool.
- The plan provides assertion-effectiveness rationale (tests have meaningful asserts, not just path exercise).
- The plan names line / branch coverage targets where applicable.
- Plans declaring `substance: code` without a recorded threshold or with a no-op assertion pattern are flagged for rejection.


#### Invariant Specification Reviewer

**Trigger:** plan touches code where invariants over a domain (e.g., idempotency, monotonicity, round-trip equality, commutativity) are checkable via property-based testing

**Mandate:** Verify each `crit-*` declares either at least one Hypothesis `@given` invariant or an explicit `pbt: not-applicable` marker accompanied by a structural reason (the criterion is pure-text content matching, or its domain is human judgment via `user_confirms`). Reject cosmetic N/A — the marker requires the substantive reason inline.

**Coverage:**

- Each `crit-*` declares at least one Hypothesis `@given` invariant, or an explicit `pbt: not-applicable` marker.
- Each `pbt: not-applicable` marker is accompanied by a structural reason (pure-text content matching, or domain is human judgment via `user_confirms`).
- Cosmetic N/A markers without inline substantive reason are flagged for rejection.
- The structural reason classifies the criterion type concretely (text-only vs. judgment vs. invariant-bearing).


### Domain 6 (Security & Trust Boundaries) — 5

#### Trust Boundary Reviewer

**Trigger:** plan modifies how data or control flows between regions of different trust levels

**Mandate:** Identify each crossing point and require validation, filtering, and audit-trail mechanisms at the boundary.

**Coverage:**

- Each crossing point between regions of different trust levels is identified.
- Validation mechanisms at the boundary are named for each crossing point.
- Filtering mechanisms at the boundary are named for each crossing point.
- Audit-trail mechanisms at the boundary are named for each crossing point.
- Crossings without validation, filtering, or audit are flagged for rejection.


#### Sensitive Information Handling Reviewer

**Trigger:** plan handles sensitive identifiers (credentials, secrets, identity tokens, personal data, privileged communications, private content)

**Mandate:** Trace the lifecycle of each sensitive value; flag plaintext storage, inappropriate exposure, and over-broad delegation.

**Coverage:**

- The lifecycle of each sensitive value is traced (origin, transit, storage, deletion).
- Plaintext storage of sensitive values is flagged.
- Inappropriate exposure (logs, transcripts, error messages) is flagged.
- Over-broad delegation of sensitive values to subprocesses or third parties is flagged.
- Each sensitive value carries an explicit handling policy.


#### Permission Boundary Reviewer

**Trigger:** plan grants, scopes, or revokes permissions or authorization

**Mandate:** Verify the granted authority is the minimum required. Reject over-broad, perpetual, or unscoped grants.

**Coverage:**

- The granted authority is verified as the minimum required for the plan's purpose.
- Over-broad grants are flagged for rejection.
- Perpetual grants without expiry are flagged for rejection.
- Unscoped grants (no resource or action limits) are flagged for rejection.
- Each grant names a revocation path.


#### Adversarial Path Reviewer

**Trigger:** plan introduces a surface that could be exercised by malicious or accidental misuse

**Mandate:** Enumerate adversarial scenarios (system-side threat model + user-side abuse cases). Require concrete plan changes for each non-trivial scenario.

**Coverage:**

- System-side threat-model scenarios are enumerated for the new surface.
- User-side abuse cases are enumerated for the new surface.
- Each non-trivial scenario carries a concrete plan change (mitigation, detection, response).
- Trivial scenarios are explicitly classified as accepted risk with reason.
- Scenarios without mitigation or accepted-risk classification are flagged.


#### Supply Chain Reviewer

**Trigger:** plan introduces or upgrades external dependencies

**Mandate:** Verify provenance, integrity, and trust assumptions of each new external dependency.

**Coverage:**

- Provenance of each new or upgraded external dependency is verified (origin, maintainer, distribution channel).
- Integrity of each dependency is verified (hash, signature, or pinned version).
- Trust assumptions of each dependency are stated (what privileges it gains, what failure modes it introduces).
- Dependencies without explicit provenance, integrity, or trust statement are flagged for rejection.


Security-related findings must include:

- Vulnerability:
- Attack condition:
- Reproduction path:
- Affected asset:
- Impact:
- Fix:
- False-positive possibility:
- Required test/check:

### Domain 7 (Data Stewardship) — 2

#### Information Integrity Reviewer

**Trigger:** plan transforms, moves, or transitions persistent information

**Mandate:** Verify meaning, relationships, and completeness are preserved across the transformation. Detect and recover from data drift over time (information that loses validity as external context evolves).

**Coverage:**

- Meaning of persistent information is preserved across the transformation.
- Relationships between data elements are preserved across the transformation.
- Completeness of the dataset is preserved across the transformation.
- Data drift over time (information losing validity as external context evolves) is detected.
- A recovery path for detected drift is named.


#### Privacy & Retention Reviewer

**Trigger:** plan handles personally identifiable or sensitive information

**Mandate:** Apply minimum-collection, purpose-limited-use, and retention-bounded policies. Flag violations.

**Coverage:**

- The minimum-collection policy is applied: only information needed for the plan's purpose is collected.
- The purpose-limited-use policy is applied: collected information is used only for the stated purpose.
- The retention-bounded policy is applied: information has a named retention period and deletion path.
- Policy violations (over-collection, repurposing, indefinite retention) are flagged.


### Domain 8 (External Contract & Compatibility) — 1

#### Interface Contract Reviewer

**Trigger:** plan modifies an externally exposed surface (API, CLI, file format, protocol, or content meant for use outside the producing context)

**Mandate:** Verify backward compatibility for active consumers; require versioning strategy and deprecation/migration path for removed surfaces; for distributed content (used or copied in other codebases / projects / teams), verify the content is sufficiently abstracted from local specifics (vocabulary, paths, examples, environmental assumptions) to function across the intended consumer range.

**Coverage:**

- Backward compatibility for active consumers is verified.
- A versioning strategy is required for the modified surface.
- A deprecation or migration path is required for removed surfaces.
- For distributed content (copied across codebases, projects, teams), abstraction from local specifics (vocabulary, paths, examples, environmental assumptions) is verified.
- Surfaces lacking versioning, migration, or sufficient abstraction are flagged for rejection.


### Domain 9 (Time & Sequencing) — 2

#### Sequencing & Parallel Execution Reviewer

**Trigger:** plan has multiple steps with order dependency or possible parallel execution

**Mandate:** Require only material ordering constraints, not a full procedural script. Verify validation-before-mutation boundaries, user gates, irreversible-action ordering, and concurrency conflicts (resource contention, ordering-assumption violations, duplicate effects). Flag unnecessary fixed sequencing when the agent could safely optimize order, batching, or parallelization.

**Coverage:**

- Material ordering constraints between dependent work units are enumerated.
- Validation-before-mutation boundaries, user gates, and irreversible-action ordering are preserved.
- Concurrency conflicts (resource contention) are enumerated.
- Ordering-assumption violations under parallel execution are enumerated.
- Duplicate-effect risks under parallel execution are enumerated.
- Unnecessary fixed sequencing is flagged when it does not improve correctness, safety, verification, or user value.


#### Deadline Reviewer

**Trigger:** plan has time-bound elements (deadline, expiry, freshness window)

**Mandate:** Verify the time constraint is named, the consequence of missing it is stated, and a path to handle missed deadlines exists.

**Coverage:**

- Each time-bound element names its time constraint (deadline, expiry, freshness window).
- The consequence of missing the constraint is stated.
- A path to handle missed deadlines is named.
- Time-bound elements without consequence or recovery path are flagged for rejection.


### Domain 10 (Cost, Resource & Performance) — 1

#### Cost & Efficiency Reviewer

**Trigger:** plan consumes meaningful resources (time, money, compute, storage, attention)

**Mandate:** Identify the main cost drivers and require safe optimization boundaries. Reject unsupported "minor impact" claims, and reject over-specified plans that spend attention on fixed steps without improving correctness, safety, verification, or user value. Speed must not weaken user gates, verification, or governance obligations.

**Coverage:**

- Main cost drivers (time, money, compute, storage, tool calls, or attention) are identified concretely.
- Safe optimization boundaries are named.
- Unsafe shortcuts that would weaken user gates, verification, or governance obligations are forbidden.
- "Minor impact" claims without quantitative or qualitative backing are flagged for rejection.
- Over-specified plans that spend attention on fixed steps without improving correctness, safety, verification, or user value are flagged.


### Domain 11 (Long-term Care) — 2

#### Long-term Care Reviewer

**Trigger:** plan produces an artifact that requires ongoing care (maintenance, updates, monitoring)

**Mandate:** Estimate future maintenance and modification cost; assess continued operability and observability (failure detection, status visibility, operator response paths). Make accumulated technical/operational debt explicit.

**Coverage:**

- Future maintenance and modification cost is estimated.
- Continued operability is assessed (operator response paths, runtime behavior under stress).
- Observability is assessed (failure detection, status visibility, telemetry).
- Accumulated technical and operational debt is made explicit.
- Artifacts lacking maintenance, observability, or debt accounting are flagged.


#### Documentation Impact Reviewer

**Trigger:** plan changes something that other people, teams, or future agents read about

**Mandate:** Verify related documentation accurately reflects the change — not whether docs were updated, but whether the artifact and the descriptive material remain consistent.

**Coverage:**

- Documentation related to the changed artifact is identified.
- Each piece of related documentation is checked for consistency with the post-change artifact.
- Updates to documentation are required only where artifact-vs-doc drift exists.
- Form-only doc updates (changing wording without resolving substance drift) are flagged.


### Domain 12 (Continuity) — substance subset

#### Persistent Record Reviewer

**Trigger:** plan's outcome must be reflected in durable records outside the plan body itself (memory, registry, configuration, logbook, contracts)

**Mandate:** Identify which durable records require updating; ensure each is named in the plan and has a verification check.

**Coverage:**

- Durable records (memory, registry, configuration, logbook, contracts) that require updating are identified.
- Each durable record is named in the plan body.
- Each durable record's update has a verification check.
- Missing record updates or missing verification checks are flagged for rejection.


### Domain 15 (Confidence) — substance subset

#### Bounded Knowledge Reviewer

**Trigger:** plan operates near the edge of known information; surprises are plausible

**Mandate:** Require an enumeration of concrete scenarios that would invalidate the plan's premises. Reject generic "edge cases may surprise us" without concrete scenarios.

**Coverage:**

- Concrete scenarios that would invalidate the plan's premises are enumerated.
- Each scenario names the specific premise it invalidates.
- Generic "edge cases may surprise us" phrasing without enumeration is flagged for rejection.
- The enumeration is appropriate to the depth of the bounded-knowledge region.


#### Confidence-Shift Reviewer

**Trigger:** plan executes over time during which confidence in some claim may shift

**Mandate:** Require an escalation protocol naming the threshold at which confidence drop triggers pause, reconsultation, or replan.

**Coverage:**

- An escalation protocol is named.
- The threshold at which confidence drop triggers escalation is named concretely.
- The escalation action (pause, reconsultation, replan) is named for each threshold.
- Plans without a named threshold or action are flagged for rejection.


### Domain 16 (Self-Application) — substance subset

#### Coherent Example Reviewer

**Trigger:** plan introduces or modifies a rule and the rule body includes examples (exemplars, anti-examples, illustrations)

**Mandate:** Verify each example self-consistently demonstrates the rule. Reject examples that themselves violate the rule's substance.

**Coverage:**

- Each example in the rule body is checked against the rule's substance.
- Examples that self-consistently demonstrate the rule pass.
- Examples that themselves violate the rule's substance are flagged for rejection.
- Anti-examples are checked for being clear violations, not edge cases.


#### Rule Lifecycle Reviewer

**Trigger:** plan introduces or modifies a rule, especially when the rule constrains future plans (including its own future revisions)

**Mandate:** Verify (a) bootstrap transitional exemption is explicitly declared if self-application is impossible, (b) handling for pre-existing artifacts under the old rule is named (retroactive / prospective / case-by-case), (c) the rule allows itself to be revised by future plans without infinite regress or absurd constraint, with the revision protocol named.

**Coverage:**

- Bootstrap transitional exemption is explicitly declared if self-application is impossible.
- Handling for pre-existing artifacts under the old rule is named (retroactive / prospective / case-by-case).
- The rule allows itself to be revised by future plans without infinite regress.
- The rule does not impose an absurd constraint on its own revision.
- The revision protocol is named explicitly.


## Sensitive-Domain Field Personas (~12)

These personas apply only within sensitive-domain task classes (writing,
translation, personal advice, legal/financial/medical, accessibility-
critical visual work, etc.). They use field-specific vocabulary by design.
Their substance does not generalize to arbitrary domains, and they are not
absorbed into the structural 16-domain system. Invoke them when the plan's
task class is in the relevant family.

### Substantive Writing & Editing

#### Voice and Tone Reviewer

**Mandate:** Verify the deliverable's voice and tone match the audience and purpose. Distinct from generic "Acceptance Criteria"; tone has aesthetic and brand dimensions that aren't checkbox-verifiable.

**Coverage:**

- The deliverable's voice is checked against the intended audience.
- The deliverable's tone is checked against the intended purpose.
- Aesthetic and brand dimensions of tone are evaluated (not just checkbox criteria).
- Voice / tone mismatches with audience or purpose are flagged.


#### Risk Language Reviewer

**Mandate:** Verify the deliverable's wording does not create legal, reputational, or interpersonal risk inadvertently (overpromising, defamation, condescension, exclusion).

**Coverage:**

- The deliverable's wording is checked for inadvertent legal risk.
- The deliverable's wording is checked for inadvertent reputational risk.
- The deliverable's wording is checked for inadvertent interpersonal risk.
- Overpromising, defamation, condescension, and exclusion patterns are flagged.


### Translation & Localization

#### Locale Specialist

**Mandate:** Verify locale-specific conventions (date format, currency, address structure, formality register) are honored.

**Coverage:**

- Locale-specific date formats are honored.
- Locale-specific currency formats are honored.
- Locale-specific address structure is honored.
- Locale-specific formality register is honored.
- Conventions that mix locales without justification are flagged.


#### Meaning Fidelity Reviewer

**Mandate:** Verify the translation preserves source meaning, not only literal word equivalence.

**Coverage:**

- Source meaning is preserved in the translation beyond literal word equivalence.
- Idiomatic phrasing is rendered in target-language idiom rather than literal calque.
- Connotations and register are preserved across the translation.
- Translations that match word-for-word but lose meaning are flagged.


#### Cultural Risk Reviewer

**Mandate:** Verify the translation does not introduce cross-cultural offense, taboo violations, or misread idioms.

**Coverage:**

- Cross-cultural offense risks are identified in the translation.
- Taboo violations in the target culture are identified.
- Misread idioms (literal renderings of target-culture-foreign idioms) are flagged.
- Cultural references that do not translate are noted with handling guidance.


#### Sensitive Language Reviewer

**Mandate:** For translation of sensitive content (legal, medical, mental-health, identity), verify the chosen terms in the target locale carry the intended weight without unintended harm.

**Coverage:**

- Sensitive-content terms (legal, medical, mental-health, identity) are identified in the source.
- Each chosen target-locale term is checked for carrying the intended weight.
- Terms with unintended harm (stigmatizing, marginalizing, or legally loaded) are flagged.
- Locale-specific preferred-term conventions are honored.


### Creative & Visual Work

#### Risk and Sensitivity Reviewer

**Mandate:** For creative work that could touch sensitive territory (identity, trauma, controversy), verify direction has explicit sensitivity boundaries and review checkpoints.

**Coverage:**

- Sensitive territory the creative work could touch (identity, trauma, controversy) is identified.
- Explicit sensitivity boundaries are named in the creative direction.
- Review checkpoints are named at points where sensitive material is introduced.
- Creative direction without sensitivity boundaries or checkpoints is flagged.


#### Accessibility Reviewer

**Mandate:** For visual or interactive work, verify accessibility commitments (alt text, contrast, keyboard navigation, screen-reader compatibility, cognitive load).

**Coverage:**

- Alt text commitments for visual content are verified.
- Contrast commitments for visual content are verified.
- Keyboard-navigation commitments for interactive content are verified.
- Screen-reader compatibility commitments are verified.
- Cognitive-load commitments (clarity, complexity bounds) are verified.


### Personal Advice & Coaching (Sensitive)

#### Autonomy Preserver

**Mandate:** Verify advice respects the recipient's autonomy: present options rather than directives, name uncertainty, avoid manipulation.

**Coverage:**

- Advice is presented as options rather than directives.
- Uncertainty in the advice is named explicitly.
- Manipulative framings (false urgency, false authority, guilt) are avoided.
- The recipient's right to refuse or modify the advice is preserved.


#### Emotional Reality Reviewer

**Mandate:** Verify advice acknowledges the emotional reality of the situation (validation before reframing) and does not dismiss feelings to reach a conclusion faster.

**Coverage:**

- The emotional reality of the situation is acknowledged before reframing.
- Validation precedes problem-solving in the advice.
- Feelings are not dismissed to reach a conclusion faster.
- Premature solution-jumping (skipping over emotional context) is flagged.


#### Support Network Reviewer

**Mandate:** Verify advice considers the recipient's network of care (professionals, family, peers) and identifies escalation paths to qualified humans when relevant.

**Coverage:**

- The recipient's network of care (professionals, family, peers) is considered in the advice.
- Escalation paths to qualified humans are identified when relevant.
- Situations beyond peer-advice scope (crisis, clinical) trigger explicit escalation guidance.
- Advice that isolates the recipient from their network of care is flagged.


### Explanation & Teaching

#### Learner Level Reviewer

**Mandate:** Verify the explanation matches the learner's current level — neither over-explaining (insulting) nor under-explaining (lost).

**Coverage:**

- The learner's current level is named in the explanation's framing.
- The explanation does not over-explain (insulting) for the named level.
- The explanation does not under-explain (lost) for the named level.
- Mismatch between the level claim and the actual explanation depth is flagged.


## Selection Rule

For non-trivial plan-required work, after Trigger Coverage Verifier and Deep
Review Selection identify the required isolated passes:

1. For each conditional domain marked applicable in the plan's Domain
   Coverage self-mark, persist the corresponding substance-triggered persona
   requirements and schedule the selected callable reviewers.
2. For each sensitive-domain task class in the plan's classification, persist
   the corresponding field-persona requirements and schedule the selected
   callable reviewers.
3. Each persona runs in its own review turn (lens-injection isolation).
4. Within domain, persona order is not significant.

When a plan has multiple task classes that draw from overlapping field
personas (e.g., a translation of a personal-advice piece that touches
substantive writing), run each persona only once.

Never add a persona that cannot produce a concrete plan change.

## Section Mapping

Each specialized persona's primary and optional plan-template sections are
recorded in `agentlaw_docs/planning-protocol/persona-section-map.md`.

## Cross-References

- Law authority: `agentlaw_docs/law/PLANNING_AND_REVIEW_RULES.md`
- Triggers and classification: `agentlaw_docs/planning-protocol/task-classification.md`
- Workflow and severity: `agentlaw_docs/planning-protocol/review-method.md`
- Universal personas: `agentlaw_docs/planning-protocol/persona-decks-core.md`
- Section mapping: `agentlaw_docs/planning-protocol/persona-section-map.md`
- Plan template: `agentlaw_docs/planning-protocol/plan-template.md`
- Migration: `agentlaw_docs/references/deck-absorption-matrix-2026-04-29.md`
