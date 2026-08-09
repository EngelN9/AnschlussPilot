# AnschlussPilot

**Disruption-Aware Journey Decision Support for German Rail**

> AnschlussPilot turns changing railway conditions into continuously updated journey decisions.

AnschlussPilot is a German rail journey-reliability project focused on a problem that conventional delay displays do not fully solve:

> **Given everything that is known right now, what should I do to maximize the chance of reaching my destination reliably?**

A passenger does not ultimately care whether one train is `+8 min`.

They care whether:

- the next connection is still realistic;
- waiting for the original connection is still the best choice;
- changing the journey earlier would produce a better outcome;
- an alternative route would reduce the expected destination delay;
- the available information is reliable enough to make a decision at all.

AnschlussPilot is therefore not intended to be another train-delay tracker or a simple connection-probability display.

It is designed as a **realtime journey decision-support system**.

---

## Project Status

> [!IMPORTANT]
> **AnschlussPilot is currently in the product-definition / pre-MVP stage.**

This README defines the intended product direction, engineering principles, system boundaries, and MVP.

It does **not** imply that any of the following already exist:

- a production application;
- a live Deutsche Bahn integration;
- a specific supported railway API;
- historical railway datasets;
- validated connection-risk models;
- machine-learning models;
- calibrated probabilities;
- production infrastructure;
- passenger-rights functionality;
- guaranteed railway coverage.

Capabilities should only be described as implemented once they are verifiably present in the repository.

---

# The Problem

German rail applications can already expose substantial operational information:

```text
Train delay
Cancellation
Platform change
Updated departure
Updated arrival
Alternative connections
```

But raw operational information is not the same as a decision.

Consider a passenger travelling:

```text
Mannheim
   ↓
Frankfurt Hbf
   ↓
Hamburg Hbf
```

The incoming train is delayed.

The passenger's original transfer in Frankfurt is becoming increasingly difficult.

A conventional system may report:

```text
Incoming train: +8 min
Connection time: 11 min
```

A connection-risk system may improve this to:

```text
Connection success probability: 41%
```

But the passenger's real problem remains unresolved:

> **Should I continue to Frankfurt, or should I change the journey before Frankfurt?**

AnschlussPilot is intended to answer this higher-level question.

For example:

```text
HIGH RISK

Current transfer margin:
2–4 min

Estimated transfer requirement:
6–8 min

Continuing to Frankfurt is no longer
the best available option.

Recommended action:
Change at Mannheim.

Alternative:
ICE xxx

Expected destination arrival:
18:37

If continuing with the original plan:
Expected destination arrival:
19:04
```

The product is therefore concerned with **journey intervention**, not merely disruption observation.

---

# Product Thesis

The central thesis of AnschlussPilot is:

> **Predict the journey outcome, compare the available actions, and help the passenger choose the best next step.**

The project follows six principles:

> **1. Optimize the journey outcome, not merely individual train punctuality.**

> **2. Give the passenger a decision, not just railway data.**

> **3. Treat connection risk as an input to a decision, not as the final product.**

> **4. Never present uncertainty as certainty.**

> **5. Data quality and railway-domain correctness come before model complexity.**

> **6. Solve one excellent German rail disruption use case before expanding scope.**

---

# What Makes AnschlussPilot Different

The product should not stop at:

```text
Your train is delayed.
```

It should not stop at:

```text
Your connection is at high risk.
```

And it should not stop at:

```text
Here is the next scheduled train.
```

The intended product loop is:

```text
Current journey
      ↓
Realtime railway state
      ↓
Connection feasibility
      ↓
Possible interventions
      ↓
Outcome estimation
      ↓
Compare alternatives
      ↓
Recommended action
      ↓
Continue monitoring
```

This makes AnschlussPilot a **continuously updating decision engine** rather than a static reliability estimator.

---

# Core Product Questions

At any point during a disrupted journey, AnschlussPilot should try to answer five questions.

## 1. What is happening?

Examples:

```text
Incoming service delayed
Connection buffer shrinking
Platform changed
Outgoing service also delayed
Original service cancelled
Realtime information unavailable
```

## 2. Is the current plan still feasible?

The system evaluates whether the passenger can still reasonably execute the current itinerary.

## 3. Is continuing with the current plan still optimal?

A technically possible connection is not always the best choice.

Waiting for the original journey may lead to a worse destination outcome than rerouting earlier.

## 4. What alternatives are available?

Alternative actions may include:

```text
continue as planned
change earlier
take a later connection
use a different railway routing
wait because the connecting service is also delayed
```

Only actions supported by reliable railway information should be considered.

## 5. What happens if I choose each action?

The product should compare the expected destination consequences of different choices.

Examples include:

```text
expected arrival time
additional destination delay
number of additional transfers
connection risk
uncertainty
```

---

# From Risk Prediction to Decision Policy

Connection-risk prediction is useful, but it is an intermediate problem.

A future probabilistic model may estimate:

$$
P(\text{miss connection}\mid X_t),
$$

where $X_t$ represents the information available at time $t$.

But AnschlussPilot ultimately wants to reason about actions.

Conceptually, the system may compare:

$$
a^*
===

\arg\max_{a \in A_t}
\mathbb{E}
\left[
U(Y)\mid X_t,a
\right],
$$

where:

- $X_t$ is the currently observable journey state;
- $A_t$ is the set of reasonable available actions;
- $Y$ is the eventual journey outcome;
- $U$ represents the passenger-relevant value of that outcome.

This is a product direction, not a claim that the MVP requires a sophisticated optimization algorithm.

The first implementation should remain simple, explainable, and testable.

---

# Target Users

The initial target user is:

> **An individual passenger travelling through Germany on a rail journey containing at least one transfer.**

The MVP is especially relevant to:

- ICE, IC, and EC journeys;
- long-distance journeys combined with regional rail;
- passengers with time-sensitive transfers;
- international travellers unfamiliar with German railway operations;
- travellers for whom missed connections produce significant downstream delay.

The initial product is not intended as a B2B railway-operations system.

---

# Connection Feasibility

A transfer should not be reduced to:

$$
\text{incoming delay}

>

\text{scheduled transfer time}.
$$

A useful conceptual starting point is an **effective transfer buffer**:

$$
B_{\mathrm{effective}}
======================

## T_{\mathrm{departure,next}}

## T_{\mathrm{arrival,current}}

## T_{\mathrm{transfer}}

T_{\mathrm{safety}},
$$

where:

- $T_{\mathrm{arrival,current}}$ is the current expected arrival of the incoming service;
- $T_{\mathrm{departure,next}}$ is the current expected departure of the connecting service;
- $T_{\mathrm{transfer}}$ is the estimated transfer requirement;
- $T_{\mathrm{safety}}$ is an additional operational margin.

For example:

```text
Scheduled transfer        12 min
Incoming delay             6 min
Transfer requirement       4 min
Safety margin              2 min
--------------------------------
Effective buffer           0 min
```

But connection feasibility cannot always be expressed by a single arithmetic rule.

Relevant state may also include:

- outgoing-train delay;
- platform changes;
- cancellations;
- partial cancellations;
- changed stop patterns;
- train splitting or joining;
- through services;
- altered train numbers;
- transfer-station topology;
- stale information;
- unavailable information.

Railway-domain correctness therefore belongs in the core architecture.

---

# Risk States

The initial product should use a small and stable risk vocabulary.

| State                    | Meaning                                                                         |
| ------------------------ | ------------------------------------------------------------------------------- |
| **Safe**                 | Current information indicates a reasonable transfer margin.                     |
| **Attention**            | The transfer margin is becoming limited or uncertain.                           |
| **High Risk**            | Missing the planned connection is a material possibility.                       |
| **Missed / Unavailable** | The original connection is no longer feasible according to current information. |
| **Unknown**              | The system lacks enough reliable information to assess the connection.          |

`Unknown` is a first-class result.

The system must be allowed to say:

> **Realtime information is insufficient to assess this connection reliably.**

It must never convert missing information into artificial certainty.

---

# Decision States

Risk and action should remain conceptually separate.

For example:

```text
Risk:
HIGH RISK

Current decision:
CONTINUE
```

may be valid if the connecting train is also heavily delayed.

Likewise:

```text
Risk:
ATTENTION

Current decision:
REROUTE EARLY
```

may be appropriate if a much better alternative is available before the planned transfer station.

This distinction is important.

**High risk does not automatically imply rerouting.**

The product should compare outcomes rather than applying a single fixed reaction to each risk state.

---

# Explainability

Every recommendation should be explainable using the structured information that produced it.

A result might look like:

```text
Recommended action:
Change at Mannheim

Why?

Original Frankfurt connection:
High Risk

Estimated Frankfurt transfer margin:
2–4 min

Estimated transfer requirement:
6–8 min

Alternative from Mannheim:
Expected destination arrival 18:37

Continue to Frankfurt:
Expected destination arrival 19:04
```

The goal is not to expose internal algorithms.

The goal is to allow the passenger to understand the decision.

---

# MVP

The MVP should remain deliberately narrow.

> **Given a supported German rail journey containing at least one transfer, AnschlussPilot monitors relevant operational changes, evaluates the feasibility of the current plan, compares a limited set of reasonable railway alternatives, and explains the currently preferred action.**

The MVP does not need to solve general transportation optimization.

---

## Intended MVP Capabilities

| Capability                  | Responsibility                                                            |
| --------------------------- | ------------------------------------------------------------------------- |
| **Journey input**           | Select a supported origin, destination, date/time, and itinerary.         |
| **Timetable ingestion**     | Represent scheduled services, stops, arrivals, departures, and transfers. |
| **Realtime ingestion**      | Process operational updates supported by the selected provider.           |
| **Journey state**           | Maintain the passenger's current itinerary and transfer structure.        |
| **State reconciliation**    | Convert changing railway observations into a coherent current state.      |
| **Connection Risk Engine**  | Evaluate current transfer feasibility.                                    |
| **Risk explanation**        | Explain why a risk state was assigned.                                    |
| **Transfer margin**         | Estimate currently usable transfer time.                                  |
| **Alternative discovery**   | Identify a limited number of reasonable railway alternatives.             |
| **Outcome comparison**      | Compare expected destination impact across actions.                       |
| **Decision recommendation** | Recommend continuing, waiting, or rerouting when justified.               |
| **Data freshness**          | Expose how current the underlying information is.                         |
| **Unknown-state handling**  | Explicitly represent insufficient information.                            |
| **Responsive interface**    | Work effectively on a mobile browser.                                     |
| **Historical observations** | Preserve legally permitted observations for analysis and later modelling. |
| **Observability**           | Detect data-pipeline and application failures.                            |

---

# MVP Decision Loop

A minimal useful implementation can follow:

```text
1. Load journey
        ↓
2. Identify transfers
        ↓
3. Receive realtime updates
        ↓
4. Reconstruct current railway state
        ↓
5. Re-evaluate transfer feasibility
        ↓
6. Generate reasonable alternatives
        ↓
7. Estimate destination outcomes
        ↓
8. Compare options
        ↓
9. Explain preferred action
        ↓
10. Repeat when state changes
```

The continuous repetition of this loop is central to the product.

---

# MVP User Experience

The initial product can remain small.

## Journey Search

Choose a supported journey.

## Journey Monitor

Show the current journey state and highlight transfers requiring attention.

## Decision Detail

Answer:

```text
What changed?
What is the current risk?
What should I do?
Why?
What happens if I continue?
What happens if I change?
How fresh is this information?
```

## Alternatives

Compare a small number of reasonable actions rather than displaying an overwhelming route catalogue.

---

# Decision-First UX

A passenger under disruption should see the decision before technical metadata.

A concept screen might look like:

```text
Frankfurt Hbf connection

HIGH RISK

Recommended:
Change earlier at Mannheim

Expected arrival:
18:37

Continue current journey:
19:04

Why?
Frankfurt transfer margin is now
estimated at 2–4 min.

Transfer requirement:
6–8 min.

Last realtime update:
18 seconds ago
```

The intended hierarchy is:

```text
Recommended action
       ↓
Destination impact
       ↓
Risk
       ↓
Reason
       ↓
Alternative comparison
       ↓
Technical details
```

not:

```text
Provider payload
       ↓
Train metadata
       ↓
Charts
       ↓
Raw statistics
       ↓
Passenger interpretation
```

---

# Data Architecture

External transport-provider formats must not define the internal application.

The intended boundary is:

```text
External railway provider
          ↓
Provider adapter
          ↓
Normalization
          ↓
Canonical railway model
          ↓
Current journey state
          ↓
Risk engine
          ↓
Alternative evaluation
          ↓
Decision engine
          ↓
API / UI
```

Provider-specific payloads should remain isolated behind adapters.

Core decision logic should operate on AnschlussPilot domain types.

---

# Canonical Domain Model

The internal model should eventually be able to represent concepts such as:

```text
Station
Journey
Journey Leg
Connection
Service Run
Stop
Scheduled Stop Event
Realtime Observation
Current Service State
Disruption
Alternative Journey
Risk Assessment
Decision Candidate
Decision Recommendation
Outcome Estimate
```

Exact implementation details should follow actual requirements rather than speculative architecture.

---

# Realtime State Reconciliation

Realtime railway information changes over time.

For example:

```text
15:01  delay +3
15:04  delay +5
15:06  platform changed
15:08  delay +9
15:10  corrected to +6
15:14  partial cancellation
```

AnschlussPilot should conceptually distinguish:

```text
Scheduled state
Realtime observations
Current interpreted state
Historical observations
```

Realtime processing must account for:

- duplicate events;
- out-of-order events;
- corrections;
- stale observations;
- incomplete updates;
- contradictory information.

Destructive overwrite of each new event is not sufficient for a system that later needs explanation, reproducibility, or statistical analysis.

---

# Service Identity

A displayed train number must not automatically be treated as the unique identity of a train run.

Railway identity may be affected by:

- service date;
- provider journey identifiers;
- changed train numbers;
- split services;
- joined services;
- changed stopping patterns;
- operational replacements.

Incorrect identity reconstruction can corrupt:

```text
journey state
historical observations
risk labels
alternative routing
machine-learning datasets
```

Service identity is therefore a core engineering problem rather than a data-cleaning afterthought.

---

# Temporal Correctness

Railway data is inherently temporal.

The system should distinguish when appropriate:

```text
scheduled timestamp
realtime event timestamp
provider observation timestamp
ingestion timestamp
decision timestamp
```

Cross-midnight journeys, timezones, daylight-saving changes, late updates, and future information must be handled deliberately.

Most importantly:

> **A historical decision must only use information that was actually available at that decision time.**

This principle is essential both for realistic backtesting and for preventing machine-learning leakage.

---

# Data Freshness

Freshness must be explicit.

The system should distinguish between:

```text
LIVE
STALE
UNAVAILABLE
```

or equivalent states.

If realtime data fails, AnschlussPilot should prefer:

```text
Realtime information unavailable

Last successful update:
18:42
```

over silently presenting old information as current.

---

# Historical Data as a Product Asset

The long-term value of AnschlussPilot may depend heavily on the quality of its historical observation pipeline.

A useful historical dataset may eventually allow the project to reconstruct:

> **What information was available at time $t$, what actions were possible, what recommendation would have been made, and what actually happened afterward?**

This creates the foundation for evaluating:

- connection-risk models;
- alternative-routing policies;
- warning lead time;
- destination-delay outcomes;
- decision quality;
- model calibration;
- policy improvement.

Historical railway data should therefore preserve temporal structure and provenance rather than merely final train delays.

Any collection, storage, redistribution, or model training must remain compatible with the applicable provider licences and terms.

---

# Deterministic Before Machine Learning

AnschlussPilot is **not an AI-first product**.

The initial risk and decision systems should be deterministic, explainable, and testable.

For example:

```text
IF transfer margin is strongly negative
AND original connection is not expected to wait
AND a validated earlier alternative produces
a materially better destination outcome
THEN recommend early rerouting
```

The exact rules should evolve from railway-domain requirements and evidence.

Machine learning should only replace or augment a rule when it can demonstrate a meaningful improvement.

---

# Machine Learning Direction

Once sufficient historical data exists, future models may estimate quantities such as:

$$
P(\text{miss connection}\mid X_t),
$$

arrival-delay distributions such as:

$$
P(T_{\mathrm{arrival}}\le t\mid X_t),
$$

or action-dependent outcomes such as:

$$
P(Y\mid X_t,a).
$$

The question is not whether a model produces impressive predictions.

The question is:

> **Does the model improve actual journey decisions compared with a simple deterministic baseline?**

---

# Statistical Validation

Probabilistic models must be evaluated as probabilistic models.

Relevant methods may include:

- temporal train/test splits;
- Brier score;
- log loss;
- calibration curves;
- reliability diagrams;
- confidence intervals;
- deterministic baseline comparison;
- subgroup analysis;
- distribution-shift monitoring;
- feature-leakage analysis.

If AnschlussPilot displays:

```text
80% missed-connection risk
```

then cases assigned approximately 80% probability should fail approximately that often over an appropriate evaluation population.

Otherwise a categorical state such as:

```text
HIGH RISK
```

may be more honest and more useful.

---

# Decision Evaluation

A connection predictor can be statistically accurate while still producing poor passenger decisions.

AnschlussPilot should eventually evaluate the **decision policy itself**.

Possible outcome measures include:

```text
destination arrival delay
probability of reaching destination
number of missed connections
warning lead time
unnecessary rerouting
additional transfers
decision reversals
unknown-state frequency
```

The best model is not necessarily the one with the best standalone prediction metric.

The best system is the one that produces better journey outcomes.

---

# Avoiding False Interventions

AnschlussPilot should not reroute aggressively merely because a connection looks risky.

A premature reroute may itself produce a worse journey.

The system must account for the cost of:

```text
false warnings
unnecessary transfers
longer routes
unstable recommendations
rapidly changing advice
```

A recommendation should change only when new information materially changes the preferred action.

Future work may need mechanisms such as:

```text
decision hysteresis
minimum improvement thresholds
confidence requirements
recommendation stability
```

to avoid repeatedly telling passengers to change their plan.

---

# Uncertainty-Aware UX

The system must distinguish between:

- scheduled facts;
- realtime observations;
- estimates;
- inferred state;
- model predictions;
- unavailable information.

For example:

```text
Estimated transfer requirement:
4–6 min
```

is preferable to:

```text
Transfer time:
5 min 14 sec
```

when such precision cannot be justified.

Likewise:

```text
Current evidence suggests this connection
is unlikely to remain feasible.
```

is different from:

```text
You will miss this train.
```

Predictions are not guarantees.

---

# Alternative Recommendations

Alternative recommendations may compare:

- continue current journey;
- wait for a delayed outgoing train;
- change at an earlier station;
- take a later connection;
- use another supported railway routing.

The MVP should keep the candidate set deliberately limited.

The system may say:

> **Based on currently available railway information, this appears to be the better journey option.**

It must not automatically say:

> **You are legally entitled to board this service.**

Operational routing and legal entitlement are separate domains.

---

# Privacy by Design

The core MVP should require as little personal information as possible.

Where feasible, journey monitoring should work without collecting:

- names;
- email addresses;
- Deutsche Bahn credentials;
- uploaded tickets;
- payment information;
- continuous precise GPS history.

Accounts, notifications, personalization, and cross-device synchronization should only be introduced when their product value justifies the additional security and GDPR surface.

---

# Legal, Licensing, and Independence

AnschlussPilot is intended to be an **independent product**.

It must not imply official Deutsche Bahn affiliation unless such a relationship actually exists.

Before integrating any railway data source, the project must verify the current:

- API terms;
- licence;
- attribution requirements;
- storage rights;
- caching restrictions;
- redistribution permissions;
- permitted model-training use;
- commercial-use conditions.

Availability of an API does not automatically imply permission for every downstream use.

---

# Passenger Rights

Journey recommendations and legal passenger rights must remain separate.

Passenger-rights functionality may eventually require:

```text
versioned legal rules
effective dates
validated domain logic
jurisdiction awareness
legal review
```

An LLM must not become the authoritative source for ticket validity, compensation entitlement, or boarding rights.

This is outside the initial MVP.

---

# Non-Goals

The initial AnschlussPilot product will **not** attempt to:

1. replace DB Navigator;
2. become another generic train-delay tracker;
3. exist primarily as a connection-reliability score website;
4. become a full German public-transport application;
5. sell tickets;
6. process payments;
7. manage reservations;
8. sign in to Deutsche Bahn accounts;
9. import private tickets by default;
10. automatically submit compensation claims;
11. provide guaranteed passenger-rights decisions;
12. guarantee that a passenger will make or miss a connection;
13. provide indoor turn-by-turn station navigation;
14. support all European railways from the beginning;
15. become a universal rail + air + coach + taxi journey planner;
16. add an LLM chatbot merely to appear AI-driven;
17. deploy ML models without proving improvement over a baseline;
18. expose uncalibrated probabilities as trustworthy numbers;
19. collect personal information unnecessary for the core use case;
20. present stale data as live;
21. manufacture certainty when data is insufficient;
22. confuse operational advice with legal entitlement.

These are deliberate product boundaries.

---

# Engineering Priorities

The initial engineering order should approximately be:

```text
Railway Domain Model
        ↓
Provider Boundary
        ↓
Realtime Data Ingestion
        ↓
Service Identity
        ↓
State Reconciliation
        ↓
Journey State
        ↓
Deterministic Risk Engine
        ↓
Alternative Evaluation
        ↓
Decision Engine
        ↓
Mobile Decision UX
        ↓
Historical Observation Pipeline
        ↓
Backtesting
        ↓
Statistical Modelling
        ↓
Machine Learning
```

Cross-cutting concerns include:

```text
Testing
Observability
DevOps / SRE
Security
Privacy
Licensing
```

This order is intentional.

Machine learning is not the foundation of AnschlussPilot.

The foundation is:

> **Reliable railway data + correct temporal state + a trustworthy railway domain model + verifiable journey decisions.**

---

# Quality Assurance

Railway applications must be tested against difficult operational cases.

Relevant scenarios include:

- normal transfer;
- shrinking transfer margin;
- incoming delay;
- outgoing delay;
- delay correction;
- platform change;
- cancellation;
- partial cancellation;
- changed stop pattern;
- changed train number;
- train splitting or joining;
- duplicate realtime events;
- out-of-order events;
- stale information;
- provider timeout;
- missing realtime information;
- same-name stations;
- cross-midnight journeys;
- terminated services;
- earlier rerouting opportunities.

Domain edge cases should become reproducible tests rather than production surprises.

---

# Observability

If AnschlussPilot becomes publicly available, system reliability becomes part of the product.

Operational monitoring should eventually include:

```text
provider availability
provider latency
data freshness
collector failures
normalization failures
state-reconciliation failures
alternative-generation failures
risk-engine failures
decision-engine failures
API latency
database health
application availability
```

A product designed to compensate for unreliable travel conditions must itself fail transparently.

---

# Product Metrics

Success should not be measured primarily through page views.

More meaningful measures may include:

- useful warning lead time;
- missed-connection detection;
- false-warning rate;
- unnecessary rerouting rate;
- recommendation stability;
- alternative quality;
- expected versus actual destination delay;
- prediction calibration;
- decision-policy improvement over baseline;
- unknown-state frequency;
- realtime-data freshness.

Ultimately, the product should be evaluated on whether it helps passengers make better decisions.

---

# Product Moat

AnschlussPilot should not assume that a machine-learning model alone creates defensibility.

Potential long-term assets include:

```text
Canonical German railway domain model
                +
Reliable historical observation store
                +
Temporal journey reconstruction
                +
Connection-outcome labels
                +
Transfer behaviour models
                +
Realtime state reconciliation
                +
Calibrated prediction models
                +
Decision-policy evaluation
```

The most valuable data is not necessarily:

> How late was ICE 123?

It may instead be:

> **At time $t$, what information was available, what alternatives were possible, which action was preferable, and what journey outcome followed?**

That is the dataset needed to improve a real decision-support system.

---

# MVP Completion Criteria

The MVP should not be considered complete merely because the application runs.

## Railway Correctness

Given:

```text
A → B → C
```

the system must correctly represent:

```text
leg 1
connection at B
leg 2
```

and update the connection when relevant operational information changes.

## Temporal Correctness

Historical evaluation must not use information that became available only after the decision timestamp.

## Data Correctness

The system must distinguish:

```text
delay
delay correction
cancellation
partial cancellation
platform change
stale data
missing data
```

and preserve service identity correctly.

## Risk Correctness

Connection feasibility should respond coherently to changing railway state.

## Decision Usefulness

For a threatened journey, AnschlussPilot should answer:

```text
What changed?
Is the current plan still feasible?
Should I continue?
What alternative is better?
Why?
What is the expected destination impact?
```

## Reliability

Provider failure must not silently turn stale information into apparently live information.

## Explainability

A recommendation should be understandable from the structured factors that produced it.

---

# Post-MVP Direction

Only after the realtime railway and deterministic decision pipeline is reliable should the project expand.

A possible progression is:

```text
Reliable timetable integration
            ↓
Reliable realtime ingestion
            ↓
Canonical railway state
            ↓
Deterministic connection assessment
            ↓
Alternative evaluation
            ↓
Decision recommendations
            ↓
Historical observation dataset
            ↓
Backtesting framework
            ↓
Calibrated probabilistic models
            ↓
Action-dependent outcome models
            ↓
Personalized transfer preferences
            ↓
Notifications
            ↓
Passenger-rights assistance
            ↓
European expansion
```

Features such as:

- accounts;
- push notifications;
- GPS;
- ticket integration;
- compensation workflows;
- legal passenger-rights automation;
- European coverage;

should each be treated as substantial product decisions rather than minor extensions.

---

# Development

Concrete installation and development instructions should only be added after the corresponding implementation exists.

Future versions of this section may document verified:

```text
technology stack
runtime versions
dependency installation
environment variables
database setup
provider configuration
development commands
testing
linting
type checking
migrations
build process
deployment
monitoring
```

This README intentionally avoids inventing technical implementation details.

---

# Contributing

Changes should be evaluated against the core product question:

> **Does this improve the passenger's ability to make a better decision during a disrupted German rail journey?**

High-value contributions are likely to improve:

- railway-domain correctness;
- service identity;
- temporal data handling;
- provider normalization;
- state reconciliation;
- connection feasibility;
- alternative evaluation;
- destination-outcome comparison;
- decision stability;
- uncertainty handling;
- mobile UX;
- testing;
- operational reliability.

Feature count is not a project objective.

---

# Disclaimer

AnschlussPilot is intended as an independent railway journey decision-support project.

Railway schedules, realtime observations, estimates, predictions, comparisons, and recommendations may be:

- delayed;
- incomplete;
- unavailable;
- uncertain;
- incorrect.

AnschlussPilot must not be treated as:

- an official railway-operator service;
- a contractual transport guarantee;
- a guarantee that a connection will succeed;
- a guarantee that a suggested service may legally be boarded;
- authoritative fare advice;
- authoritative passenger-rights advice.

Critical travel decisions should be verified against appropriate official information.

---

# Vision

AnschlussPilot should not merely tell passengers that their journey is going wrong.

It should help them decide **what to do while there is still time to improve the outcome**.

> **Observe the disruption.
> Understand the risk.
> Compare the options.
> Act before the journey fails.**
