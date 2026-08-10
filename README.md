# AnschlussPilot

**Disruption-aware journey decision support for German rail.**

> When a German rail journey starts going wrong, the useful question is not
> *"how late is my train?"* — it is *"is continuing still my best option, and if not,
> what should I do while there is still time to change it?"*

---

## Repository Status

> [!IMPORTANT]
> **Nothing is implemented yet.**
>
> This repository contains documentation only — this file, `AGENTS.md`, and the
> detail documents in [`docs/`](docs/). There is no application, no provider
> integration, no dataset, and no model.

| Claim | Status |
| --- | --- |
| Product direction | Defined (this file) |
| Engineering rules | Defined (`AGENTS.md`) |
| Phase 0 protocol manifest | `UNSET` / `BLOCKED` — freeze gates incomplete |
| Competitive benchmark (A5a) | **Not started — cheapest existence check** |
| Provider evaluation | Scaffold only; matrix unfilled — **blocking data-dependent work** |
| Identity-resolution spike (A7) | **Not started — blocking** |
| Observation collector | **Not started — blocking** |
| Domain model / risk engine / decision engine | Not started |
| Historical dataset, backtesting, ML | Not started |
| Deployment, monitoring, GDPR review | Not started |

Capabilities are described as implemented only once they are verifiably present
in this repository (`AGENTS.md` **I2**).

---

## 1. The Problem

German rail apps already surface plenty of operational information: delays,
cancellations, platform changes, updated times, alternative connections.

But operational information is not a decision.

Consider a passenger travelling `Mannheim → Frankfurt Hbf → Hamburg Hbf`.
The incoming train is late and the Frankfurt transfer is tightening.

A conventional display says:

```text
Incoming train    +8 min
Connection time   11 min
```

A connection-risk model improves this to:

```text
Connection success probability: 41%
```

Neither answers the passenger's actual question:

> **Should I stay on this train to Frankfurt, or get off earlier and reroute?**

That question is different in kind. It requires comparing what happens **if I
continue** against what happens **if I change** — a counterfactual comparison,
not a status report.

AnschlussPilot is an attempt to answer that question, and only that question.

---

## 2. Product Thesis

> **Estimate the journey outcome under each realistically available action,
> compare them, and recommend the better one — while there is still time to act.**

Six principles:

1. Optimize the **journey outcome**, not individual train punctuality.
2. Give the passenger a **decision**, not railway data.
3. Treat connection risk as an **input** to a decision, never as the output.
4. Never present uncertainty as certainty.
5. Data quality and railway-domain correctness come before model complexity.
6. Solve one excellent German rail disruption case before expanding scope.

Engineering rules that follow from these principles live in
[`AGENTS.md`](AGENTS.md). This file does not restate them.

---

## 3. The Bet

This section exists because the rest of the document is worthless if these
assumptions are false. **None of them has been tested against real data.**

### A1 — The opportunity exists, and is large enough to matter

There is a materially large class of journeys where **changing earlier produces
a meaningfully better destination arrival than continuing**, and where this is
knowable *at decision time* rather than only in hindsight.

Frequency alone is the wrong criterion. A 3% opportunity rate saving 45 minutes
beats a 15% rate saving 6 minutes. The judgement is on **expected value**:
rate × magnitude, evaluated under each ticket-binding scenario (A6).

Measure separately:

- **operational opportunity rate** — transfers where changing earlier was
  materially better, assuming nothing about the passenger's ticket;
- the same rate **under each binding scenario** (A6) — a sensitivity band, not a
  second measurement;
- mean destination-delay improvement per monitored journey;
- share of opportunities saving ≥ 15 min.

> **Not measurable in Phase 0:** how often a *real passenger* encounters this.
> That requires demand weighting the project does not have. The operational rate
> must never be restated as a passenger encounter rate — see
> [`docs/modelling-and-evaluation.md`](docs/modelling-and-evaluation.md) §2.

**Test:** the opportunity measurement in Phase 0 (§4), reported at the disruption
episode level with clustered confidence intervals. It also reports the eligible
population, evaluable count, unknown / out-of-scope count, evaluable-conditional
rate, lower and upper bounds, and coverage gap defined in
[`docs/phase0-protocol.md`](docs/phase0-protocol.md) §4. Thresholds are derived
from the observed distribution rather than chosen in advance — see §12.

### A2 — The decisive signal is observable

Whether a connection succeeds is frequently decided by dispatch
(*Anschlusssicherung* — whether the outgoing service is held). If public data
does not expose this reliably and early enough, decision quality has a hard
ceiling regardless of modelling effort.

- A system honestly obeying `AGENTS.md` **I4** would then return `UNKNOWN` in most
  interesting situations. That is *correct* behaviour, but it may not be a
  *usable product*.
- **Test:** measure how often, and how far in advance, hold/no-hold outcomes are
  inferable from the candidate feeds.

### A3 — Transfer requirement is estimable

The effective transfer buffer (§6) depends on `T_transfer`, the time the
passenger actually needs to change trains. Station-level minimum transfer times
are constants; they do not capture platform pairs, level changes, or luggage.

- If `T_transfer` can only be bounded very loosely, the honest output is a wide
  interval, which pushes many cases into `ATTENTION`/`UNKNOWN`.
- **Test:** identify an actual data source before implementing the risk engine.

### A4 — The data may legally be stored

The long-term asset described in §11 is a historical observation store. Storage,
redistribution, and model-training rights are **provider-specific and not
assumed** (`AGENTS.md` **I15**).

- **Test:** the provider evaluation (§4), which must precede any collector that
  retains data beyond ephemeral use.

### A5 — The delta over existing tools is real, and lasts

Existing tools already show delays, alternatives, and connection warnings. The
differentiator claimed here is narrow: **counterfactual comparison of
continue-vs-change at decision time, including changing before the threatened
transfer station.**

A one-off check is not enough, because the gap can close during the build. Split
in two:

- **A5a — the gap exists today.** No current tool compares the outcome of
  continuing against the outcome of changing.
- **A5b — the gap is defensible long enough** to justify a multi-month build.

**Test:** fixed, versioned archetypes populated with fresh qualifying cases and
run against the live products at Phase 0 start, Phase 0 report, and Phase 0.5
exit — never from memory. Design in
[`docs/market-and-validation.md`](docs/market-and-validation.md) §2. First run is
capped at half a day; an incomplete sample is `inconclusive`, never evidence that
a competitor lacks the capability.

### A6 — The recommendation is one the passenger can actually take

**Unverified working hypothesis:** a discounted German rail ticket may be bound
to specific services (*Zugbindung*), and relief may occur only after the useful
early-intervention window. The applicable rule and timing must come from the
independent carrier-conditions check; the provider matrix cannot establish it.

This puts the differentiator and the main legal constraint on a collision
course: `REROUTE_EARLY` is most valuable precisely when the passenger is least
likely to be free to act on it.

- **Addressable population hypothesis.** If unbound long-distance travellers are
  a minority and can already rebook freely, the passengers who benefit most may
  be the ones least able to act. Phase 0 does not estimate that population.
- **Effective opportunity rate.** If the rate collapses under the `BOUND`
  scenario, the product is materially narrower than A1 alone suggests.

**Binding status is not observable in Phase 0.** Phase 0 evaluates synthetic
itineraries (§4) — there is no passenger, therefore no ticket, therefore no
binding state to measure. A6 is consequently tested as a **sensitivity analysis,
not a population measurement**: binding is a *filter on the admissible candidate
set*, which is fully computable without anyone holding a ticket.

**Test:** compute the opportunity rate twice — once admitting all candidates
(`UNBOUND` scenario), once admitting only those a bound ticket would permit
(`BOUND` scenario) — and report both as a band. The applicable relief thresholds
and conditions must be verified against current carrier terms, never written
from memory (`AGENTS.md` I2). Design position: §7.1.

---

### The other half of the bet

A1–A7 answer *can a useful decision be made?* They cannot answer *will anyone
act on it, and could it ever reach them?* Those are separate hypotheses with
their own tests, kept in
[`docs/market-and-validation.md`](docs/market-and-validation.md) rather than
mixed into the technical assumptions:

- **Initial customer profile** — a six-clause conjunction, not "rail passengers"
- **Distribution risk** — the incumbent app averaged **23.7 million monthly
  active users** in the six months to June 2026
  ([source](https://int.bahn.de/en/site-notice/transparency_report_dsa),
  verified 2026-08-10). The question is therefore not *"is the recommendation
  better?"* but ***"is it good enough that someone opens a second app
  mid-disruption?"***
- **Commercial tracks** — passenger-facing product (`P-B2C`) and decision engine
  inside an existing surface (`P-B2B2C`), both left open
- **B1–B4** — comprehension, trust, acquisition friction, willingness to pay or
  integrate; tested in Phase 0.5

**The technical thesis can pass completely while the commercial one fails.**
Neither substitutes for the other.

### A7 — The same service run can be recognized across polls

Every downstream capability assumes that two observations taken minutes apart
can be identified as belonging to the same train run. Delay evolution, state
reconciliation, counterfactual labelling, and every historical statistic depend
on it.

**This is the most insidious assumption in the list, because failure is
invisible.** Mis-linked runs still produce data that looks entirely normal:
coherent delay sequences, plausible timestamps, statistics that compute without
error. A delay-evolution curve stitched together from three different trains is
indistinguishable from a real one until something downstream contradicts it —
typically weeks later, by which point the whole store is suspect.

It is also the **cheapest assumption to test**, and the only one testable before
any real infrastructure exists.

**Test:** poll one corridor for ~6 hours and attempt to link service runs across
consecutive polls. Count how often linkage fails or is ambiguous. **One day of
work; it de-risks the entire five-week plan.** This spike runs before the
collector is built, not after — see §4.

---

## 4. Phases

The project targets two outcomes — a measured research result and a working
product prototype — **sequenced, not pursued simultaneously**. The research
deliverable is a strict subset of the product's foundation, so one body of work
returns both.

| Phase | Question it answers | Gate to the next |
| --- | --- | --- |
| **0 — R** | **Can we make a useful decision?** A measurement report: does the policy beat the baseline, and by how much? | The report itself. Also the product go/no-go. |
| **0.5 — P-minimal** | **Will anyone act on it?** Paper-prototype check, then the author uses it on their own real journeys. Laptop-hosted, single user, no reliability guarantees. Tests **B1–B4**. | Comprehension, trust, and friction — see [`docs/market-and-validation.md`](docs/market-and-validation.md) §5 |
| **1 — P** | **Can we distribute and sustain it?** A prototype a handful of people can use, on one of the two commercial tracks | Only if 0 and 0.5 both say yes |

Every deliverable is tagged **`R`**, **`P`**, or **`RP`**. The rule this exists
to enforce:

> **Before the Phase 0 report exists, any `P`-tagged work is scope creep.**

Dual goals otherwise remove the ability to refuse work — every task can be
justified as serving one of them. The tag makes that visible instead of
arguable.

**Phase 0 has no user interface and no live service.** Its only goal is to
answer §3 with evidence.

### Design decisions that cannot be revisited later

These four are fixed before the collector's first commit. Each one, if wrong,
is unrecoverable — the missing data cannot be back-filled by any later effort.

**1. Collection scope: corridor-wide, not journey-wide.**
Capture every relevant service on the corridor, not only the services used by a
monitored itinerary. Labelling an opportunity requires the counterfactual — *what
would have happened had the passenger changed at Mannheim* — and that answer only
exists if the alternative train's **actual** run was also observed. A collector
scoped to one itinerary makes A1 permanently unmeasurable.

**2. Itinerary enumeration: synthetic.**
Phase 0 needs no real users. Once corridor-wide data exists, enumerate plausible
`A → B → C` itineraries from the timetable and evaluate all of them. This is what
makes the sample size in §12 reachable in weeks rather than months. It depends
entirely on decision 1.

**3. Corridor selection: one corridor, finished, before considering a second.**
Alternative density drives the result — a corridor with frequent alternatives
systematically inflates the opportunity rate. Two contrasting corridors would be
methodologically better, and that is not the binding constraint here.

Two corridors roughly double the time to a first result, and elapsed time is the
dominant risk to a solo project (§12, S8). **A single-corridor result carrying an
explicit non-extrapolation statement is a complete, publishable finding.** Two
corridors that never get finished are not.

Measure one. Add the second only once the first has produced a report. The report
states the corridor and its alternative density, and does not generalize beyond
it.

**4. Collection integrity from commit one.**
A collector that dies silently leaves a permanent hole. Heartbeat and gap
detection ship with the collector, not after it. Gaps must be *recorded*, not
merely absent, so replay can distinguish *"nothing happened"* from *"we were not
looking"*. See [`docs/railway-domain.md`](docs/railway-domain.md) §9.

### Deliverables

| Deliverable | For | Est. | Purpose |
| --- | --- | --- | --- |
| **Competitive benchmark (A5a)** | `RP` | **½ d** | Fixed archetypes, two fresh cases per archetype, against the live tools. Does any already compare continue-vs-change counterfactually? **Runs first — the cheapest way to discover the project has no reason to exist.** Re-run at the report and at Phase 0.5 exit for A5b. |
| `docs/provider-evaluation.md` | `RP` | 0.5 w | Which data feeds exist; what each exposes; storage / redistribution / training / commercial terms; attribution; station transfer and topology data. **Blocks all data-dependent work, but not A5a.** It does not establish carrier fare conditions. |
| **Identity-resolution spike (A7)** | `RP` | **1 d** | Six hours of polling, then attempt to link runs across polls. Tests A7 before anything is built on it. **A failure here changes the collector's design, not just its schedule.** |
| Transfer-data-source evaluation (A3) | `RP` | ½ w | Identify an actual source for `T_transfer`, or establish that only wide intervals are defensible. A3 currently has a test and no deliverable. |
| Carrier-conditions check | `RP` | ½ w | Independently verify ticket-binding and relief conditions from issuer / operator sources. The data-provider matrix cannot answer this. Until complete, [`docs/binding-scenarios.md`](docs/binding-scenarios.md) stays `UNSET`. |
| Corridor + observation schema freeze | `RP` | ½ w | Freeze corridor graph and observation field slots / statuses before collection. Only `received_at` is always required; provider, event and effective times may be explicitly absent. See [`docs/railway-domain.md`](docs/railway-domain.md) §10. |
| Observation collector | `RP` | 1 w | Append-only, provenance-tagged, **corridor-wide** capture of scheduled + realtime state. Started as early as licensing permits — *neither elapsed time nor missing scope can be back-filled.* |
| Collection integrity | `RP` | ½ w | Heartbeat, gap detection, explicit gap records. Same commit as the collector. |
| Storage + replay harness | `RP` | 1–1.5 w | Reconstruct "what was known at time *t*" from stored observations. |
| Deterministic baseline + policy | `RP` | 1 w | e.g. transfer-buffer rule, continue-unless-impossible. **Frozen and recorded in `decisions.md` before the measurement window opens** — see [`docs/modelling-and-evaluation.md`](docs/modelling-and-evaluation.md) §2. |
| Binding-scenario filter | `RP` | ½ w | Candidate admissibility under each ticket-binding scenario. Not a passenger feature in Phase 0 — a filter that makes the sensitivity analysis in A6 computable without any passenger. Rules are versioned in [`docs/binding-scenarios.md`](docs/binding-scenarios.md); a result without a ruleset version is not a result. |
| Full Phase 0 protocol freeze | `RP` | ½ d | Fill and version [`docs/phase0-protocol.md`](docs/phase0-protocol.md): enumeration, binding, episode windows, baseline, decision policy, measurement / holdout boundaries and Git commit. Any `UNSET` gate blocks measurement. |
| Synthetic enumeration + measurement | `R` | 1 w | The A1 / A2 / A6 numbers, reported per binding scenario, coverage state and disruption episode, including conditional rates and population bounds. **Go / no-go.** |
| Segment analysis | `R` | ½ w | Which journey shapes, stations / segments and times within the single measured corridor concentrate the opportunity. Analysis of data already collected — no new engineering. Feeds the ICP hypothesis in [`docs/market-and-validation.md`](docs/market-and-validation.md) §1. |
| Phase 0 report | `R` | ½ w | See below. Research output and product gate in one document. |

The paper-prototype check moved to **Phase 0.5**, where the interface it informs
actually exists. Phase 0 therefore contains **no `P`-tagged work at all**, and
the scope-creep rule above has no exceptions.

### Effort

**≈ 7–8 focused work-weeks for Phase 0.** These are planning estimates by
analogy, not measurements — they will be wrong, and the point is that they are
written down so the size of being wrong is visible.

| Available time | Phase 0 calendar |
| --- | --- |
| 8 h/week | ~8 months |
| 20 h/week | ~3.5 months |
| Full time | ~8 weeks |

> **This estimate grew from ≈5–6 weeks.** Fixing the methodology added the A5
> check, the A3 transfer-source evaluation, the observation contract and the
> binding-scenario filter. Separately, episode-level clustering
> ([`docs/modelling-and-evaluation.md`](docs/modelling-and-evaluation.md) §2) may
> extend the *measurement window* beyond what these work-weeks assume. At 8 h a
> week the honest read is that Phase 0 is close to a year — which is an argument
> for finding more hours, or for shrinking the corridor, not for pretending the
> earlier number was right.

Later phases, for planning only: **P-minimal +1–2 weeks**; **full P +4–6 weeks
plus ongoing operations**, which is the part a single person cannot sustain
indefinitely.

> **Overrun rule.** If any deliverable passes **2× its estimate**, stop and
> re-scope — shrink the corridor, shorten the window, simplify the policy. Do not
> push through. An estimate that is 2× wrong is evidence the design is wrong, not
> evidence that more hours are needed.

### The Phase 0 report

Not a pass/fail table. A pass/fail table records that the work happened; it does
not tell anyone what to do next. The report answers four questions and ends with
a verdict:

| | Question |
| --- | --- |
| **Product** | Is this worth doing on a passenger's behalf at all? |
| **Market** | Which **journey contexts** concentrate the opportunity within the measured corridor — stations / segments, times of day, transfer shapes, buffer ranges? *Not which passengers: Phase 0 has none. Passenger segmentation, ticket-mix and second-app behaviour are Phase 0.5 questions.* |
| **Data** | Which provider signals set the ceiling — and how low is it? |
| **Strategy** | `Proceed B2C` / `Proceed B2B2C` / `Narrow scope` / `Research only` / `Stop` |

It states whether A1–A7 hold, and if they do not, the product definition changes
before any further engineering.

**Written the same way whether the result is positive or negative.** A negative
result answered against these four questions is a genuine research contribution;
a negative result recorded as five failed checkboxes is not.

**Known blind spot.** Having no interface for months means no user feedback, so
Phase 0 cannot detect *"the recommendation was right but nobody dared follow
it"*. The paper-prototype check at the start of Phase 0.5 is the cheapest
available mitigation, not a substitute for real usage.

This blind spot is recoverable — it can be closed later with real users. The four
decisions above are not. That asymmetry is why they are settled first.

Everything below this line describes the intended product **after** Phase 0
succeeds. It is direction, not commitment.

---

## 5. Risk States and Decision States

### Risk vocabulary

| State | Meaning |
| --- | --- |
| `SAFE` | Current information indicates a reasonable transfer margin. |
| `ATTENTION` | The margin is becoming limited or uncertain. |
| `HIGH_RISK` | Missing the planned connection is a material possibility. |
| `MISSED_OR_UNAVAILABLE` | The connection is no longer feasible per current information. |
| `UNKNOWN` | Not enough reliable information to assess the connection. |

`UNKNOWN` is a first-class result. The system must be able to say
*"realtime information is insufficient to assess this connection"* and must never
convert missing data into artificial certainty.

### Risk is not the decision

Risk and action stay separate concepts. Both of these are valid:

```text
Risk: HIGH_RISK          Risk: ATTENTION
Action: CONTINUE         Action: REROUTE_EARLY
```

The first, when the outgoing train is also heavily delayed. The second, when a
clearly better alternative exists before the planned transfer station.

**`HIGH_RISK` does not imply rerouting.** Outcomes are compared; risk states are
not mapped to fixed reactions.

### Candidate actions

```text
CONTINUE_CURRENT_PLAN
WAIT_FOR_CONNECTION
REROUTE_EARLY
TAKE_LATER_CONNECTION
USE_ALTERNATIVE_RAIL_ROUTE
NO_RELIABLE_RECOMMENDATION
```

`CONTINUE_CURRENT_PLAN` is always an explicit candidate. The product question is
*"is changing actually better than doing nothing?"*, which requires that
baseline.

---

## 6. Connection Feasibility

A transfer is not `incoming delay > scheduled transfer time`.

The starting point is an effective transfer buffer:

$$B_{\text{eff}} = T_{\text{dep,next}} - T_{\text{arr,cur}} - T_{\text{transfer}} - T_{\text{safety}}$$

where $T_{\text{arr,cur}}$ is the current expected arrival of the incoming
service, $T_{\text{dep,next}}$ the current expected departure of the connecting
service, $T_{\text{transfer}}$ the estimated transfer requirement (see A3), and
$T_{\text{safety}}$ an operational margin.

```text
Scheduled transfer        12 min
Incoming delay             6 min
Transfer requirement       4 min
Safety margin              2 min
--------------------------------
Effective buffer           0 min
```

Feasibility also depends on outgoing delay, cancellations and partial
cancellations, platform changes, changed stopping patterns, split/join services,
through services, changed train numbers, station topology, and the freshness of
all of the above. Railway-domain correctness is therefore an architectural
concern, not a data-cleaning step. See [`docs/railway-domain.md`](docs/railway-domain.md).

The eventual decision rule is conceptually:

$$a^* = \arg\max_{a \in A_t} \mathbb{E}\left[U(Y) \mid X_t, a\right]$$

with $X_t$ the observable journey state, $A_t$ the reasonable available actions,
$Y$ the journey outcome, and $U$ its passenger-relevant value. This is a
direction. The first implementation is deterministic, explainable, and testable.

---

## 7. Known Constraints

These are product-level constraints, not future work. They limit what a
recommendation may claim.

### 7.1 Ticket binding (Zugbindung)

A discounted German rail ticket may be bound to specific services. A
recommendation to *"change earlier at Mannheim"* can therefore be an operationally
better route that the passenger is not free to take.

Passenger-rights adjudication is out of scope (§10), but the constraint is not:
an unusable recommendation has the wrong outcome estimate. See A6 for why this
is a product-level risk rather than a later feature.

Unbound long-distance tickets are roughly: fully flexible fares, BahnCard 100,
and some corporate fares. **A monthly regional pass such as the Deutschlandticket
is not one of them** — it does not cover ICE/IC/EC services, which are this
product's primary case. Every statement in this paragraph must be re-verified
against current carrier terms before being relied on.

**Position taken.** Binding status is an **input the passenger provides**, not
something the system infers:

```text
TicketConstraint:  UNBOUND | BOUND | UNKNOWN        (default UNKNOWN)
Candidate action:  executable | possibly-not-executable | unknown
```

The system filters and annotates. It never adjudicates. Output reads *"this
option may not apply to your ticket — check before boarding"*, never
*"you are entitled to board"* (`AGENTS.md` I14). When binding status is
`UNKNOWN`, executability stays `unknown` and must not be rendered as executable
(`AGENTS.md` I4).

### 7.2 Transfer requirement has no confirmed source

See A3. Until a source is identified, `T_transfer` must be expressed as an
interval and its provenance marked as *estimated*, never as provider-reported.

### 7.3 Provider terms constrain the architecture

Retention, redistribution, and training rights are established per provider
before collection, not after. Availability of an API implies nothing about
permitted downstream use.

### 7.4 Recommendations are not instructions

Output is *"based on currently available information, this appears better"* —
never *"you must take this train"*, and never a claim about boarding rights.

---

## 8. Explainability

Every recommendation is explainable from the structured factors that produced it:

```text
Recommended action        Change at Mannheim

Why?
Frankfurt connection      HIGH RISK
Transfer margin           2–4 min   (estimated)
Transfer requirement      6–8 min   (estimated)
Alternative from Mannheim arrives 18:37
Continue to Frankfurt     arrives 19:04
Last realtime update      18 seconds ago
```

Facts, estimates, and predictions are visually and structurally distinct
([`docs/decision-model.md`](docs/decision-model.md) §7). The goal is not to
expose the algorithm; it is to let the
passenger judge whether to trust the recommendation.

---

## 9. Decision-First UX

Under disruption the passenger may be walking, carrying luggage, on a small
screen, on poor connectivity. The hierarchy is:

```text
Recommended action → Destination impact → Risk → Reason → Alternatives → Detail
```

not:

```text
Provider payload → Train metadata → Charts → Raw statistics → Passenger guesses
```

Risk is never communicated by colour alone. Data freshness (`LIVE` / `STALE` /
`UNAVAILABLE`) is always visible; stale data is never presented as live.

---

## 10. Non-Goals

AnschlussPilot will not: replace DB Navigator; be a generic delay tracker; be a
reliability-score site; cover all German public transport; sell tickets; process
payments; manage reservations; sign in to carrier accounts; import tickets by
default; file compensation claims; adjudicate passenger rights; guarantee a
connection outcome; provide indoor station navigation; cover European rail from
the start; become a multimodal super-app; add a chatbot for appearance; deploy ML
without beating a baseline; expose uncalibrated probabilities; collect
unnecessary personal data; present stale data as live; or manufacture certainty.

These are deliberate boundaries. Changing one is a product decision that updates
this file first.

---

## 11. Engineering Order

```text
A5a live competitive benchmark
        ↓
Provider matrix              ← blocks data-dependent work, not A5a
        ↓
A7 identity spike
        ↓
Carrier-conditions + A3 transfer-data checks
        ↓
Corridor graph + observation schema freeze
        ↓
Corridor-wide collector + collection integrity
        ↓
Canonical model + replay harness + deterministic baseline
        ↓
Full Phase 0 protocol freeze
        ↓
Synthetic enumeration + measurement + report    ← Phase 0 ends here
        ↓
Minimal decision UX, then only evidence-justified sophistication
```

Two deliberate choices:

- **The collector follows the minimal corridor and schema freeze, then precedes
  the full domain model.** Schemas can be migrated; elapsed observation time
  cannot be recovered, and neither can data the collector was never scoped to
  capture (§4).
- **Backtesting comes before the UI.** The product claim is about decision
  quality. Prove the policy beats the baseline offline before building a surface
  to display it on.

Cross-cutting: testing, observability, security, privacy, licensing
(`AGENTS.md` **I15**, **I16**).

---

## 12. Success Criteria and Stop Conditions

Qualitative completion criteria cannot be passed or failed, so each criterion
below is a **defined, measurable quantity**.

**No target values are stated yet, deliberately.** A number invented before the
first measurement becomes an anchor that outlives the guess that produced it.
Every target is `TBD` until Phase 0 produces a distribution to set it from. What
is fixed now is the *definition* and the *method* — that is what makes the
target honest when it arrives.

| Metric | Target | How the target gets set |
| --- | --- | --- |
| Operational opportunity rate (A1) | TBD | From the observed distribution over the first full measurement window, clustered by disruption episode |
| Same rate under the `BOUND` scenario (A6) | TBD | Same window, same episodes, candidate set restricted by binding rules — a sensitivity band, not an observation |
| Mean destination-delay improvement vs. deterministic baseline | TBD | Must exceed zero with a stated confidence interval; magnitude set from observed spread |
| Share of opportunities saving ≥ 15 min | TBD | From the observed magnitude distribution |
| Warning lead time — distribution of lead time on actually-missed connections | TBD | Percentile chosen once the achievable ceiling is known (A2 bounds it) |
| False intervention rate — reroutes that ended worse than continuing | TBD | Set against the measured baseline rate, not chosen in the abstract |
| Recommendation reversals per monitored journey | TBD | From baseline volatility under the deterministic policy |
| `UNKNOWN` share of assessments | TBD | Bounded by what A2/A3 make observable; a low target may be unachievable and that is a finding, not a failure |
| Paper-prototype comprehension — participants who correctly state the recommended action | TBD | Exploratory round of 2–3 people sets the threshold; confirmatory scoring uses 5–8 new participants |
| Paper-prototype willingness — participants who say they would act on it | TBD | Same two-stage protocol; interpret jointly with A6 |

### Stop conditions

Fixed **now**, before any data exists — the opposite treatment from the targets
above, and deliberately so. A target chosen after seeing results is meaningless;
an exit chosen after seeing results is worse, because it is indistinguishable
from having no exit at all.

The numbers below are **proposed defaults**. Argue with them and change them
*now*. Changing one after measurement has begun is precisely the failure this
section exists to prevent.

| # | Condition | Verdict |
| --- | --- | --- |
| **S1** | Storage not permitted (A4) | **Hard stop** on Phase 0 as designed. Redesign as ephemeral live evaluation, or change provider. Nothing else proceeds. |
| **S2** | Under the `UNBOUND` scenario: operational opportunity rate **< 3%**, or mean improvement **< 10 min** (A1) | **Stop the intervention product.** If the opportunity is thin even where the passenger is unconstrained, the decision layer is not earning its complexity. Fall back to warning-only, or stop. |
| **S3** | `BOUND` ÷ `UNBOUND` scenario rate **< 1/3** (A6) | **Drop P, continue R.** The research result stands; the product would serve too narrow a population to justify building. |
| **S4** | `UNKNOWN` exceeds **50%** of disrupted transfers at decision time (A2) | **Stop the decision product.** A system that declines to answer more often than it answers is not decision support. |
| **S5** | Transfer requirement cannot be bounded within **±5 min** (A3) | **Narrow** to stations where it can be. If none qualify, S4 applies. |
| **S6** | A competing tool meets all four decision-grade criteria on a majority of **all** cases and a majority of `REROUTE_EARLY` cases (A5a), or overall coverage rises by at least **20 percentage points** and `REROUTE_EARLY` coverage also rises (A5b) | **Repivot.** The reason to exist is going or gone. Find a narrower gap, move to B2B2C, or stop. Report raw numerators / denominators; criteria and thresholds are in [`docs/market-and-validation.md`](docs/market-and-validation.md) §2. |
| **S7** | Any deliverable exceeds **2×** its estimate (§4) | **Re-scope**, do not push through. |
| **S8** | Phase 0 incomplete at **2×** its calendar estimate (§4) | **Stop and publish what exists.** A partial measurement honestly reported has value; an unfinished one has none. |
| **S9** | Service-run linkage fails or is ambiguous for **> 5%** of runs, and cannot be reduced (A7) | **Redesign or stop.** Delay evolution and counterfactual labels are unreliable above this rate. Switch to a provider with stable journey identifiers, or stop — do not proceed and hope. |

Reasoning behind the two most contestable numbers, so they can be argued with:

- **S2 — 3% and 10 min.** Below roughly one enumerated transfer in thirty, the
  opportunity is too sparse for a decision layer to be worth its complexity;
  below ~10 minutes saved, the advice sits inside the noise of the estimates
  producing it. Both are judgement calls, not derivations.
  **Note what this is not:** 3% is a rate over *enumerated* itineraries, not over
  journeys real passengers take. It cannot be converted into "a passenger sees
  this once a year" without demand weighting the project does not have
  ([`docs/modelling-and-evaluation.md`](docs/modelling-and-evaluation.md) §2).
- **S4 — 50%.** Chosen because it is the point at which the honest answer becomes
  the *typical* answer. A tool whose usual output is "I cannot tell you" may still
  be correct (`AGENTS.md`, *When Priorities Conflict*) — but it is a different
  product, and should be
  chosen deliberately rather than arrived at.

- **S9 — 5%.** Above roughly one run in twenty, mis-linked observations stop being
  noise and start shaping the delay-evolution statistics that everything else is
  derived from. The threshold matters less than the fact that it is checked on
  day one rather than discovered in week five.

**A stop is not a failure.** S3, S5, S6 and S8 all leave a publishable research
result intact. Only S1 forecloses everything, and it is knowable before a single
line of collector code is written.

### Advancement rule

Stop conditions cover failure. The `TBD` targets cover clear success. **Neither
covers the most likely outcome**, which looks roughly like:

```text
policy beats baseline by ~4 min
clustered confidence interval wide
one corridor, few independent episodes
UNKNOWN around 35%
```

No stop condition fires. No target is met, because none is set yet. Without a
rule, this result gets argued about indefinitely — which in practice means the
project drifts rather than decides.

> **Proceed to P-minimal if — and only if — no stop condition has fired, and the
> author, having read the finished report, would use the system on their own next
> journey.**

The second clause is deliberately a judgement, not a metric. For a single-author
project it is both honest and hard to fake: it forces the report to be read as a
user rather than as its writer. It is also free, and it is exactly what
Phase 0.5 tests at greater cost.

If the answer is *"not yet, but with X fixed I would"* — then X is the Phase 0.5
scope, written down before starting it.

---

Additionally, the MVP is not complete unless the system:

- represents `A → B → C` as leg / connection / leg and updates it correctly as
  operational state changes;
- distinguishes delay, correction, cancellation, partial cancellation, platform
  change, stale data, and missing data;
- preserves service identity across changed numbers and split/join services;
- never uses information that post-dates the decision timestamp in evaluation;
- degrades to `UNKNOWN` rather than to `SAFE` when a provider fails.

---

## 13. Privacy, Legal, Licensing

The core product requires no account, no name, no email, no carrier credentials,
no ticket upload, no payment data, and no continuous GPS history. Anything that
introduces personal data is a deliberate decision with purpose, legal basis,
retention, and deletion defined first.

AnschlussPilot is an independent project and implies no Deutsche Bahn
affiliation.

**This repository currently has no `LICENSE` file.** Until one is added, no
usage rights are granted. Provider data terms are documented alongside the
provider code once integrations exist.

---

## 14. Development

There are no installation or run instructions because there is nothing to
install or run. This section will document the verified stack, environment,
commands, and tests once they exist — see the *Definition of Done* in
`AGENTS.md`.

> **Note on formatting:** the display formulas in §6 are written on single lines
> on purpose. Multi-line LaTeX in this file was previously corrupted by a
> Markdown formatter interpreting `=` and `-` continuation lines as setext
> headings. Keep display math on one line, or disable embedded formatting.

---

## 15. Disclaimer

Schedules, realtime observations, estimates, predictions, comparisons, and
recommendations may be delayed, incomplete, unavailable, uncertain, or wrong.

AnschlussPilot is not an official railway service, not a transport guarantee, not
a guarantee that a connection will succeed, not a statement that a service may
legally be boarded, and not authoritative fare or passenger-rights advice.

Verify critical travel decisions against official information.

---

## Vision

Not *"your journey is going wrong."*

> **Observe the disruption. Understand the risk. Compare the options.
> Act while it still changes the outcome.**
