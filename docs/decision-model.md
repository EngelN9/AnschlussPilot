# Decision Model

Detail for [`AGENTS.md`](../AGENTS.md) §1 (I3, I4, I10, I11, I14). Read before
touching the risk engine, alternative generation, outcome estimation, or the
decision engine.

> **Update trigger:** revise when the risk vocabulary, the candidate action set,
> outcome dimensions, executability handling, or stability mechanisms change.
> Checked in `AGENTS.md` §4.

---

## 1. Reasoning Chain

```text
Current journey → Railway observations → Canonical current state
→ Connection feasibility → Reasonable actions → Outcome estimation
→ Action comparison → Decision recommendation → Continue monitoring
```

A risk score is an intermediate result. An alternative itinerary is an
intermediate result. **The product output is the comparison.**

---

## 2. Domain Concepts

Likely concepts, to be introduced only when an actual requirement needs them —
not speculatively:

```text
Station          Journey        JourneyLeg      Connection
ServiceRun       Stop           ScheduledStopEvent
RealtimeObservation             CurrentServiceState
Disruption       RiskAssessment AlternativeJourney
DecisionCandidate               OutcomeEstimate
DecisionRecommendation
```

These model railway semantics, not external API response shapes.

---

## 3. Connection Feasibility

Not `incoming_delay > scheduled_transfer_time`. Starting point:

$$B_{\text{eff}} = T_{\text{dep,next}} - T_{\text{arr,cur}} - T_{\text{transfer}} - T_{\text{safety}}$$

Feasibility also depends on outgoing-service delay, cancellation state, platform
changes, the *range* rather than point value of transfer time, station topology,
changed stop patterns, and whether the underlying information is fresh enough to
use at all.

`T_transfer` currently has **no confirmed data source** (see `README.md` A3).
Until it does, it is an interval with provenance `estimated`.

Assessment must remain explainable in terms of the factors that produced it.

---

## 4. Risk Vocabulary

```text
SAFE
ATTENTION
HIGH_RISK
MISSED_OR_UNAVAILABLE
UNKNOWN
```

Stable domain meanings. No localized UI wording inside domain logic. No implicit
mapping from a risk state to an action.

---

## 5. Candidate Actions

```text
CONTINUE_CURRENT_PLAN
WAIT_FOR_CONNECTION
REROUTE_EARLY
TAKE_LATER_CONNECTION
USE_ALTERNATIVE_RAIL_ROUTE
NO_RELIABLE_RECOMMENDATION
```

Three constraints:

- **The candidate set stays small.** Do not enumerate every itinerary routing can
  produce. Operationally reasonable options only.
- **`CONTINUE_CURRENT_PLAN` is always present.** Without the do-nothing baseline
  there is no answer to the question the product exists to answer.
- **Every candidate carries an executability annotation.** A route the passenger
  cannot take is not a better route.

### Executability

Ticket binding (*Zugbindung*) can make an operationally better candidate one the
passenger is not free to take.

```text
TicketConstraint   UNBOUND | BOUND | UNKNOWN     — passenger-supplied, default UNKNOWN
Executability      executable | possibly-not-executable | unknown
```

Rules:

- `TicketConstraint` is **only ever a user input**. Never infer it from fare
  data, provider fields, or context.
- `UNKNOWN` binding ⇒ `unknown` executability. Never `executable`
  (`AGENTS.md` I4, I14).
- Annotation, not exclusion: a `possibly-not-executable` candidate may still be
  shown, labelled as such. The system does not decide what the passenger is
  allowed to do.
- Output wording asserts nothing legal — *"may not apply to your ticket"*, never
  *"you are entitled"* or *"you are not permitted"*.

Outcome comparison (§6) must be able to report results with and without
non-executable candidates, so the policy is never credited for advice the
passenger could not follow.

**Phase 0 has no passenger to supply this input.** Evaluation there enumerates
the scenarios instead — compute the candidate set once under `UNBOUND` and once
under `BOUND`, and report both (`modelling-and-evaluation.md` §2, §8). The
passenger-supplied input above is the mechanism from **Phase 0.5 onward**, when
a real user exists. The rules in this section describe the runtime behaviour;
they are not a claim that Phase 0 observes binding status.

### Actionability and the decision window

A candidate is not defined only by *what* it is, but by *when it can still be
taken*. The best alternative in the timetable is worthless to a passenger who has
twenty seconds to reach a door.

**Actionability belongs in the domain model, not in the interface.** A UI that
hides an expired option is patching a domain error — the decision layer should
never have preferred it.

Each candidate carries:

```text
available_from            earliest moment the action becomes possible
latest_action_time        last moment it can still be taken
                          (e.g. alighting station departure − alighting buffer)
decision_margin           latest_action_time − now
window_state              NOT_YET | ACTIONABLE | CLOSING | EXPIRED
```

Rules:

- `EXPIRED` candidates are never recommended, and never counted as alternatives
  that were "available".
- `CLOSING` is surfaced, not silently downgraded — *"you have about 3 minutes to
  decide"* is often the most useful thing the system can say.
- `decision_margin` is an estimate with the same uncertainty discipline as
  everything else (§3, `AGENTS.md` I5). It is a range when it cannot be pinned.
- Alighting requires physically getting off a moving service: the window closes
  before the departure time, not at it.

Candidate evidence also carries the Phase 0 coverage state from
[`phase0-protocol.md`](phase0-protocol.md): `evaluable`,
`unobserved_out_of_scope`, or `insufficient_data`. Only `evaluable` candidates
enter outcome comparison. The other states remain visible to measurement as
unknown coverage; they are never converted into evidence that no alternative or
opportunity existed.

> **Consequence for measurement.** An "opportunity" whose action window had
> already closed at decision time **is not an opportunity** — it is hindsight.
> The A1 measurement must require a non-`EXPIRED` window at the evaluated
> decision time, which will reduce the measured rate relative to a naive
> definition. That reduction is a correction, not a loss
> (`modelling-and-evaluation.md` §2).

### Earlier intervention

Do not architecturally assume rerouting can only happen at the planned transfer
station. Identifying that a journey should change *before* the threatened
transfer is a distinct capability and a main reason the product exists.

**This is also where the ticket constraint bites hardest.** Binding is typically
relieved only after a delay threshold is reached — after the window in which
early intervention is worth anything. The product's most differentiated action
and its main legal constraint overlap almost exactly. Treat
`REROUTE_EARLY` + `BOUND` as a first-class case, not an edge case
(`README.md` A6).

---

## 6. Outcome Estimation

Evaluate candidates on passenger-relevant dimensions where data permits:

```text
expected destination arrival     destination delay
connection feasibility           additional transfers
journey complexity               uncertainty
```

Do not reduce comparison to *earliest scheduled arrival* in a product whose stated
goal is reliability.

Counterfactual framing is required: *what happens if the passenger continues* vs.
*what happens if the passenger changes*. Never label an alternative "better"
without stating what it is better than.

### Do not collapse this into a utility score yet

A weighted formula such as `U = −1.7 × delay − 8.4 × transfers − …` looks
rigorous and is fabricated precision until real preference data exists. Those
coefficients would encode a guess about how passengers trade minutes against
transfers — the exact kind of unjustified exactness `AGENTS.md` **I5** forbids
elsewhere.

**Phase 0 uses an explainable conservative ordering instead**, applied as
successive filters rather than a sum:

```text
1. executability        admissible under the binding scenario   (§5)
2. actionability        window not EXPIRED                      (§5)
3. feasibility          the connection can actually be made     (§3)
4. material improvement arrival better by more than a threshold
5. added cost           extra transfers, complexity
6. uncertainty          prefer the better-evidenced option
7. intervention bar     change only if the margin clears it     (§8)
```

Each step is separately explainable to a passenger, which the weighted sum is
not. A single utility function becomes appropriate once there is preference
evidence to fit it to — and the `argmax E[U(Y)]` formulation in
[`README.md`](../README.md) §6 remains the stated long-term direction, not the
Phase 0 policy.

---

## 7. Separate Facts From Estimates From Predictions

These are different categories and must stay distinguishable in the output
structure, not merely in prose:

```text
scheduled fact                  Platform 7 (provider-reported)
provider realtime observation   +8 min (observed 18:42:05)
passenger-supplied constraint   ticket binding: BOUND (self-reported)
derived state                   connection buffer 2 min
deterministic estimate          transfer requirement 4–6 min
probabilistic prediction        missed-connection risk 72%
decision recommendation         reroute at Mannheim
```

Blurring these layers is the fastest way to make the product untrustworthy.

---

## 8. Stability

Realtime state fluctuates. A system that says *reroute / continue / reroute /
continue* every few seconds is broken even if each individual answer is
defensible.

Mechanisms to consider when the problem appears: minimum improvement thresholds,
confidence requirements, hysteresis, cooldowns, persisted recommendation state,
and recorded reasons for change.

Recommendation stability is a measured product property — see `README.md` §12.

---

## 9. False Interventions

An unnecessary reroute can make the journey worse. The policy must weigh the cost
of unnecessary rerouting, additional transfers, longer travel, unstable advice,
and increased uncertainty — not only the cost of a missed connection.

A false intervention is a real failure mode, tracked as its own metric.

---

## 10. Explainable Changes

When the preferred action changes, the system should be able to name the material
reason:

```text
outgoing train is now delayed        alternative was cancelled
incoming delay increased             platform changed
transfer requirement increased       data became stale
```

Opaque recommendation flips are a defect.
