# Provider Evaluation

Phase 0 data-dependent-work blocker (`README.md` §4). Until the matrix below is
filled from primary sources, no collector may retain data and no capability may
be described as supported (`AGENTS.md` I2, I15). This does not block A5a.

This matrix evaluates **data providers only**. It does not establish carrier,
ticket-issuer, fare-validity, binding-relief, or passenger-rights conditions.
Those require the independent carrier-conditions evidence described in
[`binding-scenarios.md`](binding-scenarios.md) §3.

> **Status: PARTIAL EVIDENCE v1 — RIGHTS GATE BLOCKED.** Four DB products have
> been checked against current DB primary sources. Public pages establish some
> capabilities, access conditions and licences, but do not establish usable
> retention and downstream-use rights for the decisive RIS data. `UNKNOWN`
> means *not publicly answered*, never *probably fine*. No account, paid plan,
> provider contact or partner application was used in this review.

> **Update trigger:** re-verify whenever a provider changes terms, when adding a
> provider, and at minimum before any change to what the collector retains.
> Checked in `AGENTS.md` §4.

---

## 1. Column Definitions

| Column | Question being answered |
| --- | --- |
| Realtime | Does it expose live operational state, and at what update granularity and latency? |
| Platform | Are platform (`Gleis`) values exposed, and are changes exposed? |
| Hold signal | Is dispatcher connection protection (*Anschlusssicherung* — whether the outgoing service is being held) exposed in any form? **This bounds A2.** |
| Identity | Is there a stable journey identifier, or only train number + date? **This bounds service identity work (A7).** |
| Transfer times | Are station minimum transfer times (*Mindestumsteigezeit*) available? **This is the only identified candidate source for A3.** |
| Topology | Any platform-pair, level-change, or walking-distance data? Determines whether `T_transfer` can be narrowed below station-level constants. |
| Storage | May responses be persisted beyond immediate display, and for how long? |
| Redistribution | May derived or raw data be shown to third parties / published? |
| Training | May retained data be used to train statistical or ML models? |
| Commercial | May it be used in a commercial or monetized product? |
| Attribution | Is attribution required, and in what exact form? |
| Rate limits | Requests per interval; is a corridor-wide poll at the intended cadence feasible? |
| Access | Registration, key, approval process, cost |
| Cost | Published price or `UNKNOWN` / price on request |
| Revocability | Whether continued access is contractually protected or can be withdrawn |
| Source | URL of the terms consulted + date consulted |

---

## 2. Matrix

```text
Provider snapshot ID     db-public-primary-sources-2026-08-11-v1
Verdict version          v1-partial
Terms effective date     UNKNOWN — the consulted pages state no effective date
Terms checked date       2026-08-11
```

Once verified, these values and the chosen retention / payload policy are copied
into [`phase0-protocol.md`](phase0-protocol.md). Until then the collector gate is
closed.

### 2.1 Capability and access evidence

| Product and version | Realtime | Platform | Hold signal | Service identity | Transfer / topology | Rate limits | Access and cost | Source, checked 2026-08-11 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **RIS::Connections** product `1.0.208`, API `1.18.0` | Current forecasts, disruption state and connection status are inputs; public page gives no latency guarantee | Yes — platform-precise connection assessment | **Yes** — waiting / not-waiting disposition status | Request uses `journeyID` + `arrivalID`; cross-observation stability remains unverified pending A7 | Platform-precise transfer times, indoor-routing inputs where available, fallback corporate transfer rules and station-area transfers | 100 req/s plus 5k–500k requests/day by plan | Positive review; **only DB sales partners**; paid, price on request | [Marketplace product](https://developers.deutschebahn.com/db-api-marketplace/apis/product/ris-connections-transporteure); [official API guide](https://developer-docs.deutschebahn.com/doku/apis/ris-connections-10686902) |
| **RIS::Journeys** product `1.0.273`, API `2.12.0` | Yes — scheduled and forecast arrivals/departures, cancellations and journey changes; no public latency guarantee | Yes — platforms / bus bays | Not documented | `journeyID` and match/find endpoints exist; stability remains unverified pending A7 | No transfer-time or station-topology claim | 100 req/s plus 5k–500k requests/day by plan | Positive eligibility review; paid, price on request | [Marketplace product](https://developers.deutschebahn.com/db-api-marketplace/apis/product/ris-journeys-transporteure); [official API guide](https://developer-docs.deutschebahn.com/doku/apis/ris-journeys-10582266) |
| **Timetables** product/API `1.0.274` | Yes — planned timetable plus current changes at a station; no public latency guarantee | `UNKNOWN` from the consulted public product page | Not documented | Station-scoped plan/change records; stable cross-station journey identity not established | No transfer-time or topology claim | 60 requests/minute | Marketplace registration/subscription; free plan | [Marketplace product](https://developers.deutschebahn.com/db-api-marketplace/apis/product/timetables) |
| **RIS::Stations** product `1.29.3446`, API `1.29.1.1` | Primarily versioned station master data, not journey realtime | Yes — platform structures and sectors | Not applicable / not documented | Station, stop-place and platform keys; not a journey-identity source | Transfer times by traveller type, transfer areas, platform structure; official guide permits initial storage and incremental refresh of station master data | Test: 10 req/s and 10k/month; paid plans: 100 req/s and 150k–15m/month | Positive eligibility review; free test up to two months; paid plans EUR 4,200–84,000/year | [Marketplace product](https://developers.deutschebahn.com/db-api-marketplace/apis/product/ris-stations); [official API guide](https://developer-docs.deutschebahn.com/doku/apis/ris-stations-10686906) |

`Not documented` means the consulted primary source makes no claim for that
field. It is not evidence of absence. Version numbers are snapshots, not a
compatibility commitment.

### 2.2 Rights evidence

| Product | Storage | Retention | Redistribution | Training | Commercial use | Attribution | Revocability |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **RIS::Connections** | `UNKNOWN` — contract required | `UNKNOWN` — contract required | `UNKNOWN` — contract required | `UNKNOWN` — contract required | `UNKNOWN` — contract required | `UNKNOWN` — contract required | `UNKNOWN` — approval and contract required; no public continuity promise |
| **RIS::Journeys** | `UNKNOWN` — contract required | `UNKNOWN` — contract required | `UNKNOWN` — contract required | `UNKNOWN` — contract required | `UNKNOWN` — contract required | `UNKNOWN` — contract required | `UNKNOWN` — approval and contract required; no public continuity promise |
| **Timetables** | **Permitted for the CC BY 4.0 licensed dataset**; reproduction is licensed | No published retention cap found | **Permitted with CC BY 4.0 conditions** | `UNKNOWN` — model training is not expressly addressed; no legal interpretation is assumed | **Permitted by CC BY 4.0**, subject to attribution and other applicable rights | Preserve supplied creator/copyright/licence/disclaimer/source information, indicate modifications and link the licence when sharing | CC BY 4.0 grant is irrevocable while its conditions are followed; API availability itself has no public continuity commitment |
| **RIS::Stations** | Official guide expressly supports storing and incrementally updating station master data; rights for the transfer/topology subset remain `UNKNOWN` pending contract/scope confirmation | `UNKNOWN` for the contract-governed API and transfer/topology subset | CC BY 4.0 applies to stated *Stationswissen*; exact inclusion of transfer/topology fields is `UNKNOWN` | `UNKNOWN` — not expressly addressed and licence scope is unresolved | `UNKNOWN` for the transfer/topology subset; contract required | CC BY 4.0 attribution for covered *Stationswissen*; OpenStreetMap attribution also applies to state-boundary data | `UNKNOWN` for API/contract access; the CC BY grant for material actually covered remains irrevocable while compliant |

The CC BY conclusions above use the
[CC BY 4.0 legal code](https://creativecommons.org/licenses/by/4.0/legalcode.en)
linked by DB: it permits reproduction, sharing and adapted material, including
relevant database-right uses, subject to attribution. It grants only rights the
licensor is authorised to grant; it does not settle privacy, trademark,
third-party or contract scope. That is why training and the boundary of
RIS::Stations *Stationswissen* remain `UNKNOWN` rather than inferred.

---

### Verified finding — the decisive signals exist, behind a partner gate

Re-checked 2026-08-11:
[DB API Marketplace — RIS::Connections (DB Transporteure)](https://developers.deutschebahn.com/db-api-marketplace/apis/product/ris-connections-transporteure)

| Field | What the product page states |
| --- | --- |
| Hold signal | *"Informationen ob Anschlüsse `warten` oder `nicht warten`"* — **A2b true** |
| Transfer times | *"gleisscharfe Umsteigezeiten"*, with a personalized reachability assessment for occasional travellers, commuters and mobility-impaired travellers — **A3b true** |
| Access | *"Zugang erfolgt nach positiver Prüfung **ausschließlich für Vertriebspartner der Deutschen Bahn AG**"* |
| Pricing | *"Kostenpflichtig (Preis auf Anfrage)"* |
| Terms | *"Nutzungsbedingungen werden vertraglich vereinbart"* — no published standard terms |

**What this settles and what it does not.** The two assumptions most likely to
cap this project — hold signals (A2) and platform-level transfer times (A3) —
are not physically unobservable. They exist, in a documented feed. The open
question is **A2c / A3c: whether this project may use them.** Access is limited
to DB sales partners after review, priced individually, and governed by a
negotiated contract rather than public terms.

Three consequences:

1. **The access question outranks the modelling questions.** It is investigated
   in parallel with A5a (`README.md` §4), not deep inside Phase 0.
2. **Any `UNKNOWN` rate this project measures is tier-relative.** It reflects the
   feeds obtainable here, not a limit of German rail — see `README.md` A2 and
   **S4**.
3. **Revocability becomes a first-order risk** (**S11**). A moat built on access
   the incumbent grants and can withdraw is not a moat, and it points the
   strategy toward B2B2C or research (**S12**).

The matrix above now records the public evidence for four products. It still
does not identify an obtainable decisive-signal feed with sufficient rights;
that missing contract/access evidence keeps the gate closed.

---

## 3. What Each Answer Decides

This is the part that makes the matrix worth filling. Write the verdict, not just
the facts.

| Finding | Consequence |
| --- | --- |
| **Storage not permitted** | Phase 0 cannot be built as designed. The historical store, backtesting, and the entire moat argument (`README.md` §3 A4) collapse. Fallback is ephemeral live evaluation with no reconstruction — a different, weaker product. **Decide this before writing the collector.** |
| **Storage permitted, retention capped** | Collector needs enforced retention and deletion from day 1. A cap shorter than the frozen measurement window in [`phase0-protocol.md`](phase0-protocol.md) may make A1 unmeasurable. |
| **Training use not permitted** | The ML roadmap in [`modelling-and-evaluation.md`](modelling-and-evaluation.md) §5 is dead for this provider. Deterministic policy only. This is survivable — say so explicitly rather than leaving it ambiguous. |
| **Redistribution not permitted** | Constrains what the UI may show and rules out publishing datasets or examples. Affects §9 of the README. |
| **Hold signal absent** | A2 fails or is severely bounded. Expect a high `UNKNOWN` rate and set the §12 target accordingly. **This is the finding most likely to cap the product's ceiling.** |
| **No stable journey identity** | Service identity work becomes heuristic. Budget for it and test it explicitly (`testing-catalogue.md` §4). The identity spike (A7) measures how bad it actually is; **S9** is the exit. |
| **No transfer-time or topology data** | A3 has no source. `T_transfer` stays a wide interval, pushing cases toward `ATTENTION`/`UNKNOWN`. **S5** applies if it cannot be bounded within ±5 min. |
| **Rate limits below corridor-wide polling** | Forces a narrower corridor or a slower cadence, which directly reduces achievable warning lead time. Recompute the Phase 0 scope. |
| **Attribution required** | Becomes a UI requirement, not a footnote. Record the exact required wording. |
| **Commercial use prohibited** | Fine for Phase 0. Record it so it is not discovered later after building on it. |

---

## 4. Verdict

> **Verdict v1-partial: BLOCKED.** Public primary sources establish useful
> capabilities, but they do not establish that AnschlussPilot is eligible for
> RIS::Connections or may retain its hold/connection data for longitudinal
> replay. No Phase 0 provider is selected. A7, polling and collector work remain
> prohibited.

The project pursues a research result (**R**) and a product prototype (**P**) in
sequence (`README.md` §4). **Their permission requirements differ, so the verdict
answers both separately.** The same terms check produces both answers at no extra
cost, and finding that only R is permitted is a useful result, not a failure.

### 4.1 Is R permitted?

Requires: retention long enough to measure, and use for private analysis.

```text
Retention permitted        UNKNOWN for decisive RIS data
Retention window           UNKNOWN for decisive RIS data
Analysis / research use    UNKNOWN for decisive RIS data
Model training use         UNKNOWN for decisive RIS data
Verdict for R              BLOCKED pending eligibility and contractual rights
```

If retention is **not** permitted → `README.md` §12 **S1** applies: hard stop on
Phase 0 as designed. Redesign as ephemeral live evaluation, or change provider.

### 4.2 Is P permitted?

Requires everything R requires, plus showing derived data to other people.

```text
Redistribution / display to third parties   UNKNOWN for decisive RIS data
Attribution required, exact wording         UNKNOWN for decisive RIS data
Commercial use                              UNKNOWN for decisive RIS data
Verdict for P                               BLOCKED; no product route authorised
```

If R passes and P fails → drop P, continue R unchanged. Record it as a decision
in [`decisions.md`](decisions.md), superseding **D005**.

### 4.3 Consequences

1. **Provider selection:** none. Timetables is openly licensed but does not
   document the decisive hold signal or transfer topology needed to answer A2
   and A3. RIS::Connections documents those signals but is restricted to
   approved DB sales partners under individually agreed terms.
2. **Collector policy:** none can be frozen. No decisive-signal payload may be
   polled or retained until eligibility, storage and retention are expressly
   established.
3. **Assumptions:** A2b and A3b remain verified; A2c, A3c and A4 remain
   `UNKNOWN`; A7 remains untested despite `journeyID` being documented.
4. **Research and product:** both R and P remain blocked for the designed
   longitudinal decision experiment. This result does not authorise an
   ephemeral redesign or a B2B2C implementation.
5. **Stop conditions:** S1, S11 and S12 are not declared triggered because the
   required contractual facts are absent, not negative. The rights gate is
   nevertheless closed under I15. The next evidence action requires separate
   authorisation to contact DB or apply for access; this public-source sprint
   does neither.

### 4.4 Evidence still required to change the verdict

- Written eligibility for AnschlussPilot's intended research and possible
  product use, including whether non-sales-partner access is possible.
- Contract terms covering payload storage, retention, research analysis,
  third-party display, commercial use, attribution and termination.
- Confirmation that the licensed scope of RIS::Stations includes the specific
  transfer/topology fields Phase 0 would retain.
- Only after those rights pass: a bounded A7 identity spike and cadence/coverage
  feasibility check using the authorised products.
