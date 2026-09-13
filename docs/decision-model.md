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

`T_transfer` has **no published per-station source** (see `README.md` A3). A
published *computation rule* exists; see the S5 desk check below. Until a
station-level bound is computed from it, `T_transfer` is an interval with
provenance `estimated`.

### S5 desk check (D051) — 2026-09-13

**Status: executed at desk level. At the desk stage S5 had neither fired nor
passed; see *Bounds computed* below — S5 does not fire at either shortlisted
station.** This check
reads the transfer threshold as *bounded within ±5 min*, meaning an interval no
wider than 10 min.

**Sources read on 2026-09-13:**

1. **gtfs.de free schedule feeds** (`de_fv`, `de_rv`): no `transfers.txt`.
   This was verified from the file list of the D054 snapshot. The
   [services page](https://gtfs.de/en/services/) lists `transfers.txt` only
   for the paid feeds.
2. **DB InfraGO Richtlinie 402.0203A01** —
   [*Planungsprocedere; Aufgaben und Abläufe im Planungsprocedere für den Netzfahrplan*](https://www.dbinfrago.com/resource/blob/13175496/f347b99ef9b749b4a6ce8c23a978d23f/Ril-402-0203A01-INB-2026-data.pdf),
   valid from 14.12.2025. `verified`. It defines how every *Übergangszeit* is
   built. In paraphrase:
   - **Distance:** one third of the arriving train's platform *Nutzlänge*
     (usable length), plus one fifth of the departing train's, plus the
     distance between the two platforms. It is walked at 4.68 km/h
     (78 m/min).
   - **Quality minutes:** 1 min at every station, and a possible +1 min at
     category 1 and 2 stations with unfavourable conditions.
   - **Stairs:** a fixed +1 min.
   - **Local adjustment:** up to ±1 min.
   - **Same-platform transfer:** 2 min *"zuzüglich der Qualitätsparameter nach
     (4) Nr. 3"*. Section (4) lists only items 1 and 2, so this reference is an
     inconsistency in the source, left unresolved here.
   - **S-Bahn systems:** quality parameter 0.
   - **Override:** stable values from simulation or measurement may replace
     the computed ones.
3. **Per-station values are not published.** None appear on the
   [Kursbuch reading guide](https://kursbuch.bahn.de/hafas/kbview.exe/dn?rt=1&mainframe=AI_KB_lesen),
   and no public list was found. This absence is `requires verification`,
   limited to the sources searched.
4. **DB InfraGO
   [Stationspreisliste 2026](https://www.dbinfrago.com/resource/blob/13518698/1cd204bc2c7a98b2490822ee6fc200ad/Stationspreisliste-2026-data.pdf)**
   (valid from 01.01.2026) gives these price classes:
   - class 1: Nürnberg Hbf, München Hbf;
   - class 2: Augsburg Hbf, Würzburg Hbf, Ingolstadt Hbf, Regensburg Hbf.

   The Ril refers to *"Kategorien 1 und 2"* under the INPB. Only secondary
   sources say that this means today's *Preisklasse*, so that link is
   `requires verification`.
5. **DB InfraGO OpenStation.** Two primary sources state the data licence is
   CC0: the
   [openstation-docs README](https://github.com/dbinfrago/openstation-docs)
   and the
   [DB API Marketplace product page](https://developers.deutschebahn.com/db-api-marketplace/apis/product/open-station).
   - **Access:** per the README, the Mobilithek bulk NeTEx file downloads
     without credentials. The Marketplace route needs registration, which
     **D044** excludes.
   - **What the public example shows** (Saarbrücken Hbf): a `Length` on every
     platform and platform edge.
   - **What it lacks:** platforms carry no coordinates, and path segments
     (`SitePathLink`) carry no length. The docs say the indoor graph is still to
     be recorded.
   - **Open point:** whether `Quay/Length` equals the Ril's *Nutzlänge* is
     `requires verification`.
   - **Mobilithek terms:** the terms-of-use page did not render on 2026-09-13,
     so under D044 no bulk download has been made.
6. **gtfs.de `stops.txt` geometry:** most major stations have 1–3 child stops
   and no platform codes, so it is unusable as a distance input.

**What the rule implies.** This is illustrative arithmetic, not a measurement,
and the inter-platform distances are assumed:

| Case | Arithmetic | Result |
| --- | --- | --- |
| Lower end, most permissive reading | Same-platform 2 min, minus the 1 min local adjustment | 1 min |
| Class 2 station: 400 m platforms, up to 150 m platform change, every optional minute | (133 + 80 + 150) m ÷ 78 m/min ≈ 4.7 min, plus 4 min | ≈ 8.7 min |
| Large node: the same, with a 400 m platform change | (133 + 80 + 400) m ÷ 78 m/min ≈ 7.9 min, plus 4 min | ≈ 11.9 min |

- The class 2 case gives `[1, 8.7]` min, which is within ±5 min.
- The large-node case gives `[1, 11.9]` min, which is outside ±5 min.

**Verdict.**
- **S5 has not fired.** A published rule bounds `T_transfer` within ±5 min at
  compact stations, given platform lengths (CC0) and an inter-platform
  distance.
- **S5 has not passed.** No source read contains the inter-platform distance,
  so any named station's bound currently rests on an assumed distance.
- **Consequence for station choice:** prefer compact class 2 stations over
  large class 1 nodes, where the upper end probably breaks ±5 min.

**Candidate stations** from the frozen Phase 0A v2 seed list. Schedule counts
come from the gtfs.de `de_fv` / `de_rv` feeds (DELFI e.V. data, CC BY 4.0),
retrieved 2026-09-13. The weekday is Tuesday 2026-09-15, 16:00–20:00. Pairs
count a long-distance arrival and a regional departure 5–30 min apart. That
window and that gap band are illustrative; both are still `UNSET` in the
protocol.

| Station | Price class | Long-distance arrivals | Regional departures | Pairs in band |
| --- | --- | --- | --- | --- |
| München Hbf | 1 | 57 | 216 | 1,280 |
| Nürnberg Hbf | 1 | 41 | 270 | 1,143 |
| Augsburg Hbf | 2 | 23 | 143 | 323 |
| Würzburg Hbf | 2 | 21 | 86 | 188 |
| Ingolstadt Hbf | 2 | 8 | 65 | 48 |
| Bamberg | not extracted | 6 | 75 | 48 |
| Rosenheim | not extracted | 4 | 57 | 25 |

- **Regensburg Hbf** matched no long-distance arrival in the window. This was
  not investigated.
- **Long-distance realtime coverage** at these stations was observed once, in
  the private D054 snapshot. München, Nürnberg, Augsburg and Würzburg carried
  most of their running long-distance trips at that instant; Bamberg and
  Rosenheim carried few, from very small samples. The figures stay private
  ([`provider-evaluation.md`](provider-evaluation.md) §2.4).

**Proposed next steps** (see **D055**):
1. Read Mobilithek's terms of use.
2. If they permit it, download the OpenStation NeTEx bulk file once and
   extract the platform lengths for Augsburg Hbf and Würzburg Hbf.
3. Decide how to bound the inter-platform distance, which has no published
   source.
4. Run the `README.md` §4 sizing gate for the chosen station.

#### Bounds computed — 2026-09-13 (D055, accepted)

**Status: S5 does not fire at either shortlisted station.** Both conservative
intervals are no wider than 10 min. The station choice stays open because the
sizing gate cannot be run yet (see below).

**Inputs**, all read or retrieved on 2026-09-13:

- **Platform lengths and station categories:** DB InfraGO OpenStation NeTEx
  bulk file, CC0, publication timestamp 2026-09-13T02:31:47Z.
  - Downloaded once via `bahnhof.de/daten/netex`, which redirects to the
    Mobilithek `noauth` endpoint, with no credentials.
  - Kept privately under `.private/` with its SHA-256.
- **Inter-platform distances:** OpenStreetMap, via four read-only Overpass
  queries (OSM base 2026-09-13T11:28Z). Contains information from
  [OpenStreetMap](https://www.openstreetmap.org/copyright), which is made
  available here under the
  [Open Database License (ODbL)](https://opendatacommons.org/licenses/odbl/1-0/).
  - The ODbL was read in full first. A distance printed here is a *Produced
    Work*: it needs the §4.3 notice, and §4.5(b) means no share-alike.
  - The raw responses stay private.
- **Mobilithek platform terms: not read.** The terms page rendered empty on two
  attempts. The download went ahead on DB InfraGO's CC0 statement for the data,
  by the author's decision under D044. The platform-terms gap stays open.

**Correction to item 4 above.** OpenStation carries two separate fields,
`DBINFRAGO_STATION_CATEGORY` and `DBINFRAGO_PRICE_CATEGORY`:

| Station | Station category | Price category |
| --- | --- | --- |
| Augsburg Hbf | **1** | 2 |
| Würzburg Hbf | 2 | 2 |

- The Ril's *"Kategorien 1 und 2"* therefore most plausibly means the
  **station** category, not the price class. This is still an interpretation,
  but a primary source now shows the two fields differ.
- The arithmetic is unchanged: both stations get the optional extra minute
  under either reading.

**Method** (conservative by construction):

- The longest platform edge at the station is used for **both** the one-third
  arriving share and the one-fifth departing share: Augsburg 457 m, Würzburg
  443 m.
- **Inter-platform distance:** the largest edge-to-edge distance between any
  two train platforms or platform edges mapped in OSM.
- **Minutes:** every optional and fixed minute is applied — quality 1,
  category extra 1, stairs 1 and local +1, so 4 min.
- **Lower end:** 1 min, the most permissive same-platform reading above.
  Under the 2 min reading, every width below shrinks by 1 min.

| Station | Farthest pair (OSM) | Walk | Interval | Width | S5 |
| --- | --- | --- | --- | --- | --- |
| Augsburg Hbf — all train platforms, including short platforms 101 / 501 / 801 / 901 | 501 ↔ 801, 293 m | 6.9 min | [1, 10.9] min | 9.9 min | does not fire — **0.1 min inside the limit** |
| Augsburg Hbf — main platforms 1–12 only | 2 ↔ 12, 62 m | 3.9 min | [1, 7.9] min | 6.9 min | does not fire |
| Würzburg Hbf — all edges, including Gleis 1 | 1 ↔ 11, 83 m | 4.1 min | [1, 8.1] min | 7.1 min | does not fire |

**What this does not establish:**

- **Augsburg's conservative case sits at the threshold.** The 293 m pair is two
  short platforms at opposite ends of the station, so the distance is mostly an
  along-track offset that the length shares partly count again. Whether in-scope
  long-distance or regional trains use those short platforms is
  `requires verification`; the free feeds carry no platform codes at Augsburg.
- Whether `Quay/Length` equals the Ril's *Nutzlänge* is still
  `requires verification`.
- **The Ril gives a *planning* transfer time.** Using it to bound the time a
  passenger actually needs is D055's modelling assumption, not a measurement.
- **OSM geometry is contributor-mapped.** Its metre-level error is not
  quantified.

**Sizing gate — no verdict possible yet.** `README.md` §4 asks whether the
chosen scope yields the episode counts in
[`modelling-and-evaluation.md`](modelling-and-evaluation.md) §2 within half the
calendar ceiling. Two inputs are missing:

1. **Episode rules are unfrozen.** The **minimum episode count** and the episode
   windows `W_primary` / `W_narrow` / `W_wide` are `UNSET` in the protocol
   manifest. The author must freeze them before any station can be sized
   against them.
2. **No episode rate is known** for either station. One realtime snapshot
   cannot supply it.

The only comparison available today is timetable volume: weekday 16:00–20:00
transfer pairs above, Augsburg 323 and Würzburg 188. That is a proxy for
enumerated transfers, not for independent episodes.

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

**That rule describes runtime.** Measurement classifies whole itineraries by a
separate rule: an itinerary with any non-`evaluable` candidate produces **no
opportunity / no-opportunity verdict at all** and is counted as uncovered
([`phase0-protocol.md`](phase0-protocol.md) §2). The product still recommends
among what it can see; the measurement declines to conclude. Applying the runtime
rule to measurement would silently record an unobserved candidate as evidence
that no better option existed.

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
