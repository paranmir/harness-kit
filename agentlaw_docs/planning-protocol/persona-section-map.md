# Persona Section Map

## Purpose

Map each review persona to the plan-template sections for which it is primarily
accountable, plus optional sections it may consult. Every persona receives and
may inspect the **whole current plan**; the map narrows accountability and
targeted re-review triggers, not visibility. The map drives **section-based
revision re-review** per `agentlaw_docs/law/PLANNING_AND_REVIEW_RULES.md` § Required
Planning Workflow.

## Format

- **Tier**: `universal` (always evaluated for non-trivial work),
  `substance-triggered` (evaluated when the corresponding Domain Coverage
  box is marked), or `sensitive-domain` (evaluated when the task class is
  in the sensitive-domain family). `workflow-stage` roles run only when their
  named protocol-stage trigger is satisfied.
- **Primary sections**: the persona must make an explicit, cited judgment about
  these sections. A change to one of them triggers that persona's targeted
  re-review when the persona is otherwise applicable.
- **Optional sections**: likely context for the persona's judgment. Optional
  mappings do not limit whole-plan inspection and optional-only changes do not
  trigger that persona's targeted re-review.

When section-based re-review applies, a persona is re-invoked if **any of
its primary sections** changed in the revision. Optional-only changes do
not trigger re-invocation. Finding owners are re-invoked until their findings
close, even when another persona owns the changed primary section. Unknown,
broad, classification-changing, or domain-changing revisions require the
full-review fallback defined by the review workflow.

## Universal Personas

| Persona | Tier | Primary sections | Optional sections |
| --- | --- | --- | --- |
| Trigger Coverage Verifier | universal | Classification, Domain Coverage | (whole plan body) |
| Intent Statement Reviewer | universal | Intent | Status |
| Underlying-Need Reviewer | universal | Intent | Scope |
| Artifact Shape Reviewer | universal | Intent | Verification |
| Acceptance Criteria Reviewer | universal | Intent | Verification, Risk |
| Affected Surface Reviewer | universal | Scope | Premise, Risk |
| Stakeholder Map Reviewer | universal | Scope | Continuity |
| Downstream Impact Reviewer | universal | Scope | External Contract & Compatibility, Long-term Care |
| Indirect Effect Reviewer | universal | Scope | Risk, Long-term Care |
| Temporal Scope Reviewer | universal | Scope | Time & Sequencing |
| Premise & Source Reviewer | universal | Premise | Confidence |
| Assumption-Path Reviewer | universal | Premise, Confidence | Risk |
| Prerequisite Chain Reviewer | universal | Premise | Risk, Verification |
| Prior Art Reviewer | universal | Premise | Scope |
| Risk Classification Reviewer | universal | Risk | Scope, Verification |
| User Gate Reviewer | universal | Risk | Status, Continuity |
| Rollback Path Reviewer | universal | Risk | Verification |
| Fallback Plan Reviewer | universal | Risk | Premise, Confidence |
| Verification Reviewer | universal | Verification | Risk, Acceptance Criteria |
| Internal Continuity Reviewer | universal | Continuity | Status |
| Governance & Compliance Reviewer | universal | Governance | Premise |
| Confidence Label Reviewer | universal | Confidence | Premise |
| Undecided Item Reviewer | universal | Confidence | Continuity |
| Rule-on-Self Reviewer | universal | Self-Application | Classification, Governance |
| Form-vs-Substance Auditor | universal | Premise, Status, Acceptance Criteria | All sections (audits each in turn) |
| Cross-Section Coherence Reviewer | universal | Verification, Work Breakdown, Acceptance Criteria | Intent, Scope, Risk |
| Code-Fact Claim Verifier | universal | Premise, Intent (Acceptance Criteria) | Confidence, Status |

## Workflow-Stage Personas

These roles inspect the whole plan at their named lifecycle stage. Their
primary-section mappings express mandatory judgment anchors; their stage
triggers, not ordinary section edits alone, activate them.

| Persona | Tier | Primary sections | Optional sections |
| --- | --- | --- | --- |
| Plan Synthesizer | workflow-stage (open substantive findings) | Work Breakdown, Plan Review Evidence | All sections named by open findings; Intent, Risk, Verification, Execution Gates |
| Outcome Sufficiency Reviewer | workflow-stage (findings closed and amendments re-reviewed) | Intent, Work Breakdown, Verification | Scope, Premise, Risk, Execution Gates, Plan Review Evidence |

## Substance-Triggered Personas

| Persona | Tier | Primary sections | Optional sections |
| --- | --- | --- | --- |
| Source Conflict Reviewer | substance-triggered (Premise) | Premise | Confidence |
| Runtime State Reviewer | substance-triggered (Premise) | Premise | Verification |
| Access-Premise Reviewer | substance-triggered (Premise) | Premise | Security & Trust Boundaries |
| Design Alternatives Reviewer | substance-triggered (material design choice) | Work Breakdown, Code Architecture & Implementation Control | Intent, Scope, Premise, Risk, Verification, Long-term Care |
| Code Design & Maintainability Reviewer | substance-triggered (code) | Code Plan Fidelity, Code Architecture & Implementation Control, Work Breakdown | Scope, Risk, Verification, External Contract & Compatibility, Long-term Care |
| Trust Boundary Reviewer | substance-triggered (Security) | Security & Trust Boundaries | Scope, Data Stewardship |
| Sensitive Information Handling Reviewer | substance-triggered (Security) | Security & Trust Boundaries | Data Stewardship |
| Permission Boundary Reviewer | substance-triggered (Security) | Security & Trust Boundaries | Premise (Access-Premise) |
| Adversarial Path Reviewer | substance-triggered (Security) | Security & Trust Boundaries | Risk, Verification |
| Supply Chain Reviewer | substance-triggered (Security) | Security & Trust Boundaries | Premise |
| Information Integrity Reviewer | substance-triggered (Data) | Data Stewardship | Premise, Verification |
| Privacy & Retention Reviewer | substance-triggered (Data) | Data Stewardship | Security & Trust Boundaries |
| Interface Contract Reviewer | substance-triggered (Contract) | External Contract & Compatibility | Scope, Long-term Care |
| Sequencing & Parallel Execution Reviewer | substance-triggered (Time) | Time & Sequencing | Risk, Verification |
| Deadline Reviewer | substance-triggered (Time) | Time & Sequencing | Risk |
| Cost & Efficiency Reviewer | substance-triggered (Cost) | Cost, Resource & Performance | Scope, Long-term Care |
| Long-term Care Reviewer | substance-triggered (Long-term) | Long-term Care | Risk, Continuity |
| Documentation Impact Reviewer | substance-triggered (Long-term) | Long-term Care | Scope, Continuity |
| Persistent Record Reviewer | substance-triggered (Continuity) | Continuity | Scope, Governance |
| Bounded Knowledge Reviewer | substance-triggered (Confidence) | Confidence | Risk, Premise |
| Confidence-Shift Reviewer | substance-triggered (Confidence) | Confidence | Risk, Verification |
| Coherent Example Reviewer | substance-triggered (Self-Application) | Self-Application | Governance |
| Rule Lifecycle Reviewer | substance-triggered (Self-Application) | Self-Application | Bootstrap Transitional Declaration, Governance |
| Test Specification Reviewer | substance-triggered (code) | Code Plan Fidelity, Verification | Acceptance Criteria |
| Risk-Weighted Test Reviewer | substance-triggered (code) | Code Plan Fidelity, Risk | Verification |
| Defect Detection Adequacy Reviewer | substance-triggered (code) | Code Plan Fidelity, Verification | Acceptance Criteria |
| Invariant Specification Reviewer | substance-triggered (code) | Code Plan Fidelity, Verification | Acceptance Criteria |

## Sensitive-Domain Field Personas

These personas apply only within sensitive-domain task classes (per
`task-classification.md`) and use field-specific vocabulary. They review
deliverable-quality dimensions that do not generalize to arbitrary
domains.

| Persona | Tier | Primary sections | Optional sections |
| --- | --- | --- | --- |
| Voice and Tone Reviewer | sensitive-domain (writing/editing) | Intent (artifact shape), Acceptance Criteria | Scope (audience) |
| Risk Language Reviewer | sensitive-domain (writing/editing/legal/medical) | Intent (artifact shape), Acceptance Criteria | Governance, Risk |
| Locale Specialist | sensitive-domain (translation/localization) | Intent (artifact shape) | Premise (sources for terminology) |
| Meaning Fidelity Reviewer | sensitive-domain (translation/localization) | Intent | Premise |
| Cultural Risk Reviewer | sensitive-domain (translation/localization, creative) | Intent, Acceptance Criteria | Scope (stakeholders) |
| Sensitive Language Reviewer | sensitive-domain (sensitive translation) | Intent | Premise |
| Risk and Sensitivity Reviewer | sensitive-domain (creative) | Intent, Acceptance Criteria | Risk |
| Accessibility Reviewer | sensitive-domain (visual/UI) | Intent (artifact shape), Acceptance Criteria | Scope (stakeholders) |
| Autonomy Preserver | sensitive-domain (personal advice/coaching) | Intent, Acceptance Criteria | Continuity |
| Emotional Reality Reviewer | sensitive-domain (personal advice/coaching) | Intent | Acceptance Criteria |
| Support Network Reviewer | sensitive-domain (personal advice/coaching) | Intent (acceptance/escalation) | Continuity |
| Learner Level Reviewer | sensitive-domain (explanation/teaching) | Intent, Acceptance Criteria | Scope (stakeholders) |

## Section Naming

The section names above match `agentlaw_docs/planning-protocol/plan-template.md`
exactly. Renaming a section in the template requires updating this map and
republishing both files together (Stage 4 mirror obligation).

## Section-Based Re-Review — Worked Example

Suppose a non-trivial plan is reviewed and the consolidation step requires
revisions to:

- the **Premise** section (a fact's verification was challenged), and
- the **Risk** section (a fallback was added).

Re-review invokes only personas whose primary sections include Premise or
Risk:

- Premise: Premise & Source Reviewer, Assumption-Path Reviewer, Prerequisite
  Chain Reviewer, Prior Art Reviewer, Source Conflict Reviewer (if marked),
  Runtime State Reviewer (if marked), Access-Premise Reviewer (if marked).
- Risk: Risk Classification Reviewer, User Gate Reviewer, Rollback Path
  Reviewer, Fallback Plan Reviewer.

Personas mapped to other primary sections (e.g., Intent Statement Reviewer)
are not re-invoked. Their prior findings stand because their sections did
not change.

The Trigger Coverage Verifier always re-runs after revisions because its
primary sections (Classification, Domain Coverage) implicitly depend on the
plan body and may need to re-check that revisions did not introduce a new
trigger.

## Cross-References

- Law authority: `agentlaw_docs/law/PLANNING_AND_REVIEW_RULES.md`
- Plan template: `agentlaw_docs/planning-protocol/plan-template.md`
- Workflow: `agentlaw_docs/planning-protocol/review-method.md`
- Persona decks: `agentlaw_docs/planning-protocol/persona-decks-core.md`,
  `agentlaw_docs/planning-protocol/persona-decks-specialized.md`
