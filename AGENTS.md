# AGENTS.md

This file defines the operating rules for AI coding agents and human contributors working on **AnschlussPilot**.

AnschlussPilot is a:

> **Disruption-aware journey decision-support system for German rail.**

Its purpose is not merely to report train delays or predict missed connections.

Its core responsibility is:

> **Given the information available right now, help determine which reasonable action is most likely to produce a better journey outcome.**

The system should continuously reason about questions such as:

1. What changed?
2. Is the current journey still feasible?
3. Is continuing with the current plan still the best choice?
4. What reasonable alternatives exist?
5. What is the expected outcome of each option?
6. Is there enough reliable information to recommend any action at all?

All substantial implementation decisions should be evaluated against those questions.

---

# 1. Repository Truth Comes First

Before modifying the repository, inspect its actual current state.

The repository is the source of truth for what exists.

Do not infer implementation status from:

- this file;
- README descriptions;
- product plans;
- issue text;
- screenshots;
- previous conversations;
- roadmap items;
- comments describing future architecture.

Before any non-trivial change, inspect at minimum:

```text
README.md
AGENTS.md
```

and all relevant:

```text
docs/
source files
tests
schemas
provider adapters
database migrations
CI workflows
configuration
architecture documentation
product requirements
```

that affect the requested change.

If the repository later contains more specific instructions, prefer the most local applicable instruction while preserving the product invariants defined here.

---

# 2. Never Invent Capabilities

Never claim a feature or capability exists unless it is verifiably present.

Do not fabricate:

- supported railway providers;
- realtime integrations;
- historical datasets;
- API capabilities;
- routing capabilities;
- platform-change handling;
- cancellation handling;
- state-reconciliation behaviour;
- prediction models;
- calibrated probabilities;
- machine-learning performance;
- test results;
- benchmarks;
- deployment status;
- production readiness;
- monitoring coverage;
- GDPR compliance;
- security guarantees;
- licensing permission;
- passenger-rights logic.

If something is planned but not implemented, label it as planned.

If a capability cannot be verified, state that it cannot be verified.

Never report tests as passing unless they were actually executed successfully.

---

# 3. Core Product Invariant

AnschlussPilot is not primarily a train-status product.

It is not primarily a connection-probability product.

It is not primarily an alternative-route search product.

The intended reasoning chain is:

```text
Current journey
      ↓
Railway observations
      ↓
Canonical current state
      ↓
Connection feasibility
      ↓
Reasonable actions
      ↓
Outcome estimation
      ↓
Action comparison
      ↓
Decision recommendation
      ↓
Continue monitoring
```

A risk score is an intermediate result.

An alternative itinerary is also an intermediate result.

The product output is the **decision support created by comparing plausible actions and their expected outcomes**.

---

# 4. Product Principles

The following are architectural constraints.

## 4.1 Optimize journey outcomes

Do not optimize only for:

```text
smallest train delay
shortest scheduled journey
highest connection probability
fewest API calls
```

without considering the passenger's complete journey.

The relevant target is closer to:

```text
reliable destination arrival
```

subject to available information and reasonable passenger costs.

---

## 4.2 Decisions over raw railway data

Passenger-facing functionality should prioritize:

```text
recommended action
destination impact
risk
reason
alternative comparison
data freshness
```

before:

```text
raw provider fields
internal status codes
technical metrics
railway metadata
```

Technical details may exist for diagnostics, observability, or advanced views, but they are not the primary product.

---

## 4.3 Risk is not the decision

Never implement:

```text
HIGH_RISK => REROUTE
```

as a universal product rule.

A high-risk connection may still be the best option if:

- the connecting service is also delayed;
- all alternatives are significantly worse;
- the alternative contains even greater uncertainty;
- rerouting creates additional risky transfers.

Likewise, a merely `ATTENTION` connection may justify an early change if a clearly better alternative exists.

Keep **risk assessment** and **action recommendation** as separate concepts.

---

## 4.4 Uncertainty must remain visible

Never transform missing, stale, inferred, or probabilistic information into certainty.

`UNKNOWN` must remain a valid domain result.

Do not silently map:

```text
missing data
stale data
unsupported condition
conflicting observations
```

to:

```text
SAFE
CONTINUE
```

Do not fabricate probabilities.

Do not display unjustified precision.

---

## 4.5 Data quality before model complexity

Prioritize:

```text
correct service identity
correct timestamps
correct event ordering
correct normalization
correct state reconciliation
correct labels
correct freshness semantics
correct backtesting
```

before advanced models.

A sophisticated model using temporally invalid or incorrectly reconciled data is worse than a simple deterministic rule.

---

## 4.6 German rail first

Do not expand into:

```text
European rail
aviation
coaches
taxis
car sharing
general multimodal routing
```

merely because the abstractions could support them.

Build one excellent German rail disruption use case first.

---

# 5. MVP Boundary

The intended MVP is:

> **Given a supported German rail journey containing at least one transfer, AnschlussPilot monitors relevant operational changes, evaluates whether the current plan remains feasible, compares a limited set of reasonable railway alternatives, and explains the currently preferred action.**

The MVP should focus on:

```text
journey state
connection feasibility
alternative generation
outcome comparison
decision recommendation
uncertainty handling
```

Do not expand scope simply to increase feature count.

---

# 6. Explicit Non-Goals

Unless the product definition is deliberately changed, AnschlussPilot is not:

- a DB Navigator replacement;
- a generic train-delay tracker;
- merely a reliability-score website;
- a ticket shop;
- a payment processor;
- a seat-reservation platform;
- a Deutsche Bahn account client;
- a compensation-claim automation system;
- an authoritative passenger-rights engine;
- a guaranteed missed-connection predictor;
- an indoor turn-by-turn station navigator;
- a universal European rail planner;
- a multimodal mobility super-app;
- an LLM chatbot whose main purpose is marketing.

Do not introduce these indirectly without updating the product documentation.

---

# 7. Railway Domain Correctness

Railway behaviour must be treated as a first-class domain problem.

Never assume:

```text
train number == train identity
scheduled transfer == feasible transfer
delay == only disruption type
arrival times only move later
updates arrive in order
provider data is consistent
a train always keeps the same stopping pattern
same-name stations are equivalent
```

Relevant domain situations include:

- changing delays;
- delay corrections;
- platform changes;
- cancellations;
- partial cancellations;
- changed stopping patterns;
- changed train numbers;
- train splitting;
- train joining;
- through services;
- replacement services;
- journey termination;
- cross-midnight journeys;
- duplicate events;
- out-of-order events;
- missing observations;
- stale observations;
- conflicting observations.

Do not implement shortcuts that only work for a polished demo.

---

# 8. Canonical Provider Boundary

Raw external-provider payloads must not become the internal domain model.

Maintain a separation similar to:

```text
External provider
      ↓
Provider adapter
      ↓
Normalization
      ↓
Canonical railway model
      ↓
Journey state
      ↓
Risk / alternative / decision logic
```

Provider-specific fields should remain isolated.

Core decision logic should not depend directly on raw provider JSON.

This allows:

- testing without live providers;
- provider replacement;
- schema evolution;
- explicit handling of missing information;
- stable domain semantics.

---

# 9. Canonical Domain Concepts

Likely domain concepts include:

```text
Station
Journey
JourneyLeg
Connection
ServiceRun
Stop
ScheduledStopEvent
RealtimeObservation
CurrentServiceState
Disruption
RiskAssessment
AlternativeJourney
DecisionCandidate
OutcomeEstimate
DecisionRecommendation
```

Do not create these abstractions speculatively.

Introduce or extend them only when supported by actual requirements.

Domain types should model railway semantics, not external API response shapes.

---

# 10. Service Identity Is Critical

Never assume a displayed train number uniquely identifies a train run.

Identity may depend on:

- provider journey identifiers;
- service date;
- route;
- stop sequence;
- operating context;
- replacement relationships;
- split/join behaviour.

Incorrect identity handling can corrupt:

```text
current journey state
historical observations
risk labels
alternatives
backtests
ML training data
```

Service identity logic must be explicitly tested.

---

# 11. Realtime Observations Are Events, Not Just Values

Do not treat realtime ingestion as destructive overwrites.

Conceptually distinguish:

```text
Scheduled state
Realtime observations
Current interpreted state
Historical observations
```

Example:

```text
15:01  delay +3
15:04  delay +5
15:06  platform changed
15:08  delay +9
15:10  corrected to +6
15:14  partial cancellation
```

The latest interpreted state may change, but previous observations can remain important for:

- debugging;
- reproducibility;
- delay evolution;
- historical reconstruction;
- statistical modelling;
- decision backtesting.

---

# 12. State Reconciliation

Realtime observations may be:

- duplicated;
- delayed;
- out of order;
- corrected;
- incomplete;
- inconsistent.

State reconciliation should therefore be explicit.

Do not assume:

```text
last event received == newest truth
```

without considering provider semantics and timestamps.

When possible, preserve enough information to explain why current state changed.

---

# 13. Temporal Correctness

Time semantics are fundamental.

Where applicable, distinguish:

```text
scheduled time
provider observation time
event effective time
ingestion time
decision time
```

Use timezone-aware values where appropriate.

Handle:

- cross-midnight journeys;
- service dates;
- daylight-saving transitions;
- delayed observations;
- late corrections.

Most importantly:

> **Historical evaluation must only use information that was available at the evaluated decision time.**

---

# 14. Data Freshness

Freshness is part of product correctness.

Do not present old information as realtime merely because it is the latest stored record.

The system should be capable of representing states equivalent to:

```text
LIVE
STALE
UNAVAILABLE
```

A decision recommendation should know the freshness of the evidence it relies on.

If data freshness is insufficient, prefer uncertainty over false confidence.

---

# 15. Data Provenance

Where practical, retain provenance for normalized information.

It should be possible to determine:

- which provider supplied it;
- when it was observed;
- when it was received;
- whether it was directly reported;
- whether it was inferred;
- how it was normalized.

Never silently convert inferred information into provider-reported fact.

---

# 16. Connection Feasibility

A connection must not be reduced to:

```text
incoming_delay > scheduled_transfer_time
```

A conceptual starting point is:

$$
B_{\mathrm{effective}}
======================

## T_{\mathrm{departure,next}}

## T_{\mathrm{arrival,current}}

## T_{\mathrm{transfer}}

T_{\mathrm{safety}}.
$$

But feasibility may also depend on:

- outgoing-service delay;
- cancellation state;
- platform changes;
- transfer-time range;
- station topology;
- changed stop patterns;
- unsupported or stale information.

Keep connection assessment explainable.

---

# 17. Risk Semantics

Risk states should have stable domain meaning.

A conceptual vocabulary is:

```text
SAFE
ATTENTION
HIGH_RISK
MISSED_OR_UNAVAILABLE
UNKNOWN
```

Do not embed localized UI wording directly in core domain logic.

Do not infer that a particular risk state implies a particular action.

---

# 18. Decision Candidates

The decision layer should compare explicit candidate actions.

Possible actions may include:

```text
CONTINUE_CURRENT_PLAN
WAIT_FOR_CONNECTION
REROUTE_EARLY
TAKE_LATER_CONNECTION
USE_ALTERNATIVE_RAIL_ROUTE
NO_RELIABLE_RECOMMENDATION
```

This is conceptual terminology; use repository conventions if different.

Do not generate an unlimited number of alternatives merely because routing makes them available.

The candidate set should remain operationally reasonable.

---

# 19. Outcome Estimates

Each decision candidate should be evaluated against passenger-relevant outcomes where data permits.

Possible dimensions include:

```text
expected destination arrival
destination delay
connection feasibility
number of additional transfers
journey complexity
uncertainty
```

Do not reduce action comparison to only:

```text
earliest scheduled arrival
```

if the product is explicitly intended to optimize reliability.

---

# 20. Separate Facts, Estimates, and Predictions

Structured output must distinguish:

```text
scheduled fact
provider realtime observation
derived state
deterministic estimate
probabilistic prediction
decision recommendation
```

These categories are not interchangeable.

For example:

```text
Platform 7
```

may be provider-reported.

```text
Transfer requirement: 4–6 min
```

may be estimated.

```text
Missed-connection risk: 72%
```

may be probabilistic.

```text
Recommended action: reroute at Mannheim
```

is a decision output.

Do not blur these layers.

---

# 21. Decision Recommendation Is Not a Fact

A recommendation is a result of current evidence and decision logic.

Do not present it as an operational guarantee.

Prefer:

```text
Recommended based on currently available information
```

over:

```text
You must take this train
```

unless the product intentionally supports authoritative operational instructions, which is outside the initial scope.

---

# 22. Recommendation Stability

Realtime state may fluctuate rapidly.

Do not create a product that tells the passenger:

```text
reroute
continue
reroute
continue
```

every few seconds because small estimates changed.

Where appropriate, consider:

- minimum improvement thresholds;
- confidence requirements;
- hysteresis;
- cooldowns;
- state persistence;
- recommendation-change reasons.

Recommendation stability is a product-quality property.

---

# 23. Avoid False Interventions

An unnecessary reroute can make the journey worse.

Do not optimize only for avoiding missed connections.

A decision policy should also consider the cost of:

- unnecessary rerouting;
- additional transfers;
- longer travel;
- unstable advice;
- increased uncertainty.

A false intervention is a real failure mode.

---

# 24. Compare Against Continue-as-Planned

The current itinerary should normally remain an explicit decision candidate.

Do not compare alternatives only against each other.

The key product question often is:

> **Is changing now actually better than doing nothing?**

This requires a baseline candidate representing the current plan.

---

# 25. Earlier Intervention Is a Distinct Capability

Do not limit alternative evaluation to the station where the connection is expected to fail.

A key product opportunity is identifying whether the journey should be changed **before** the threatened transfer point.

Architecture should not unnecessarily assume:

```text
rerouting can only happen at planned transfer station
```

unless that is intentionally an MVP constraint.

---

# 26. Decision Engine Must Be Testable

Core recommendation logic should be testable without:

- a browser;
- live railway APIs;
- production credentials;
- production infrastructure.

Prefer deterministic fixtures and pure domain logic where possible.

Tests should be able to supply:

```text
journey state
observations
candidate alternatives
expected outcomes
```

and assert the resulting recommendation.

---

# 27. Counterfactual Thinking

Decision quality requires asking:

```text
What happens if the passenger continues?
What happens if the passenger changes?
```

This is different from predicting only what will happen under the current itinerary.

When building future evaluation infrastructure, preserve this distinction.

Do not label an alternative as "better" without defining what it is being compared against.

---

# 28. Historical Reconstruction

If historical storage is implemented, it should ideally support reconstruction of:

```text
What was known at time t?
What journey state existed?
What actions were available?
What would the policy recommend?
What happened afterward?
```

This is more valuable than storing only final delay values.

Historical reconstruction is foundational for trustworthy backtesting.

---

# 29. Backtesting Before Deployment

Changes to deterministic rules, statistical models, or decision policies should eventually be evaluated against historical data before production use.

A backtest should preserve:

- chronological information availability;
- provider-state semantics;
- candidate availability;
- decision timestamps;
- actual outcomes.

Do not use future observations to improve past decisions.

---

# 30. Baseline First

Before introducing machine learning, establish meaningful deterministic baselines.

Examples may include:

```text
simple transfer-buffer rule
risk-threshold rule
continue-unless-impossible
next-reasonable-connection rule
```

The exact baseline should match the domain.

ML must be evaluated against the baseline rather than against no system at all.

---

# 31. Machine Learning Policy

Do not introduce ML merely because the problem supports it.

Before adding a model, require:

1. a clear target;
2. adequate historical data;
3. correct temporal labels;
4. a deterministic baseline;
5. leakage analysis;
6. appropriate evaluation metrics;
7. documented uncertainty;
8. evidence of product improvement.

Potential future targets include:

$$
P(\text{miss connection}\mid X_t),
$$

$$
P(T_{\mathrm{arrival}}\leq t\mid X_t),
$$

or:

$$
P(Y\mid X_t,a).
$$

The last category may eventually be most relevant to decision quality.

---

# 32. Feature Leakage

Railway prediction systems are particularly vulnerable to leakage.

Do not use information unavailable at prediction time.

Potential leakage includes:

- final arrival delay;
- future cancellation events;
- later platform changes;
- downstream events occurring after prediction time;
- final outcome labels joined into input features.

Random row-level train/test splitting may be inappropriate.

Prefer temporal validation where applicable.

---

# 33. Calibration Matters

Probabilistic outputs must be evaluated probabilistically.

Useful evaluation may include:

- Brier score;
- log loss;
- calibration curves;
- reliability diagrams;
- temporal holdouts;
- confidence intervals;
- subgroup evaluation;
- distribution-shift monitoring.

Do not expose:

```text
78% risk
```

merely because a classifier produces `0.78`.

If calibration is inadequate, prefer categorical risk states.

---

# 34. Evaluate the Decision Policy

Prediction quality and decision quality are different.

A model may improve AUC while making worse journey recommendations.

Where possible, evaluate product outcomes such as:

```text
destination delay
successful journey completion
missed connections
false interventions
unnecessary rerouting
warning lead time
recommendation stability
```

The best predictive model is not automatically the best product policy.

---

# 35. UX Is Decision-First

Primary passenger-facing hierarchy should resemble:

```text
Recommended action
      ↓
Expected destination impact
      ↓
Risk
      ↓
Reason
      ↓
Alternative comparison
      ↓
Technical detail
```

Do not make the passenger interpret internal railway data to discover the decision.

---

# 36. Mobile Pressure Context

Assume the passenger may be:

- standing on a platform;
- walking;
- carrying luggage;
- under time pressure;
- using a small screen;
- experiencing poor connectivity.

Critical information should be scannable.

Avoid interfaces that require extensive reading before the next action becomes clear.

---

# 37. Accessibility

Do not communicate risk or action solely through colour.

Use explicit labels.

Prefer semantic HTML.

Maintain keyboard accessibility where applicable.

Accessibility regressions are product defects.

---

# 38. Localization

Engineering documentation may use English.

Passenger-facing German should be treated as genuine localization.

Use established railway terminology where appropriate, such as:

```text
Anschluss
Umstieg
Verspätung
Zugausfall
Gleisänderung
voraussichtliche Ankunft
```

Do not mix localized strings into domain logic.

---

# 39. Privacy by Default

Collect as little personal information as possible.

Avoid unnecessary:

- names;
- emails;
- DB credentials;
- tickets;
- payment data;
- continuous GPS;
- persistent journey history tied to identity.

If a new feature introduces personal data, consider:

```text
purpose
legal basis
retention
access
deletion
security
data minimization
```

before implementation.

---

# 40. Security

Never commit secrets.

Treat external provider responses as untrusted input.

Validate external input.

Use environment variables or proper secret management.

If authentication is introduced, enforce authorization server-side.

Do not assume hidden frontend controls provide security.

---

# 41. Legal and Licensing Boundaries

Do not assume that publicly accessible railway data may automatically be:

- stored indefinitely;
- redistributed;
- scraped;
- used commercially;
- used for model training;
- re-licensed.

Before adding a provider integration, verify its actual current terms.

Document material restrictions near the provider code or appropriate repository documentation.

---

# 42. Passenger Rights Are a Separate Domain

Operational journey recommendations and legal entitlement are not the same thing.

The system may say:

```text
This appears to be the better railway option
based on currently available information.
```

It must not infer without validated legal logic:

```text
You are legally entitled to board this service.
```

Passenger-rights support should require:

- versioned rules;
- effective dates;
- legal validation;
- explicit jurisdiction.

Do not use an LLM as the authoritative legal engine.

---

# 43. LLM Boundaries

LLMs must not become authoritative for:

- timetable facts;
- realtime railway state;
- arithmetic transfer feasibility;
- service identity;
- provider normalization;
- legal entitlement;
- fare validity.

If generative text is introduced, it should explain already structured domain conclusions rather than invent them.

---

# 44. Never Hide Uncertainty with Language Generation

If structured state says:

```text
UNKNOWN
```

generated text must remain consistent with uncertainty.

An LLM must never transform:

```text
estimated
unknown
unverified
stale
```

into:

```text
confirmed
guaranteed
certain
```

---

# 45. Testing Strategy

Test railway edge cases, not only happy paths.

Relevant scenarios include:

```text
normal connection
incoming delay
outgoing delay
shrinking buffer
delay correction
platform change
full cancellation
partial cancellation
changed stop pattern
changed train number
split/join service
duplicate event
out-of-order event
provider timeout
stale data
missing data
same-name station
cross-midnight journey
earlier rerouting opportunity
alternative becomes unavailable
recommendation reversal pressure
```

---

# 46. Test Decision Cases Explicitly

Decision-layer tests should include situations such as:

```text
HIGH_RISK but CONTINUE is best
ATTENTION but REROUTE_EARLY is best
MISSED and later alternative is preferred
UNKNOWN => no reliable recommendation
alternative has earlier arrival but much higher risk
minor improvement should not trigger recommendation change
```

This prevents accidental coupling between risk state and action.

---

# 47. Provider Contract Tests

Provider adapters should test:

- valid responses;
- missing fields;
- unknown status values;
- malformed timestamps;
- duplicate observations;
- cancellations;
- platform changes;
- syntactically valid but unexpected values.

Unknown provider values should degrade gracefully where possible.

---

# 48. Error Handling

Differentiate errors such as:

```text
provider unavailable
provider rejected request
malformed provider data
unsupported journey
normal missing information
normal UNKNOWN risk
internal processing error
database failure
```

Do not collapse all failures into one generic exception where the distinction matters.

---

# 49. Observability

Meaningful future metrics may include:

```text
provider availability
provider latency
data freshness
normalization failures
state-reconciliation failures
risk-evaluation failures
alternative-generation failures
decision-engine failures
recommendation changes
API latency
database health
```

Prefer metrics that expose user-impacting failures.

---

# 50. Product Analytics

Avoid vanity metrics as the primary measure of success.

Potentially useful metrics include:

- warning lead time;
- false warning rate;
- unnecessary rerouting rate;
- missed-connection detection;
- recommendation stability;
- expected versus actual destination arrival;
- decision-policy improvement;
- unknown-state frequency;
- data freshness.

Analytics must respect privacy requirements.

---

# 51. API Design

Application APIs should expose AnschlussPilot domain concepts rather than raw provider schemas.

Prefer stable representations for:

```text
Journey
Connection
RiskAssessment
AlternativeJourney
OutcomeEstimate
DecisionRecommendation
```

When API semantics change:

1. inspect consumers;
2. update validation;
3. update tests;
4. update examples;
5. update documentation.

---

# 52. Validation and Types

Validate structured data at system boundaries where supported.

Prefer explicit types over loosely structured maps.

Stable domain states should generally be represented using:

- enums;
- tagged unions;
- discriminated unions;
- typed value objects;

or equivalent language features.

Avoid uncontrolled string values for critical domain state.

---

# 53. Database Changes

Treat persistence changes carefully.

Before changing schema:

- inspect existing migrations;
- preserve migration history;
- consider existing data;
- consider backward compatibility;
- update tests.

Historical observation schemas require particular care because migration mistakes can invalidate later backtesting.

---

# 54. Dependencies

Before adding a dependency, ask:

```text
What problem does this solve?
Does the existing stack already solve it?
What maintenance burden does it add?
What security surface does it add?
Is its licence compatible?
```

Avoid dependency proliferation.

---

# 55. Avoid Speculative Architecture

Do not introduce:

```text
microservices
Kafka
Kubernetes
GraphQL
vector databases
LLM orchestration
event sourcing
complex workflow engines
```

merely because the project might need them someday.

Choose the simplest architecture that preserves current correctness requirements.

---

# 56. Performance

Optimize passenger-critical operations first:

```text
journey loading
realtime refresh
state reconciliation
risk recomputation
alternative evaluation
decision recomputation
mobile rendering
```

Measure before optimizing.

Correctness comes before speculative performance work.

---

# 57. Resilience

External providers will fail.

Provider failure must not automatically:

- crash the UI;
- erase last known state;
- mark a connection safe;
- produce a recommendation from stale data without warning.

Graceful degradation is part of product correctness.

---

# 58. Recommendation Changes Must Be Explainable

When the preferred action changes, the system should ideally be able to identify the material reason.

Examples:

```text
outgoing train is now delayed
incoming delay increased
alternative was cancelled
platform changed
transfer requirement increased
data became stale
```

Avoid opaque recommendation flips.

---

# 59. Documentation Must Reflect Reality

Documentation must describe what actually exists.

Update documentation when implementation materially changes:

- setup;
- architecture;
- supported providers;
- risk semantics;
- decision semantics;
- schemas;
- testing;
- deployment.

Do not add fake installation commands or future API examples.

---

# 60. Naming

Use domain-specific names.

Prefer:

```text
Connection
ServiceRun
RealtimeObservation
RiskAssessment
DecisionCandidate
OutcomeEstimate
DecisionRecommendation
TransferBuffer
```

Avoid vague names such as:

```text
Manager
Thing
Helper2
DataProcessor
GenericService
```

when a domain concept exists.

---

# 61. Change Discipline

Keep changes focused.

Avoid combining unrelated:

- feature work;
- refactoring;
- dependency upgrades;
- formatting;
- schema redesign.

Do not rewrite the entire repository to solve a narrow issue.

---

# 62. Refactoring

Refactor to improve:

- correctness;
- domain boundaries;
- testability;
- readability;
- maintainability.

Do not refactor merely to impose stylistic preference.

Preserve observable behaviour unless the task explicitly changes it.

---

# 63. Required Agent Workflow

For every non-trivial task, follow this sequence.

## Step 1 — Inspect

Read the relevant:

```text
code
tests
schemas
configuration
documentation
```

Do not assume architecture.

## Step 2 — Locate the Domain Owner

Determine where the behaviour belongs:

```text
provider adapter
normalization
service identity
state reconciliation
journey model
risk engine
alternative generation
outcome estimation
decision engine
API
frontend
persistence
```

Fix the problem at the correct layer.

## Step 3 — Preserve Product Scope

Confirm the change contributes to the current AnschlussPilot product boundary.

Avoid accidental expansion.

## Step 4 — Preserve Temporal Correctness

For data or modelling changes, ask:

> Would this information actually be available at the time the system makes the decision?

## Step 5 — Implement the Smallest Coherent Change

Prefer a narrow complete solution over broad redesign.

## Step 6 — Test the Relevant Domain Cases

Run focused tests first.

Then broader repository checks where practical.

## Step 7 — Review Uncertainty and Failure Modes

Consider, where relevant:

```text
missing data
stale data
duplicate data
out-of-order data
provider failure
conflicting data
unknown values
cross-midnight journeys
alternative failure
recommendation instability
```

## Step 8 — Review Decision Effects

If the change affects risk, routing, alternatives, or recommendation logic, ask:

```text
Could this trigger unnecessary rerouting?
Could this suppress a useful intervention?
Could this make recommendations unstable?
Could it confuse risk with action?
```

## Step 9 — Update Documentation

Update documentation if semantics or capabilities changed.

## Step 10 — Report Truthfully

Summarize:

```text
what changed
why it changed
what was tested
what was not tested
known limitations
```

Never fabricate validation.

---

# 64. Definition of Done

A substantial change is complete only when applicable checks are satisfied:

- requested behaviour is implemented;
- product scope remains intact;
- railway-domain semantics are preserved;
- provider boundaries remain clean;
- temporal correctness was considered;
- stale/missing data behaviour was considered;
- risk and action remain conceptually separate;
- decision stability was considered;
- relevant tests were added or updated;
- executed tests pass;
- static/type/lint checks pass where applicable;
- documentation matches behaviour;
- no secrets were added;
- no unsupported capability claims were introduced;
- no licensing assumptions were silently introduced.

If something could not be verified, state that clearly.

---

# 65. When Requirements Are Ambiguous

Prefer the interpretation that:

1. preserves existing repository behaviour;
2. keeps the MVP narrow;
3. protects railway-domain correctness;
4. preserves temporal validity;
5. preserves uncertainty;
6. avoids unnecessary intervention;
7. avoids irreversible architecture.

Document material assumptions.

Do not create a large subsystem merely to resolve a small ambiguity.

---

# 66. Engineering Priority Hierarchy

When priorities conflict, prefer:

```text
Railway-domain correctness
          ↓
Temporal correctness
          ↓
Data correctness
          ↓
Decision usefulness
          ↓
Decision stability
          ↓
Reliability
          ↓
Explainability
          ↓
Prediction sophistication
          ↓
Architectural cleverness
```

A sophisticated system that confidently recommends the wrong action is a failure.

A simpler system that correctly says:

> **No reliable recommendation — realtime information is insufficient.**

is behaving properly.

---

# 67. Final Product Rule

Before completing any substantial change, ask:

> **Does this make AnschlussPilot better at deciding what a passenger should do while there is still time to improve the journey outcome?**

If the answer is no, reconsider whether the change belongs in the product.

The intended sequence is:

```text
Observe the disruption
        ↓
Reconstruct the current state
        ↓
Understand the risk
        ↓
Generate reasonable actions
        ↓
Estimate their outcomes
        ↓
Compare them
        ↓
Recommend carefully
        ↓
Continue monitoring
```

That is AnschlussPilot's core engineering contract.
