# **AGENTS.md**

This file defines the operating rules for AI coding agents and human contributors working on **AnschlussPilot**.

AnschlussPilot is a:

> **German rail connection-risk and journey-reliability decision-support platform**

The project is not a generic train-delay dashboard, a DB Navigator clone, or an AI-first demonstration.

Its core responsibility is to help a passenger answer:

1. **Can I still realistically make this connection?**  
2. **What is the likely impact on my complete journey?**  
3. **What is the next reasonable action if the planned connection fails?**

All implementation decisions should be evaluated against those questions.

---

# **1\. Source of Truth**

Before modifying the repository, inspect the actual repository state.

Do not infer implementation status from this file, the README, issue descriptions, comments, screenshots, previous conversations, or planned architecture.

The repository itself is the source of truth for what currently exists.

Before making a substantial change, read at minimum:

README.md  
AGENTS.md

and any relevant:

docs/  
architecture documentation  
product requirements  
source files  
tests  
configuration  
database migrations  
provider adapters  
CI workflows

that relate to the task.

If the repository later contains a dedicated product requirements document, architecture decision records, schemas, or provider documentation, read those before changing the corresponding subsystem.

---

# **2\. Never Invent Repository Capabilities**

Never claim that something exists unless it is verifiably present in the repository or actually confirmed by a configured external system.

Do not fabricate:

* implemented features;  
* supported railway providers;  
* API capabilities;  
* realtime-data availability;  
* timetable coverage;  
* platform-change support;  
* cancellation support;  
* deployment infrastructure;  
* production readiness;  
* test results;  
* benchmark results;  
* model performance;  
* prediction accuracy;  
* model calibration;  
* database contents;  
* historical datasets;  
* monitoring coverage;  
* security guarantees;  
* GDPR compliance;  
* licensing permissions;  
* passenger-rights support.

If something is planned but not implemented, describe it as planned.

If something cannot be verified, say that it cannot be verified.

---

# **3\. Product Invariants**

The following principles are architectural constraints, not marketing slogans.

## **3.1 Journey outcome over train delay**

AnschlussPilot should reason about the passenger's **journey outcome**, not merely display individual train delays.

A delay is an input.

The product output should concern questions such as:

Is the transfer still feasible?  
How much usable transfer margin remains?  
What happens to destination arrival?  
What should the passenger do next?

Do not turn the project into a general-purpose train-status application unless the product scope is explicitly changed.

---

## **3.2 Decision support over raw data**

Prefer:

HIGH RISK

Estimated remaining transfer buffer:  
0–2 min

Reason:  
Incoming arrival has moved later and the transfer requires approximately 5–7 min.

Alternative:  
Next reasonable connection ...

over interfaces that primarily expose:

raw API fields  
train metadata  
large tables  
technical charts  
provider-specific status codes

Technical details may exist, especially for debugging or observability, but passenger-facing UX should remain decision-first.

---

## **3.3 Uncertainty must remain visible**

Never convert insufficient information into artificial certainty.

`Unknown` is a valid and necessary product state.

The system must be able to represent:

SAFE  
ATTENTION  
HIGH\_RISK  
MISSED\_OR\_UNAVAILABLE  
UNKNOWN

or semantically equivalent domain values.

Do not silently map missing or stale data to `SAFE`.

Do not fabricate a probability when the system only supports a deterministic classification.

Do not display false precision.

Prefer:

Estimated transfer requirement: 4–6 min

over:

Transfer requirement: 5 min 14 sec

unless such precision is actually justified.

---

## **3.4 Data quality before model complexity**

A more sophisticated model does not compensate for incorrect railway identity, broken event ordering, stale data, leakage, or an invalid target.

Prioritize:

correct domain modelling  
correct service identity  
correct event ingestion  
correct temporal ordering  
correct state reconciliation  
correct freshness handling  
correct labels

before introducing additional machine-learning complexity.

---

## **3.5 German rail first**

Do not expand scope merely because an abstraction makes expansion technically possible.

The product should first solve one high-quality German rail connection-risk use case.

European expansion, multimodal routing, aviation, coaches, taxis, and unrelated mobility features are post-MVP concerns.

---

# **4\. MVP Boundary**

The intended MVP is:

> Given a supported German rail itinerary containing at least one transfer, AnschlussPilot evaluates each connection as railway conditions change, explains its current risk, and presents a reasonable alternative when the original connection becomes unsafe or impossible.

Features should normally support at least one of:

connection feasibility  
journey outcome  
risk explanation  
alternative decision support  
data reliability

If a proposed feature does not materially improve one of these areas, question whether it belongs in the MVP.

---

# **5\. Explicit Non-Goals**

Unless the product requirements are intentionally changed, do not implement AnschlussPilot as:

* a DB Navigator replacement;  
* a complete German public-transport application;  
* a ticket shop;  
* a payment system;  
* a reservation platform;  
* a Deutsche Bahn account client;  
* a compensation-claim service;  
* a legal passenger-rights authority;  
* a guaranteed connection predictor;  
* an indoor turn-by-turn station navigator;  
* a universal European railway planner;  
* a flight \+ rail \+ bus \+ taxi multimodal platform;  
* an LLM chatbot whose purpose is merely to make the project appear AI-driven.

Do not introduce these capabilities indirectly without updating the corresponding product documentation.

---

# **6\. Railway Domain Correctness**

Railway domain behaviour must be treated as a first-class engineering concern.

Do not assume that:

train number \== unique train run  
scheduled connection \== feasible connection  
delay \== only relevant disruption  
arrival/departure times only move forward  
realtime updates arrive in order  
provider data is internally consistent  
same-name stations are interchangeable

Design for domain situations such as:

* delay changes;  
* delay corrections;  
* platform changes;  
* full cancellations;  
* partial cancellations;  
* changed stopping patterns;  
* train splitting;  
* train joining;  
* through services;  
* train-number changes;  
* journeys crossing midnight;  
* service termination;  
* replacement transport;  
* duplicate events;  
* out-of-order events;  
* missing realtime data;  
* stale realtime data;  
* conflicting observations.

Do not implement a domain shortcut merely because it works for a demo itinerary.

---

# **7\. Connection Feasibility**

A connection should not be reduced to:

incoming\_delay \> scheduled\_transfer\_time

A useful conceptual model is:

# **$$**

# **B\_{\\mathrm{effective}}**

## **T\_{\\mathrm{departure,next}}**

## **T\_{\\mathrm{arrival,current}}**

## **T\_{\\mathrm{transfer}}**

T\_{\\mathrm{safety}}.  
$$

This formula is conceptual.

Do not assume it completely defines connection feasibility.

The implementation may also need to consider:

* cancellation state;  
* platform changes;  
* outgoing-service delay;  
* changed stop patterns;  
* transfer-station topology;  
* unavailable information;  
* provider confidence;  
* transfer-time ranges;  
* other domain conditions.

Keep the risk engine explainable.

If a rule changes the user's risk state, the system should ideally be capable of explaining the relevant factors.

---

# **8\. Risk State Semantics**

Risk states must have stable domain meanings.

A recommended conceptual vocabulary is:

| State | Meaning |
| ----- | ----- |
| `SAFE` | Current information indicates a reasonable buffer. |
| `ATTENTION` | The available margin is shrinking or becoming uncertain. |
| `HIGH_RISK` | Missing the connection is a material possibility. |
| `MISSED_OR_UNAVAILABLE` | The planned connection is no longer feasible according to current information. |
| `UNKNOWN` | Available information is insufficient for reliable assessment. |

Avoid encoding presentation text directly into core domain logic.

Prefer stable domain enums or equivalent typed representations.

Frontend wording may later be localized independently.

---

# **9\. External Provider Boundary**

Never let raw external-provider payloads become the AnschlussPilot domain model.

Maintain an explicit boundary:

External provider  
      ↓  
Provider adapter  
      ↓  
Normalization  
      ↓  
AnschlussPilot domain model  
      ↓  
Journey / Risk engine  
      ↓  
Application API / UI

Provider-specific fields should be translated into canonical internal concepts before they reach core journey logic.

Benefits include:

* provider independence;  
* easier testing;  
* controlled schema evolution;  
* explicit missing-data semantics;  
* easier addition of future providers;  
* reduced coupling between infrastructure and domain logic.

Do not spread provider-specific JSON parsing throughout the backend.

---

# **10\. Canonical Domain Model**

The internal model should be designed around railway and journey concepts rather than API response shapes.

Likely concepts include:

Station  
Journey  
Service / Train Run  
Stop  
Scheduled Stop Event  
Realtime Observation  
Journey Leg  
Connection  
Disruption  
Alternative  
Risk Assessment

Do not add abstractions merely because they sound architecturally sophisticated.

Add them when the repository has a concrete domain requirement.

When modifying domain types:

1. inspect all consumers;  
2. inspect persistence implications;  
3. inspect serialization/API implications;  
4. update tests;  
5. update documentation when semantics change.

---

# **11\. Service Identity**

Service identity is a critical correctness problem.

Do not assume that a displayed train number uniquely identifies a train run.

The system may eventually need identity based on a combination of provider-defined journey identifiers, service date, route information, stop sequence, or other stable attributes.

Never merge services solely because they share a train number.

Never split one service solely because an operational field changed.

Identity logic must have tests.

---

# **12\. Realtime Observations and State Reconciliation**

Do not treat realtime ingestion as a sequence of destructive overwrites.

Conceptually distinguish:

Scheduled truth  
Realtime observations  
Current interpreted state  
Historical observations

For example:

15:01  delay \+3  
15:04  delay \+5  
15:06  platform changed  
15:08  delay \+9  
15:10  corrected to \+6  
15:14  partial cancellation

The current state may be derived from these observations, but historical information can remain important for:

* debugging;  
* auditability;  
* delay evolution;  
* model training;  
* data-quality analysis;  
* reproducibility.

Do not discard historical observations without a deliberate retention decision.

---

# **13\. Temporal Correctness**

Railway data is temporal data.

Be explicit about:

* service date;  
* event timestamp;  
* scheduled timestamp;  
* provider observation timestamp;  
* ingestion timestamp;  
* timezone;  
* daylight-saving transitions;  
* stale thresholds.

Avoid using naive datetimes when timezone-aware values are required.

Do not assume all relevant times belong to the same calendar date.

Cross-midnight journeys must be tested.

---

# **14\. Data Freshness**

Freshness must be represented explicitly.

Do not present stale information as live merely because it is the newest value available in the database.

Where relevant, retain:

observed\_at  
received\_at  
last\_successful\_update  
provider status  
freshness / stale state

or equivalent information.

User-facing behaviour should be capable of distinguishing:

Realtime data available  
Realtime data stale  
Realtime data unavailable

A provider failure should degrade gracefully.

---

# **15\. Data Provenance**

Whenever practical, normalized information should retain enough provenance to determine:

* where it came from;  
* when it was observed;  
* which provider supplied it;  
* how it was normalized;  
* whether it was inferred.

Do not silently convert inferred information into provider-reported fact.

This distinction is especially important for risk explanations and later statistical modelling.

---

# **16\. Historical Data**

Historical railway observations may become a major project asset.

If historical collection is implemented, design it deliberately.

Consider:

* deduplication;  
* event identity;  
* immutable versus mutable fields;  
* storage volume;  
* retention;  
* licensing restrictions;  
* reproducibility;  
* schema evolution;  
* data backfills;  
* incomplete observations.

Do not retain provider data simply because storage is technically possible.

Verify that retention and downstream use are permitted.

---

# **17\. Machine Learning Policy**

Machine learning is post-baseline.

Do not introduce ML merely to make AnschlussPilot look more advanced.

Before adding a model, require:

1. a clearly defined target;  
2. a meaningful deterministic baseline;  
3. sufficient historical data;  
4. temporal validation;  
5. leakage analysis;  
6. appropriate metrics;  
7. documented failure modes;  
8. evidence that the model improves the relevant product decision.

The central future modelling problem may include:

$$  
P(\\text{miss connection}\\mid X).  
$$

A model that predicts delay accurately but does not improve connection decisions may not provide useful product value.

---

# **18\. Statistical Validation**

Probability quality matters.

Do not evaluate probabilistic risk models using classification accuracy alone.

Where appropriate, evaluate:

* Brier score;  
* log loss;  
* calibration curves;  
* reliability diagrams;  
* temporal holdout performance;  
* baseline comparison;  
* confidence intervals;  
* subgroup behaviour;  
* distribution shift.

If the UI shows:

80% missed-connection risk

the probability should have defensible calibration.

Otherwise use categorical risk states instead.

---

# **19\. Feature Leakage**

Railway prediction systems are highly susceptible to temporal leakage.

Do not use information that would not have been available at the prediction timestamp.

Examples of potential leakage include:

* final arrival delay;  
* future cancellation information;  
* later platform updates;  
* downstream events observed after prediction time;  
* labels accidentally joined back into features.

Train/test splitting should normally respect time.

Random row-level splitting may produce misleadingly optimistic results.

---

# **20\. Frontend and UX**

AnschlussPilot is intended for passengers who may be:

* standing on a platform;  
* moving between platforms;  
* under time pressure;  
* using one hand;  
* on a small screen;  
* dealing with unreliable connectivity.

Design accordingly.

Prioritize:

decision  
risk  
reason  
alternative  
arrival impact

before technical detail.

Do not overload primary screens with internal railway metadata.

Responsive web or PWA behaviour is preferred for an initial product unless the repository intentionally adopts another strategy.

---

# **21\. Accessibility**

Passenger-facing interfaces should aim for accessible semantics.

Do not communicate risk exclusively through colour.

Use text labels such as:

Safe  
Attention  
High Risk  
Unavailable  
Unknown

Ensure interactive controls remain keyboard-accessible where applicable.

Prefer semantic HTML.

Accessibility regressions should be treated as product defects.

---

# **22\. Localization**

Repository-level engineering documentation may use English.

Passenger-facing German should be treated as genuine localization rather than literal translation.

Relevant railway terminology includes:

Anschluss  
Umstieg  
Verspätung  
Zugausfall  
Gleisänderung  
voraussichtliche Ankunft

Do not invent German railway terminology when established wording exists.

Do not mix localization strings deeply into domain logic.

Use localization boundaries when the frontend supports multiple languages.

---

# **23\. Privacy**

Default to collecting less data.

The MVP should not require personal accounts unless they are needed for a concrete capability.

Avoid unnecessary collection of:

* names;  
* email addresses;  
* Deutsche Bahn credentials;  
* tickets;  
* payment details;  
* continuous precise GPS;  
* long-term journey histories tied to identity.

If a feature introduces personal data, explicitly consider:

purpose  
retention  
access  
deletion  
security  
legal basis  
data minimization

Do not casually add analytics identifiers or persistent tracking.

---

# **24\. Security**

Do not commit secrets.

Never place real credentials in:

source code  
tests  
examples  
README.md  
AGENTS.md  
tracked .env files  
fixtures  
logs

Use environment variables or appropriate secret management.

If authentication is added, implement authorization explicitly.

Do not assume that hiding a frontend control prevents backend access.

Validate external input.

Treat provider payloads as untrusted input.

Keep dependencies reasonably current and review security-sensitive changes carefully.

---

# **25\. Legal and Licensing Boundaries**

Do not assume that public accessibility of railway information implies permission to:

* scrape it;  
* store it indefinitely;  
* redistribute it;  
* train models on it;  
* commercialize it;  
* remove attribution.

Before implementing an external provider integration, verify the actual current licence and API terms.

Document important restrictions near the provider implementation.

Do not claim that AnschlussPilot is affiliated with Deutsche Bahn or another operator unless that relationship actually exists.

---

# **26\. Passenger Rights**

Operational route advice and legal entitlement are different domains.

The application may eventually say:

Based on currently available timetable information,  
this is the next reasonable connection.

It must not infer without validated legal logic:

You are legally entitled to board this service.

Passenger-rights functionality should be implemented as a separately validated domain with versioned legal rules and effective dates.

Do not delegate authoritative legal decisions to an LLM.

---

# **27\. Testing Strategy**

Tests should emphasize domain correctness, not only happy paths.

At minimum, relevant subsystems should eventually cover cases such as:

normal connection  
shrinking transfer buffer  
incoming delay correction  
outgoing train delay  
platform change  
full cancellation  
partial cancellation  
duplicate observation  
out-of-order observation  
cross-midnight journey  
provider timeout  
stale realtime data  
missing realtime data  
changed train number  
changed stopping pattern  
same-name stations  
service termination

Prefer deterministic fixtures.

Avoid tests that depend unnecessarily on live external APIs.

External-provider integration tests should be separated from pure domain tests.

---

# **28\. Test the Domain Core Independently**

Core risk logic should be testable without:

* a browser;  
* a database when not required;  
* a network connection;  
* a live railway provider;  
* production credentials.

Prefer pure or mostly pure domain functions where appropriate.

This makes behaviour easier to verify and protects the project from provider instability.

---

# **29\. Provider Contract Tests**

When provider adapters exist, test normalization boundaries.

Verify that provider representations are converted correctly into canonical domain values.

Test at least:

valid response  
missing optional fields  
unknown enum/status values  
malformed timestamps  
duplicate events  
cancelled services  
platform changes  
unexpected but syntactically valid payloads

Do not let unknown provider values crash the entire journey pipeline when graceful handling is possible.

---

# **30\. Error Handling**

Do not swallow errors silently.

Differentiate where useful between:

provider unavailable  
provider rejected request  
invalid provider response  
normal missing data  
unsupported journey  
internal processing failure  
database failure

User-facing errors should remain understandable.

Internal logs should preserve enough technical context for diagnosis without leaking secrets or unnecessary personal data.

---

# **31\. Observability**

When operational components exist, instrument meaningful boundaries.

Potential metrics include:

provider request success rate  
provider latency  
data freshness  
collector failures  
normalization failures  
risk-evaluation failures  
API latency  
error rate  
queue backlog  
database health

Do not add metrics merely because they are easy to count.

Prefer metrics that can identify user-impacting failures.

---

# **32\. Logging**

Use structured logging where the stack supports it.

Avoid logging:

* credentials;  
* tokens;  
* private ticket data;  
* unnecessary location history;  
* full sensitive payloads.

Include correlation identifiers when useful for tracing a journey or ingestion pipeline.

Do not make logs the only source of important state.

---

# **33\. API Design**

If an application API exists, expose AnschlussPilot domain concepts rather than raw provider payloads.

Prefer stable internal representations.

Do not expose an external provider's schema as the public API unless there is a deliberate reason.

When changing API semantics:

1. inspect consumers;  
2. update validation;  
3. update tests;  
4. update examples;  
5. update documentation.

---

# **34\. Validation and Types**

Use schema validation at system boundaries where the language and stack support it.

Validate:

* external provider data;  
* API requests;  
* environment configuration;  
* persisted structured data when appropriate.

Prefer explicit types for domain concepts over unstructured dictionaries or loosely typed maps.

Avoid "stringly typed" railway states where stable enums or discriminated unions would be clearer.

---

# **35\. Database Changes**

Do not modify persistent schemas casually.

For schema changes:

* inspect existing migrations;  
* preserve migration order;  
* avoid rewriting already-applied migrations unless repository policy explicitly permits it;  
* consider backward compatibility;  
* consider existing data;  
* update tests.

If historical observations are stored, protect temporal and identity semantics during migrations.

---

# **36\. Dependencies**

Before adding a dependency, ask:

What problem does it solve?  
Can the existing stack solve it?  
What maintenance burden does it introduce?  
What security surface does it add?  
Is the licence compatible?

Avoid adding large frameworks for small utilities.

Do not introduce a second library that duplicates an established repository abstraction without a strong reason.

---

# **37\. Architecture Changes**

Avoid speculative architecture.

Do not introduce:

* microservices;  
* event sourcing;  
* Kafka;  
* Kubernetes;  
* GraphQL;  
* vector databases;  
* LLM infrastructure;  
* complex workflow engines;

merely because AnschlussPilot might need them one day.

Use the simplest architecture that correctly supports current requirements.

Architecture should evolve from observed requirements, not résumé-driven design.

---

# **38\. Performance**

Optimize passenger-critical paths first.

Potentially important latency includes:

journey lookup  
realtime refresh  
risk recomputation  
alternative retrieval  
initial mobile render

Do not optimize hypothetical bottlenecks before measuring them.

Correctness and clarity come before premature optimization.

---

# **39\. Resilience**

External railway providers are expected to fail occasionally.

Design graceful degradation.

A provider failure should not automatically:

* crash the frontend;  
* erase the last known state;  
* classify a connection as safe;  
* present stale information as current.

Where appropriate, retain the last successful observation together with its timestamp and freshness state.

---

# **40\. Documentation**

Documentation must describe the repository that actually exists.

When implementation changes materially affect:

* setup;  
* architecture;  
* configuration;  
* supported providers;  
* risk semantics;  
* data model;  
* testing;  
* deployment;

update the relevant documentation in the same change.

Do not add future installation instructions before the corresponding implementation exists.

Do not leave README claims stale after removing a feature.

---

# **41\. Comments**

Use comments for:

* domain reasoning;  
* railway-specific edge cases;  
* non-obvious invariants;  
* provider quirks;  
* legal or licensing constraints when relevant.

Do not use comments to restate obvious syntax.

If a strange implementation exists because of a railway-domain rule, explain the rule.

---

# **42\. Naming**

Prefer names from the actual domain.

Good examples:

Connection  
JourneyLeg  
RealtimeObservation  
TransferBuffer  
RiskAssessment  
ServiceRun  
ScheduledStopEvent

Avoid vague names such as:

DataManager  
Helper  
Thing  
Processor2  
UtilsService

unless their responsibility is genuinely generic.

---

# **43\. Change Discipline**

Keep changes focused.

Do not combine unrelated:

feature work  
refactoring  
dependency upgrades  
formatting changes  
schema redesign

unless the task genuinely requires them together.

Avoid repository-wide rewrites for a local problem.

Preserve existing conventions unless there is a strong reason to change them.

---

# **44\. Refactoring**

Refactor when it improves:

* correctness;  
* comprehensibility;  
* testability;  
* domain boundaries;  
* maintainability.

Do not refactor solely to impose a preferred style.

Before major refactoring, understand existing behaviour and tests.

Preserve external behaviour unless the task explicitly changes it.

---

# **45\. AI / LLM Usage**

AnschlussPilot must not use an LLM where deterministic logic is more appropriate.

LLMs must not become the authoritative mechanism for:

* realtime railway state;  
* timetable facts;  
* connection feasibility arithmetic;  
* legal entitlement;  
* fare validity;  
* data normalization;  
* safety-critical operational facts.

If LLM functionality is introduced later, its role should be clearly bounded.

Structured domain data should remain authoritative.

---

# **46\. Do Not Hide Uncertainty Behind AI**

Never use generative text to make incomplete railway information sound confident.

If structured data says:

UNKNOWN

the generated explanation must remain consistent with that state.

An LLM must never upgrade:

unknown  
estimated  
unverified

into:

confirmed  
guaranteed  
certain

---

# **47\. Product Analytics**

If analytics are introduced, measure product usefulness rather than vanity metrics.

Potentially meaningful metrics include:

warning lead time  
false warning rate  
connection outcome accuracy  
calibration  
alternative recommendation usefulness  
unknown-state rate  
data freshness  
recommendation adoption

Page views alone do not establish that AnschlussPilot helps passengers.

Analytics collection must also respect privacy requirements.

---

# **48\. Definition of Done**

A change is not complete merely because code has been written.

For a substantial change, verify as applicable:

* implementation matches the requested scope;  
* existing repository conventions are respected;  
* relevant tests were added or updated;  
* relevant tests pass;  
* static checks pass;  
* type checks pass;  
* formatting/lint checks pass;  
* error states were considered;  
* stale/missing data behaviour was considered;  
* domain edge cases were considered;  
* documentation was updated;  
* no unsupported capability claims were introduced;  
* no secrets were committed;  
* no licensing assumptions were silently introduced.

If a check cannot be run, state that explicitly.

Never report a check as passing unless it was actually executed successfully.

---

# **49\. Required Agent Workflow**

For every non-trivial task, follow this sequence.

## **Step 1 — Inspect**

Read the relevant code, tests, configuration, and documentation.

Do not begin by assuming the architecture.

## **Step 2 — Identify the domain boundary**

Determine which part of the system owns the behaviour:

provider adapter?  
normalization?  
domain model?  
risk engine?  
API?  
frontend?  
persistence?

Fix behaviour at the correct layer.

## **Step 3 — Preserve product scope**

Confirm that the task supports AnschlussPilot's current product boundary.

Do not expand into unrelated transport functionality accidentally.

## **Step 4 — Implement the smallest coherent change**

Prefer a complete narrow solution over a broad partial redesign.

## **Step 5 — Test**

Run the smallest relevant tests first.

Then run broader checks when practical.

## **Step 6 — Review failure cases**

Explicitly consider:

missing data  
stale data  
duplicate data  
out-of-order data  
provider failure  
unknown domain values  
cross-midnight time

when relevant.

## **Step 7 — Review documentation**

Update documentation when behaviour, setup, architecture, or capability claims changed.

## **Step 8 — Report truthfully**

Summarize:

what changed  
why  
what was tested  
what was not tested  
remaining limitations

Never fabricate validation.

---

# **50\. When Requirements Are Ambiguous**

Prefer existing repository behaviour and documented product principles.

Do not invent a large new subsystem to resolve a minor ambiguity.

When multiple interpretations are possible, choose the one that:

1. preserves current functionality;  
2. keeps the MVP narrow;  
3. preserves railway-domain correctness;  
4. keeps uncertainty explicit;  
5. minimizes irreversible architectural decisions.

Document material assumptions.

---

# **51\. Repository Evolution**

This file should evolve with the project.

When AnschlussPilot gains actual:

* providers;  
* backend architecture;  
* database schemas;  
* frontend framework;  
* deployment infrastructure;  
* ML pipelines;  
* production monitoring;

replace generic guidance with repository-specific commands and invariants.

For example, future versions of this file should eventually include verified commands such as:

install dependencies  
start development environment  
run unit tests  
run integration tests  
run type checks  
run lint  
run migrations  
build production artifacts

Do not add placeholder commands that do not work.

---

# **52\. Final Engineering Principle**

When uncertain between a clever implementation and a trustworthy one, prefer the trustworthy one.

The hierarchy for AnschlussPilot is:

Railway-domain correctness  
          ↓  
Data correctness  
          ↓  
Decision usefulness  
          ↓  
Reliability  
          ↓  
Explainability  
          ↓  
Model sophistication

A sophisticated system that confidently gives the wrong railway advice is worse than a simple system that correctly says:

> **Unknown — realtime information is insufficient to assess this connection reliably.**

