# **AnschlussPilot**

**German Rail Connection-Risk & Journey-Reliability Decision-Support Platform**

> From railway disruption data to an actionable travel decision.

AnschlussPilot is a project focused on one difficult question in German rail travel:

> **Is my planned connection still realistically achievable — and if not, what should I do next?**

Railway applications are good at showing timetables, delays, cancellations, and platform information. But a passenger making a transfer does not merely need another delay number.

They need a decision.

AnschlussPilot is designed to transform timetable and realtime railway information into an assessment of **connection feasibility**, explain why a transfer is becoming risky, and identify a reasonable alternative when the original journey can no longer be completed as planned.

The objective is not simply:

$$  
\\text{minimum scheduled travel time}  
$$

but rather:

$$  
\\text{reliable arrival at destination}.  
$$

---

## **Status**

> \[\!IMPORTANT\]  
> **AnschlussPilot is currently in the product-definition / pre-MVP stage.**

This README describes the intended product, architecture boundaries, engineering principles, and MVP scope.

It does **not** imply that:

* realtime railway integrations are already implemented;  
* any specific Deutsche Bahn or other transport API is currently supported;  
* connection-risk predictions have already been validated;  
* machine-learning models already exist;  
* the application is production-ready;  
* passenger-rights decisions are supported;  
* any particular deployment architecture or technology stack has been finalized.

Implementation claims should only be added to this README once they are supported by the actual repository.

---

# **The Problem**

Consider a passenger changing trains at Frankfurt Hauptbahnhof.

Their itinerary provides a scheduled 12-minute connection.

The incoming train is currently six minutes late.

A conventional application may show:

Incoming train: \+6 min  
Scheduled transfer: 12 min

At first glance, the passenger appears to have six minutes left.

But that is not necessarily the usable transfer time.

Suppose:

Scheduled connection       12 min  
Incoming train delay       \+6 min  
Platform transfer           4 min  
Safety buffer               2 min  
\---------------------------------  
Effective buffer             0 min

The meaningful question is therefore not:

> How late is my train?

It is:

> **Given the current railway state, how realistic is my next connection?**

And if the answer is "not very realistic":

> **What is the best reasonable next action?**

That is the problem AnschlussPilot is intended to solve.

---

# **Product Thesis**

AnschlussPilot is based on five principles:

> **1\. Predict the journey outcome, not merely the train delay.**

> **2\. Give the traveller a decision, not just railway data.**

> **3\. Never present uncertainty as certainty.**

> **4\. Data quality comes before model complexity.**

> **5\. Build one excellent German rail use case before expanding scope.**

The core product pipeline is intentionally narrow:

Planned journey  
      ↓  
Timetable \+ realtime railway state  
      ↓  
Connection feasibility  
      ↓  
Risk explanation  
      ↓  
Reasonable alternative  
      ↓  
Expected destination impact

If this chain works reliably, AnschlussPilot already provides meaningful value without needing to become a general-purpose transport super-app.

---

# **Target Users**

The initial target user is:

> **An individual passenger travelling through Germany on a rail journey containing at least one transfer.**

The MVP is particularly relevant to:

* ICE, IC, and EC passengers;  
* journeys combining long-distance and regional rail;  
* travellers making time-sensitive connections;  
* international passengers unfamiliar with German railway operations;  
* passengers for whom a missed connection causes significant disruption.

The initial product is **not** intended for:

* railway dispatch centres;  
* transport operators;  
* enterprise travel management;  
* travel agencies;  
* fleet or infrastructure management.

Those would represent substantially different B2B products.

---

# **Core Product Questions**

Every MVP feature should materially improve at least one of three questions.

## **1\. Can I still make this connection?**

The system should assess whether the current transfer remains realistically feasible.

## **2\. What happens to my complete journey?**

A train delay matters primarily because of its effect on the passenger's eventual arrival.

The product should therefore reason about the **journey**, not just an individual train.

## **3\. What should I do next?**

When the original transfer becomes unsafe or impossible, the system should identify a reasonable railway alternative and explain its impact on destination arrival.

Features that do not substantially improve one of these questions should normally remain outside the MVP.

---

# **Connection-Risk Model**

A connection should not be classified using a rule as simplistic as:

$$  
\\text{incoming delay} \> \\text{scheduled transfer time}  
\\Rightarrow  
\\text{missed connection}.  
$$

A more useful conceptual quantity is the **effective transfer buffer**:

# **$$**

# **B\_{\\mathrm{effective}}**

## **T\_{\\mathrm{departure,next}}**

## **T\_{\\mathrm{arrival,current}}**

## **T\_{\\mathrm{transfer}}**

T\_{\\mathrm{safety}},  
$$

where:

* $T\_{\\mathrm{arrival,current}}$ is the currently expected arrival of the incoming service;  
* $T\_{\\mathrm{departure,next}}$ is the currently expected departure of the connecting service;  
* $T\_{\\mathrm{transfer}}$ is the estimated time required to perform the transfer;  
* $T\_{\\mathrm{safety}}$ is an additional operational safety margin.

This is deliberately a conceptual model rather than a claim that transfer feasibility can always be reduced to one equation.

Real railway operations may involve:

* platform changes;  
* cancellations;  
* partial cancellations;  
* delayed departures of the connecting service;  
* train splitting or joining;  
* through services;  
* changed stopping patterns;  
* replacement transport;  
* inconsistent or stale realtime information.

These cases belong in the domain model rather than being treated as unusual exceptions after implementation.

---

# **Risk States**

The initial product should use a small, stable, explainable vocabulary.

| State | Meaning |
| ----- | ----- |
| **Safe** | Current information indicates a reasonable transfer margin. |
| **Attention** | The available transfer margin is becoming limited. |
| **High Risk** | Missing the planned connection is a material possibility. |
| **Missed / Unavailable** | The original connection is no longer feasible according to current information. |
| **Unknown** | Available data is insufficient for a reliable assessment. |

`Unknown` is a required product state.

A trustworthy decision-support system must be capable of saying:

> **We don't know.**

It must not manufacture a risk score merely because the user interface expects one.

---

# **Explainability**

The connection assessment should be understandable without exposing unnecessary implementation details.

A result might conceptually look like:

HIGH RISK

Incoming service:  
\+8 min

Scheduled transfer:  
11 min

Estimated transfer requirement:  
4–6 min

Effective remaining buffer:  
approximately \-3 to \-1 min

The purpose of the explanation is not to demonstrate algorithmic sophistication.

It is to help the passenger understand why the recommendation changed.

---

# **MVP**

The MVP can be defined as follows:

> **Given a supported German rail itinerary containing at least one transfer, AnschlussPilot evaluates each connection as railway conditions change, explains its current risk, and presents a reasonable alternative when the original connection becomes unsafe or impossible.**

## **Intended MVP Capabilities**

| Capability | Responsibility |
| ----- | ----- |
| **Journey input** | Select an origin, destination, date/time, and supported itinerary. |
| **Timetable ingestion** | Represent scheduled stops, arrivals, departures, and services. |
| **Realtime status** | Process delays, cancellations, platform changes, and other states actually supported by the selected provider. |
| **Journey state** | Represent journey legs and transfers. |
| **Connection Risk Engine** | Determine the current feasibility state of a transfer. |
| **Risk explanation** | Explain the factors behind the current assessment. |
| **Transfer buffer** | Estimate the currently available transfer margin. |
| **Alternative connection** | Identify a reasonable next railway option when the planned transfer fails. |
| **Expected arrival impact** | Show how disruption or rerouting affects destination arrival. |
| **Data freshness** | Expose when operational information was last successfully updated. |
| **Unknown-state handling** | Refuse to produce unjustified certainty when required data is unavailable. |
| **Responsive interface** | Remain usable on a mobile browser during travel. |
| **Historical observations** | Preserve permitted operational observations for later reliability analysis. |
| **Observability** | Detect failures in collectors, providers, processing, and application services. |

---

# **MVP User Experience**

The first version does not need a large collection of screens.

Four strong product surfaces are sufficient.

## **Journey Search**

Select a supported journey.

## **Journey Monitor**

Show the passenger's current journey, legs, transfers, and relevant disruption state.

## **Connection Detail**

Answer:

What is the current risk?  
How much transfer margin remains?  
Why has the risk changed?  
How fresh is the underlying data?

## **Alternative**

Show a reasonable alternative connection together with its effect on expected destination arrival.

---

# **Decision-First UX**

A passenger standing on a platform under time pressure should not have to interpret a railway operations dashboard.

A connection view might conceptually present:

Frankfurt Hbf  
ICE 123 → ICE 789

HIGH RISK

Expected arrival: 16:19  
Connecting departure: 16:23  
Estimated transfer requirement: 6–8 min

Alternative  
ICE 791 · 16:42

Expected destination impact:  
\+19 min

The intended hierarchy is:

Decision  
   ↓  
Risk  
   ↓  
Reason  
   ↓  
Alternative  
   ↓  
Technical details

not:

Raw provider data  
   ↓  
Train metadata  
   ↓  
Charts  
   ↓  
Statistics  
   ↓  
Passenger decision

AnschlussPilot should be a **decision-support product**, not a railway-data dashboard.

---

# **Data Architecture**

The external railway provider must not become the application's domain model.

A provider boundary should separate transport-specific payloads from internal product logic:

External railway provider  
          ↓  
    Provider adapter  
          ↓  
     Normalization  
          ↓  
AnschlussPilot domain model  
          ↓  
 Journey / Risk engine  
          ↓  
       API / UI

This allows external data sources to evolve without forcing connection-risk logic to depend directly on provider-specific JSON structures.

The internal model should eventually be able to represent concepts such as:

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

The precise implementation should be documented only after the repository contains it.

---

# **Railway Identity and State Reconciliation**

Realtime railway software is not ordinary CRUD software.

A single service may produce a sequence of observations such as:

15:01  delay \+3  
15:04  delay \+5  
15:06  platform changed  
15:08  delay \+9  
15:10  corrected to \+6  
15:14  partial cancellation

Simply overwriting the previous record loses important information.

The architecture should distinguish:

Scheduled truth  
Realtime observations  
Current interpreted state  
Historical observations

A particularly important engineering problem is **service identity**.

The same physical train run must not accidentally become multiple unrelated entities simply because:

* its train number changes;  
* realtime information is corrected;  
* its stopping pattern changes;  
* an event is received twice;  
* updates arrive out of order.

Correct railway state reconciliation is a core product capability.

---

# **Data Is a Product Asset**

The long-term value of AnschlussPilot is likely to depend at least as much on its data quality as on its application code.

Historical observations may eventually enable analysis of:

* delay evolution;  
* station-specific transfer reliability;  
* service-specific reliability;  
* connection outcomes;  
* disruption patterns;  
* transfer-time assumptions;  
* prediction calibration.

Accordingly, data ingestion should be designed for:

* normalization;  
* provenance;  
* deduplication;  
* temporal correctness;  
* event identity;  
* schema evolution;  
* missing-data handling;  
* reproducibility.

External data may only be stored or reused when permitted by its applicable licence and terms.

---

# **Deterministic Before Machine Learning**

AnschlussPilot should **not** begin as an AI-first project.

The initial system should establish a deterministic, explainable baseline.

Conceptually:

$$  
R \=  
f(  
\\text{remaining transfer time},  
\\text{estimated transfer requirement},  
\\text{delay evolution},  
\\text{cancellation state},  
\\ldots  
).  
$$

A simple but defensible:

HIGH RISK

is preferable to presenting:

78% probability of missing connection

when that probability has not been validated.

Machine learning should be introduced only when it can demonstrate measurable improvement over the deterministic baseline.

---

# **Future Statistical Modelling**

After sufficient historical data has been collected, AnschlussPilot may investigate models for quantities such as:

$$  
P(\\text{miss connection}\\mid X)  
$$

or destination-arrival distributions.

A probabilistic model must not be evaluated solely by generic classification accuracy.

Relevant evaluation should include, where appropriate:

* temporal train/test splits;  
* Brier score;  
* log loss;  
* calibration curves;  
* reliability diagrams;  
* confidence intervals;  
* deterministic baseline comparison;  
* feature-leakage analysis;  
* distribution-shift monitoring.

If the product displays an 80% missed-connection probability, events assigned approximately 80% probability should fail at approximately that frequency over an appropriate evaluation population.

**Probability calibration is a product requirement, not merely a modelling detail.**

---

# **Uncertainty-Aware UX**

Prediction uncertainty must be reflected in the interface.

The application should distinguish between:

* a deterministic operational fact;  
* a current realtime observation;  
* an estimate;  
* an inference;  
* a probabilistic prediction;  
* unavailable information.

For example:

Estimated transfer requirement:  
4–6 min

is preferable to:

Transfer requires exactly 5 min 14 sec

when the system cannot justify that precision.

Similarly, a prediction must never be worded as a carrier guarantee.

---

# **Realtime Reliability**

AnschlussPilot depends on external systems that may be:

* unavailable;  
* delayed;  
* incomplete;  
* contradictory;  
* rate-limited;  
* stale.

Failure handling is therefore part of the product.

If realtime information is unavailable, the application should prefer:

Realtime data unavailable

Last successful update:  
18:42

over silently presenting stale data as live information.

The system should distinguish at minimum between:

scheduled data  
live/realtime data  
stale data  
missing data  
inferred state

---

# **Alternative Recommendations**

When a planned connection becomes infeasible, AnschlussPilot may identify a reasonable railway alternative based on currently available information.

An alternative can include:

* the next reasonable railway connection;  
* expected departure;  
* expected destination arrival;  
* additional journey time.

The system may state:

> **Based on currently available timetable information, this is the next reasonable connection.**

It must not automatically state:

> **You are legally entitled to board this train.**

Operational routing and passenger-rights or fare-rule decisions are different domains.

Ticket validity and legal entitlement remain outside the initial MVP.

---

# **Privacy by Design**

The initial product should minimize personal-data collection.

The core MVP should not require an account merely to evaluate a journey.

Where feasible, an anonymous session should be preferred over collecting:

* names;  
* email addresses;  
* Deutsche Bahn credentials;  
* uploaded tickets;  
* payment information;  
* continuous precise GPS history.

Authentication, notifications, cross-device synchronization, and personalized behaviour should be introduced only when their product value justifies the additional security and GDPR surface.

---

# **Security**

Security requirements should grow with the application's capabilities, but secure engineering begins before production deployment.

Relevant concerns include:

* secret management;  
* authentication and authorization when accounts are introduced;  
* transport encryption;  
* secure API design;  
* dependency management;  
* rate limiting;  
* data-retention policies;  
* auditability;  
* abuse prevention;  
* backup and recovery.

Sensitive capabilities should not be added merely because they are convenient to implement.

---

# **Legal, Licensing, and Independence**

AnschlussPilot is intended to be an **independent product**.

It must not imply that it is operated, endorsed, or officially provided by Deutsche Bahn or another railway operator unless such a relationship actually exists.

Any external data integration must be evaluated against the provider's then-current:

* API terms;  
* licence;  
* attribution requirements;  
* storage rights;  
* caching restrictions;  
* redistribution rules;  
* permitted commercial or non-commercial uses.

Legal wording must not be frozen in advance of selecting and verifying the actual data sources.

If passenger-rights functionality is introduced later, applicable EU and German rules must be treated as a separately versioned domain rather than inferred casually by an AI model.

---

# **Quality Assurance**

Railway applications contain far more edge cases than a happy-path demonstration suggests.

Testing should eventually include scenarios such as:

* cancellations;  
* partial cancellations;  
* platform changes;  
* journeys crossing midnight;  
* changed train numbers;  
* duplicate realtime events;  
* out-of-order events;  
* delay corrections;  
* provider timeouts;  
* stale data;  
* missing realtime information;  
* changed stopping patterns;  
* terminated journeys;  
* same-name stations;  
* train splitting or joining;  
* changed transfer stations.

Domain edge cases should be represented as explicit tests rather than being discovered only after deployment.

---

# **Observability and Operations**

If AnschlussPilot becomes publicly available, reliability becomes part of the product itself.

Operational monitoring should eventually cover:

* provider availability;  
* data freshness;  
* collector failures;  
* queue or processing backlog;  
* API latency;  
* error rates;  
* database health;  
* application availability;  
* resource consumption;  
* deployment failures;  
* infrastructure cost.

A system designed to help passengers react to unreliable railway operations must itself fail transparently and predictably.

---

# **Product Analytics**

If the project reaches real users, success should not be measured primarily through page views.

More meaningful product metrics may include:

* useful warning lead time;  
* missed-connection detection performance;  
* false-warning rate;  
* prediction calibration;  
* alternative-route usefulness;  
* journey completion outcomes;  
* recommendation adoption;  
* data freshness;  
* unsupported/unknown assessment frequency.

Exact KPIs should be defined only once the corresponding product behaviour can actually be measured.

---

# **Non-Goals**

Scope discipline is a core design requirement.

The initial AnschlussPilot product will **not** attempt to:

1. replace DB Navigator;  
2. become a complete German public-transport application;  
3. sell railway tickets;  
4. process payments;  
5. manage seat reservations;  
6. sign in to Deutsche Bahn accounts;  
7. import private tickets by default;  
8. automatically file compensation claims;  
9. provide guaranteed passenger-rights decisions;  
10. guarantee that a passenger will make or miss a connection;  
11. provide turn-by-turn indoor station navigation;  
12. support the entire European railway network from the beginning;  
13. combine rail, flights, coaches, taxis, car sharing, and other modes into a universal journey planner;  
14. add an LLM chatbot merely to make the project appear AI-driven;  
15. depend on unvalidated machine-learning predictions;  
16. expose probabilities that have not been appropriately calibrated;  
17. collect personal information unnecessary for the core use case;  
18. claim that external railway information is always correct or realtime;  
19. fabricate certainty when information is missing;  
20. confuse an operational recommendation with a legal entitlement.

These are deliberate product boundaries, not missing features.

---

# **Engineering Priorities**

For the initial GitHub project, the technical priorities are approximately:

Railway Domain Model  
        ↓  
Product Definition  
        ↓  
Data Engineering  
        ↓  
Backend / State Reconciliation  
        ↓  
Deterministic Risk Engine  
        ↓  
Frontend / UX  
        ↓  
Historical Data  
        ↓  
Statistical Validation  
        ↓  
Machine Learning

Several disciplines cut across the entire system:

QA  
DevOps / SRE  
Security  
Privacy  
Licensing  
Observability

This ordering is intentional.

**Machine learning is not the foundation of AnschlussPilot.**

The foundation is:

> **reliable transport data \+ a correct railway domain model \+ a verifiable connection-risk system.**

---

# **Disciplines Informing the Project**

Although AnschlussPilot may be developed as an individual GitHub project, its design should be reviewed through several professional perspectives:

* Railway domain engineering  
* Mobility product management  
* Data engineering  
* Backend and distributed systems  
* Applied machine learning  
* Statistics and forecasting  
* Frontend/mobile engineering  
* Human-computer interaction  
* QA and test automation  
* DevOps/SRE  
* Security and privacy engineering  
* German/EU data licensing and GDPR  
* Passenger-rights domain expertise  
* German localization and UX writing  
* Product analytics

A single contributor may perform several of these roles.

The purpose of listing them is not to imply a large project team, but to make the quality bar explicit.

---

# **Localization**

The repository documentation may primarily use English to support international technical collaboration.

A passenger-facing product intended for Germany should eventually treat German as a first-class product language rather than as an afterthought.

Railway terminology should be reviewed in its actual transport context, including terms such as:

* `Anschluss`  
* `Umstieg`  
* `Verspätung`  
* `Zugausfall`  
* `Gleisänderung`  
* `voraussichtliche Ankunft`

Product localization should prioritize natural German railway language over literal translation from English.

---

# **MVP Completion Criteria**

The MVP should not be considered complete simply because a web application exists.

It should satisfy several categories of correctness.

## **Product Correctness**

Given:

A → B → C

the system must understand:

leg 1  
connection at B  
leg 2

and reassess the transfer at `B` when relevant railway state changes.

## **Data Correctness**

The same train run should not become multiple unrelated services because of realtime updates or corrections.

The system must distinguish:

* delay;  
* cancellation;  
* partial cancellation;  
* platform change;  
* missing data;  
* stale data.

## **Decision Usefulness**

For a threatened transfer, the application should answer:

What is happening?  
How risky is the connection?  
Why?  
What can I do next?

## **Reliability**

External-provider failure must not crash the user journey or silently transform stale data into apparently live information.

## **Explainability**

The user should be able to understand why the connection status changed without needing to understand the underlying implementation.

---

# **Post-MVP Direction**

Only after the core railway-data and connection-assessment pipeline is reliable should the project expand.

A possible progression is:

Reliable realtime data pipeline  
            ↓  
Historical reliability dataset  
            ↓  
Probabilistic risk modelling  
            ↓  
Arrival-delay distributions  
            ↓  
Calibrated connection probabilities  
            ↓  
Station-specific reliability modelling  
            ↓  
Personalized transfer requirements  
            ↓  
Notifications  
            ↓  
Passenger-rights assistance  
            ↓  
European expansion

Possible later capabilities such as:

* user accounts;  
* push notifications;  
* GPS;  
* ticket integration;  
* passenger-rights automation;  
* compensation workflows;  
* European railway coverage;

should each be treated as substantial product decisions rather than small additions to the MVP.

---

# **Development**

The repository should document concrete development instructions only after the corresponding implementation exists.

Future versions of this section may contain verified information about:

* technology stack;  
* local development setup;  
* supported runtime versions;  
* environment variables;  
* database setup;  
* railway-provider configuration;  
* test commands;  
* linting and formatting;  
* architecture documentation;  
* deployment;  
* monitoring.

Until those choices exist in the repository, this README intentionally avoids inventing them.

---

# **Contributing**

During the pre-MVP phase, proposed changes should be evaluated against the project's core use case:

> **Does this materially improve the passenger's ability to assess or react to a threatened German rail connection?**

High-value contributions are expected to strengthen areas such as:

* railway-domain correctness;  
* transport-data quality;  
* realtime state reconciliation;  
* connection-risk logic;  
* uncertainty handling;  
* alternative-journey reasoning;  
* mobile decision UX;  
* test coverage;  
* operational reliability.

Feature count is not a project objective.

---

# **Disclaimer**

AnschlussPilot is intended as an independent railway journey decision-support project.

Schedules, realtime observations, estimates, predictions, and suggested alternatives may be delayed, incomplete, unavailable, or incorrect.

The project must not be treated as:

* an official railway-operator service;  
* a guarantee that a particular connection will be made;  
* a guarantee that a suggested service may legally be boarded;  
* authoritative fare advice;  
* legal advice concerning passenger rights;  
* a contractual transport guarantee.

Users should verify critical travel decisions against appropriate official information.

---

# **Project Principle**

If AnschlussPilot succeeds at one thing, it should be this:

> **When a German rail journey starts going wrong, the passenger should understand the risk early enough to make a better decision.**

