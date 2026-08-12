# Phase 0A `REROUTE_EARLY` Kill-Check — 2026-08-12

> **Update trigger:** append when a candidate is inspected, a mobile observation
> is accepted or rejected, the product-support enquiry receives a response, the
> eight-hour active-effort cap is reached, or a gate is recomputed. Frozen method
> fields may not be changed after the first candidate is inspected; record a
> deviation and its consequence instead.

Phase 0A asks one narrow question: while a useful alternative still diverges
before the threatened transfer, can a traveller compare the destination outcome
of continuing the current plan with rerouting now? It is a product-thesis
kill-check, not a claim about market share, ticket validity, passenger rights or
provider-data permission.

## 1. Freeze Record

```text
run_id                         phase0a-reroute-early-2026-08-12-v1
method_frozen_at_utc           2026-08-12T00:34:58Z
method_frozen_at_berlin        2026-08-12T02:34:58+02:00
status                         PRE_REGISTERED — no Phase 0A candidate inspected
target_sample                  3 unique cases
maximum_sample                 5 unique cases
active_effort_cap              8 hours
waiting_time                   excluded from active effort
frozen_surfaces                DB Navigator; MoBY; Wohin·Du·Willst
account_mode                   anonymous / guest only
web_substitution               prohibited
provider API use               prohibited
location simulation            prohibited
Anschlussvormeldung submission prohibited
```

The archived D045 run in
[`a5a-phase0-start-2026-08-12.md`](a5a-phase0-start-2026-08-12.md) remains
provenance only. Its one case and one observation are not imported into this
denominator.

## 2. Product-Support Enquiry

| Sent date | Recipient unit | Question scope | State | Follow-up rule |
| --- | --- | --- | --- | --- |
| 2026-08-12 | Bavaria passenger-information product support for MoBY / Wohin·Du·Willst | Supported connection relationships or markings; viewability without submitting a request; whether continue and early-reroute destination ETAs are compared; current production demo or test case; permission to publish de-identified public-surface screenshots in a non-commercial report | `awaiting response` | One same-thread follow-up after seven working days without a response; after seven further working days record `inconclusive / no response`, never refusal |

No private address, message body or Gmail screenshot is retained in Git. A
response can refine future candidate screening or provide official current
screens, but cannot retroactively change an already observed case. A statement
that a decision-grade comparison exists is primary evidence, not a scored live
observation, until a current surface or official current screen verifies it.

## 3. Frozen Candidate Discovery

Use public web interfaces only to discover and track candidates; they are not
scored and no API is used. Poll in this fixed order:

```text
München → Nürnberg → Würzburg → Augsburg → Regensburg → Ingolstadt
→ Bamberg → Rosenheim
```

A case qualifies only when all of these hold at inclusion:

1. a German long-distance service connects into a Bavarian regional service;
2. a live disruption affects the journey;
3. at least one alternative diverges before the transfer station;
4. the alternative's action window is still open; and
5. the same journey can be viewed anonymously or as a guest in all three frozen
   apps.

`Nahverkehr → Fernverkehr`, München S-Bahn and other relationships that the
official Anschlussvormeldung material expressly excludes are out of scope.
`Fernverkehr → Nahverkehr` is only a scope candidate: unless the app or current
official evidence confirms the relationship, this record does not call it
eligible for an actual Anschlussvormeldung.

Record every inspected candidate, including exclusions:

| Candidate ID | Discovered UTC / Berlin | Seed station and services | Journey | Live disruption | Pre-transfer reroute and deadline | Three anonymous surfaces | Include / exclude reason |
| --- | --- | --- | --- | --- | --- | --- | --- |
| — | — | — | — | — | — | — | No Phase 0A candidate inspected before freeze |

## 4. Observation Contract

For each included case, record two temporal anchors:

- `t_early`: the first time the disruption is known and a pre-transfer reroute
  remains actionable;
- `t_late`: the earliest observable among the standard warning, the best
  reroute's action deadline, or the official connection-protection processing
  point (approximately 15 minutes before transfer, with a response approximately
  10 minutes before transfer).

At each anchor, preserve the observation time, source and freshness; the
continue destination ETA, every relevant reroute destination ETA and action
deadline; and the evidence needed to distinguish provider-reported facts from
researcher calculations. A timetable-feasible action has
`ticket_executability = UNKNOWN` unless independent binding evidence says
otherwise.

For each app, begin at the disrupted journey detail and allow at most three
purposeful navigation taps and two minutes. Record every tap, scroll and elapsed
time. A missing app, required login or absent anonymous entry is
`surface_unavailable`; no web surface replaces it.

The four decision-grade criteria remain:

| Criterion | `yes` only when |
| --- | --- |
| `both_branches` | Continue and change are explicit in one view |
| `outcome_stated` | Both branches show expected destination arrival, not only departure times |
| `at_decision_time` | The comparison is visible while the recorded action window remains open |
| `reachable` | The comparison is reached within three taps and two minutes from disrupted journey detail |

`all_four=yes` requires four `yes` values backed by accepted evidence.

### Case and observation schema

```text
case_id; discovered_at_utc; discovered_at_berlin; origin; transfer;
destination; incoming_service; outgoing_service; disruption;
t_early_utc; t_late_utc; continue_eta_t_early; reroute_etas_t_early;
continue_eta_t_late; reroute_etas_t_late; action_deadlines; freshness;
source; ticket_executability; additional_transfers; reversal_observed;
false_intervention_outcome; limitations

case_id; observed_at_utc; observed_at_berlin; tool; app_version; device; os;
locale; account_state; navigation_taps; scrolls; elapsed_seconds;
both_branches; outcome_stated; at_decision_time; reachable; all_four;
evidence_paths; evidence_sha256; limitations
```

### Case registry

| Case ID | Journey and disruption | `t_early` | `t_late` | Continue ETA | Best reroute ETA / deadline | Freshness / source | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| — | — | — | — | — | — | — | No included case yet |

### Tool observations

| Case ID | Observed UTC / Berlin | Tool and environment | Account state | Taps / scrolls / seconds | Four criteria | `all_four` | Evidence | Limitations |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| — | — | — | — | — | — | — | — | No observation yet |

## 5. Evidence and Privacy

Raw screenshots stay outside Git. A committed copy must remove names, avatars,
email addresses, precise location, booking and order references, tickets,
loyalty identifiers, QR codes and unrelated notifications while preserving the
journey state, actions, times and interaction sequence needed for scoring.

```text
evidence/phase0a/2026-08-12/<case_id>/<tool>/<sequence>-<description>.png
```

| Case ID | Tool | Sanitised path | SHA-256 | Criterion or operand supported | Accepted / rejected reason |
| --- | --- | --- | --- | --- | --- |
| — | — | — | — | — | No Phase 0A evidence yet |

## 6. Frozen Metrics and Baselines

Baseline ladder, from weakest intervention to the incumbent operational lane:

1. `CONTINUE_CURRENT_PLAN`;
2. connection warning;
3. manual alternatives search from disruption detail;
4. Anschlussvormeldung / connection-protection workflow.

The fourth baseline is embedded in MoBY and Wohin·Du·Willst rather than scored
as a fourth app. The [official DB Regio Bayern FAQ](https://regional.bahn.de/regionen/bayern/service/anschluss-voranmeldung/anschlussvormeldung-faq)
documents route-position validation and a workflow in which requests may be
made from one hour before the first transfer, operations normally process the
request about 15 minutes before transfer and the app responds about 10 minutes
before transfer. These timings establish a baseline window, not a live product
score. Without a genuine in-route observer in Germany, the live
Anschlussvormeldung lane remains `UNKNOWN`; location is never simulated and no
request is sent.

For case `i`:

- `projected_arrival_gain_t0_i = ETA_continue_destination(t_early) −
  ETA_best_reroute_destination(t_early)`. This is a contemporaneous forecast
  advantage, not actual time saved.
- `option_decay_count_i` is the count of alternatives that were open and
  forecast-better at `t_early` but no longer actionable at `t_late`.
- `decision_lead_time_i = best_reroute_action_deadline −
  first_complete_comparison_time`. Also record minutes earlier than the first
  standard warning or connection-protection baseline observation.

Guardrails recorded separately are extra transfers, ticket-binding status,
freshness, recommendation reversal and any observed false-intervention outcome.
An unobserved counterfactual outcome stays `UNKNOWN`.

| Case ID | Gain operands / minutes | Decayed options operands / count | Lead-time operands / minutes | Extra transfers | Binding | Freshness | Reversal | False-intervention outcome |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| — | — | — | — | — | — | — | — | No metrics yet |

## 7. Frozen Gates and Verdict Logic

### Competitor stop gate

- After three complete cases, if the same app has `all_four=yes` in at least
  `2/3`, trigger S6 immediately and stop.
- Otherwise extend to at most five cases. At five, the same app reaching at
  least `3/5` triggers S6.
- Missing surfaces never enter a smaller denominator and never support a claim
  that the capability is absent.

### Early-reroute opportunity gate

All four conditions are required on five unique cases:

```text
projected_arrival_gain_t0 >= 10 minutes     at least 3 / 5
mean projected_arrival_gain_t0 >= 10 minutes
option_decay_count >= 1                     at least 3 / 5
positive lead versus an existing baseline  at least 3 / 5
```

### Minimum-data gate

- **Informational sufficiency:** every metric operand is observed at the correct
  decision time in all five cases.
- **Acquisition rights:** at least one obtainable source permits the Phase 0
  storage and retention required for measurement.

Only five unique cases, fifteen complete app observations, complete temporal
anchors and a complete evidence manifest can support a recommendation to
continue into full Phase 0. If any surface, denominator, temporal anchor,
Anschlussvormeldung live lane or acquisition-rights answer is incomplete, the
overall verdict is `inconclusive / BLOCKED`. No collector, model or product work
starts. A stop result records only stop, B2B2C or research-only options; it does
not create a replacement product automatically.

## 8. Result Ledger

| Gate | Raw numerator / denominator | State | Reason |
| --- | --- | --- | --- |
| Competitor stop | 0 / 0 complete cases per app | `NOT_EVALUATED` | No Phase 0A observation yet |
| Early-reroute opportunity | 0 / 5 complete cases | `NOT_EVALUATED` | No Phase 0A case yet |
| Informational sufficiency | 0 / 5 complete cases | `NOT_EVALUATED` | No Phase 0A case yet |
| Acquisition rights | — | `BLOCKED` | No provider has sufficient verified storage and retention rights |
| Anschlussvormeldung live lane | 0 observations | `UNKNOWN` | No genuine in-route German observation |

```text
phase0a_status       PRE_REGISTERED
phase0a_verdict      NOT_ISSUED
provider_rights      BLOCKED
engineering_allowed NO
```
