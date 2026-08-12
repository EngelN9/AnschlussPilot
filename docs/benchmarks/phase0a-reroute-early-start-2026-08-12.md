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
status                         INCONCLUSIVE / BLOCKED — no complete case denominator
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

### Run environment and surface setup

Observed on a physically connected RMX3661 running Android 15 (API 35), with
device locale `zh-TW`, system timezone `Europe/Berlin`, automatic time enabled
and automatic timezone disabled. The timezone was set by the device owner; no
location was supplied or simulated.

| Surface | Version | Install source | Observed account state | Permission state at setup | Phase 0A scoring effect |
| --- | --- | --- | --- | --- | --- |
| DB Navigator | `26.14.0` (`240000760`) | Pre-installed before this run | Anonymous surface previously reached; no account created or used | Not re-requested during setup | Ready for an included case; no setup screen is scored |
| MoBY | `6.165.3.2900787` (`604900787`) | Google Play | Onboarding completed with optional pseudonymous analytics left off; anonymous connection-search home reached without login | Notifications and precise location not granted | Ready for an included case; onboarding is outside the tap/time denominator |
| Wohin·Du·Willst | `4.2.3` (`29733721`) | Google Play | Introductory flow reached without login, but both the text search and regional-version list returned `Sorry, we could't load any places. Please try again!`; no version or journey-search surface could be selected | Notifications, precise location and special alarm access not granted | `surface_unavailable` for this run; the fixed denominator is not reduced and no product-capability inference is made |

Installation and onboarding checks establish only that the app package and an
anonymous entry path are present. They do not show an eligible live journey or
any decision-grade comparison.

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

### Discovery deviations

| Observed UTC | Deviation | Containment and effect |
| --- | --- | --- |
| 2026-08-12T00:41Z–00:44Z | The eight `bahnhof.de` station pages selected their current window from the browser's Asia/Taipei clock while displaying German station wall times. | The complete München-to-Rosenheim sweep was rejected as a discovery attempt: no displayed service was accepted, excluded or used as a metric operand. Subsequent discovery uses a journey search with the date and time explicitly set to Europe/Berlin. |
| 2026-08-12T00:45Z | The current DB long-distance traffic page listed no result when both `Fernverkehr` and `Bayern` filters were active, although the unfiltered long-distance view listed a Stuttgart–Nürnberg infrastructure disruption. | A broad traffic bulletin is retained only as a lead. A case still requires an exact itinerary, contemporaneous journey detail, action deadline and alternative; the bulletin alone cannot establish `t_early`. |

| Candidate ID | Discovered UTC / Berlin | Seed station and services | Journey | Live disruption | Pre-transfer reroute and deadline | Three anonymous surfaces | Include / exclude reason |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `C001` | 2026-08-12T00:46:22Z / 2026-08-12T02:46:22+02:00 | Augsburg / ICE 619 → RE16 → RE80 | Stuttgart Hbf → Augsburg Hbf → Treuchtlingen → Ansbach; 03:50–07:39 | Journey detail showed scheduled and predicted times equal; the only current message concerned a defective vehicle boarding aid | Origin-level IC 2063 at 06:05, arriving 07:49; later regional options arrived 08:49 | not checked after exclusion | exclude — no observed timing or connection disruption, and the visible alternative was forecast later than continuing |
| `C002` | 2026-08-12T00:56Z–01:14Z / 2026-08-12T02:56–03:14+02:00 | Nürnberg / IC 2063 → replacement Bus RE30 | Planned Stuttgart Hbf 06:05 → Nürnberg Hbf 08:18/08:30 → Bayreuth Hbf 09:55 | Current journey detail carried a heat-damage notice for Stuttgart–Nürnberg: omitted stops and delays up to 20 minutes; its displayed itinerary still used scheduled times and therefore did not establish an adjusted continue ETA | 03:50 ICE 619 via Augsburg, RE16, replacement Bus RE31 and RE30; arrives 09:41, diverges at Stuttgart and closes at 03:50 | DB Navigator and MoBY displayed the same journey anonymously while the window remained open; Wohin·Du·Willst could not load any place or regional version and never reached journey search | exclude — fixed condition 5 failed. The displayed 14-minute alternative advantage, two extra transfers and 03:50 deadline remain candidate-screening facts, not accepted metric operands or a scored 2/3 denominator |

### Active-effort ledger

| UTC interval | Active minutes | Work |
| --- | ---: | --- |
| 2026-08-12T00:41Z–00:49Z | 8 | Timezone-contaminated station sweep, containment, current long-distance bulletin review and one exact Berlin-time journey check |
| 2026-08-12T00:49Z–00:54Z | 5 | Installed the two frozen Bavarian apps from their official Google Play listings, recorded package versions and checked the anonymous onboarding path without granting location |
| 2026-08-12T00:54Z–00:58Z | 4 | Refreshed the public disruption bulletin and checked a Stuttgart–Nürnberg–Bayreuth journey plus a pre-transfer alternative; retained C002 as pending rather than prematurely including it |
| 2026-08-12T00:58Z–01:03Z | 5 | Completed anonymous onboarding checks without granting notification, location or special alarm access; reached MoBY search and the Wohin·Du·Willst version-selection boundary |
| 2026-08-12T01:03Z–01:11Z | 8 | Repeated Wohin·Du·Willst text and regional-version loading checks, then independently found C002 in DB Navigator and MoBY while its 03:50 action window remained open |
| 2026-08-12T01:11Z–01:15Z | 4 | Repeated the failing Wohin·Du·Willst surface check, preserved de-identified exclusion evidence and recomputed the frozen denominator and gates |

Total active effort was 34 minutes. The run stopped before the eight-hour cap
because a mandatory frozen surface could not reach journey search after
repeated anonymous setup attempts, making a complete three-app case denominator
impossible in this environment. This is an incomplete-run stop, not S6 and not
evidence that the missing product capability does not exist.

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
| — | — | — | — | — | — | — | No included case; C001 and C002 were excluded before scoring |

### Tool observations

| Case ID | Observed UTC / Berlin | Tool and environment | Account state | Taps / scrolls / seconds | Four criteria | `all_four` | Evidence | Limitations |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| — | — | — | — | — | — | — | — | No scored observation; two anonymous C002 journey checks and one unavailable-surface check were retained only as exclusion evidence |

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
| `C002` | DB Navigator | `evidence/phase0a/2026-08-12/c002/db-navigator/01-results.png` | `513757EF0B13FDDB1C2FB6C8A281FDE6912FFFE6CEC08C848BD9298CFF9ED67B` | Same 06:05–09:55 IC 2063 / replacement-bus journey visible at 03:14 Berlin; current result list carried a disruption marker | Accepted for candidate-exclusion provenance; rejected from scoring because the case failed the three-surface inclusion condition |
| `C002` | MoBY | `evidence/phase0a/2026-08-12/c002/moby/01-results.png` | `8B1B26630DC364E8AF08161AC3466757F38B2172C8B6180480C44C6B8D79C155` | Same 06:05–09:55 IC 2063 / replacement-bus journey visible at 03:13 Berlin; result flagged a disruption and partial absence of realtime information | Accepted for candidate-exclusion provenance; rejected from scoring because the case failed the three-surface inclusion condition |
| `C002` | Wohin·Du·Willst | `evidence/phase0a/2026-08-12/c002/wohin-du-willst/01-surface-unavailable.png` | `FD33DDFF8AD56C9F756945C33573EFA01A5DE97F27568846C2020F6864318278` | Text-place search returned `Sorry, we could't load any places. Please try again!` at 03:11 Berlin before any journey could be entered | Accepted for the run-specific `surface_unavailable` finding only; it does not establish absence of any feature |

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
| — | — | — | — | — | — | — | — | No accepted metric operands; C002's displayed 09:55 versus 09:41 candidate-screening comparison is not promoted into this table |

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
| Competitor stop | DB Navigator 0 / 0; MoBY 0 / 0; Wohin·Du·Willst 0 / 0 complete cases | `NOT_EVALUATED` | No unique case produced three complete app observations; S6 is not triggered and no denominator is reduced |
| Early-reroute opportunity | 0 / 5 complete cases | `INCONCLUSIVE` | Two inspected candidates were excluded; the 3/5 counts and five-case mean cannot be computed |
| Informational sufficiency | 0 / 5 complete cases; 0 / 15 complete app observations | `INCONCLUSIVE` | No accepted `t_early` / `t_late` pair or complete three-surface case |
| Acquisition rights | — | `BLOCKED` | No provider has sufficient verified storage and retention rights |
| Anschlussvormeldung live lane | 0 observations | `UNKNOWN` | No genuine in-route German observation |

```text
phase0a_status       INCONCLUSIVE_BLOCKED
phase0a_verdict      INCONCLUSIVE / BLOCKED
provider_rights      BLOCKED
engineering_allowed NO
```

Neither a continuation recommendation nor an S6 stop/pivot result is supported.
The fixed competitor lane is incomplete, the live Anschlussvormeldung lane is
unobserved, the product-support enquiry is awaiting response and acquisition
rights remain blocked. A later run may begin only as a new pre-registered
denominator after the frozen app surface is genuinely available; this run is
not rewritten or backfilled.
