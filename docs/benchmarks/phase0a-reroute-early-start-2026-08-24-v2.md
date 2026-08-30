# Phase 0A `REROUTE_EARLY` Kill-Check — 2026-08-24 v2

> **Update trigger:** append when the post-freeze device check completes, a
> candidate is inspected, a mobile observation is accepted or rejected, the
> eight-hour active-effort cap is reached, or a gate is recomputed. Frozen method
> fields may not be changed after the first candidate is inspected; record a
> deviation and its consequence instead.

This is a new pre-registered denominator under [`decisions.md`](../decisions.md)
**D046**. It asks whether a traveller can compare continuing with rerouting
before a threatened `Fernverkehr → Nahverkehr` transfer while the alternative
is still actionable. It is not a market-share claim, a passenger-rights test or
provider-data permission.

## 1. Freeze Record

```text
run_id                         phase0a-reroute-early-2026-08-24-v2
pre_registration_parent       cd1796c
method_frozen_at_utc           2026-08-24T02:36:35Z
method_frozen_at_berlin        2026-08-24T04:36:35+02:00
status                         RUNNING — first fixed-order sweep complete; 0 included cases
target_sample                  3 unique cases
maximum_sample                 5 unique cases
active_effort_cap              8 hours
waiting_time                   excluded from active effort
frozen_surfaces                DB Navigator; MoBY; Wohin·Du·Willst
frozen_observation_order       DB Navigator; MoBY; Wohin·Du·Willst
account_mode                   anonymous / guest only
web_substitution               prohibited for scoring
provider API use               prohibited
location simulation            prohibited
Anschlussvormeldung submission prohibited
```

The closed v1 run in
[`phase0a-reroute-early-start-2026-08-12.md`](phase0a-reroute-early-start-2026-08-12.md)
remains `inconclusive / BLOCKED` at `0/5` complete cases and `0/15` scored
observations. No candidate, observation or evidence from v1 enters this v2
denominator.

### Why a new version may start

The 2026-08-24 setup preflight reached the anonymous journey-search entry in all
three frozen apps on the same physical device. The v1-specific
`surface_unavailable` failure for Wohin·Du·Willst did not recur. DB Navigator
has also changed version since v1. These facts permit a new denominator; they do
not revise v1 and are not themselves scored observations.

### Frozen environment and surfaces

Physical device: RMX3661, Android 15, locale `zh-TW`, timezone
`Europe/Berlin`. Automatic timezone remains off. No location is supplied or
simulated.

| Surface | Package | Frozen version | Account state at preflight | Notification / fine / coarse location | Scoring effect |
| --- | --- | --- | --- | --- | --- |
| DB Navigator | `de.hafas.android.db` | `26.15.0` (`240000770`) | Logged out; anonymous search entry reached | denied / denied / denied | Eligible for a case only after the post-freeze check |
| MoBY | `com.mdv.DEFASCompanion` | `6.165.3.2900787` (`604900787`) | Explicitly not logged in; anonymous search entry reached; optional analysis off | denied / denied / denied | Eligible for a case only after the post-freeze check |
| Wohin·Du·Willst | `de.dbregio.wohinduwillst` | `4.2.3` (`29733721`) | Logged out; anonymous search entry reached | denied / denied / denied | Eligible for a case only after the post-freeze check |

Before active effort begins, re-check the versions, timezone, permissions and
anonymous entry. Any mismatch stops candidate inspection until it is recorded
and resolved. If a frozen app version changes after the first candidate is
inspected, close v2 incomplete and require a new version rather than mixing app
versions in one denominator.

The post-freeze check ran from `2026-08-24T02:41:11Z` to
`2026-08-24T02:42:36Z` (`04:41:11`–`04:42:36` Europe/Berlin). The device
timezone, all three frozen package versions and all three anonymous search
entries matched the freeze. Android package-state evidence reported notification,
fine-location and coarse-location grants as false for every package; notification
AppOps were also `ignore`. The installed Android build did not expose the
attempted `cmd package check-permission` subcommand, so package-state output was
used rather than treating that unsupported command as a pass.

## 2. Frozen Candidate Discovery

Use official public web interfaces only to discover candidates. Web results are
leads, never scored app observations. Every journey search must set its date and
time explicitly for `Europe/Berlin`; station pages must not infer the current
window from the host computer's Asia/Taipei clock.

Inspect seed stations in this fixed order:

```text
München → Nürnberg → Würzburg → Augsburg → Regensburg → Ingolstadt
→ Bamberg → Rosenheim
```

A case qualifies only when all five conditions hold at inclusion:

1. a German long-distance service connects into a Bavarian regional service;
2. a live disruption affects the journey;
3. at least one alternative diverges before the transfer station;
4. the alternative's action window remains open; and
5. the same journey can be viewed anonymously or as a guest in all three frozen
   apps.

`Nahverkehr → Fernverkehr`, München S-Bahn and other relationships excluded by
the official Anschlussvormeldung material stay out of scope. A
`Fernverkehr → Nahverkehr` itinerary is not called eligible for
Anschlussvormeldung unless the app or current official evidence establishes it.

Record every inspected candidate, including exclusions:

### Discovery deviations

| ID | Observed UTC / Berlin | Deviation | Consequence |
| --- | --- | --- | --- |
| DV001 | 2026-08-24 02:43–02:45Z / 04:43–04:45+02:00 | The official München station departure board opened at `10:44`, reflecting the Asia/Taipei host clock rather than the required Berlin window. | Rejected the entire board output before candidate screening; no metric or candidate fact was taken from it. Continued only through official journey searches with an explicit `2026-08-24 04:50` Berlin departure anchor. |
| DV002 | 2026-08-24 02:52–02:53Z / 04:52–04:53+02:00 | The first Rosenheim seed route, München Hbf → Bad Reichenhall, could route through Austria. | Rejected it before candidate scoring and replaced it with the German-only München Hbf → Bad Aibling search. |

| Candidate ID | Discovered UTC / Berlin | Seed station and services | Journey | Live disruption | Pre-transfer reroute and deadline | Three anonymous surfaces | Include / exclude reason |
| --- | --- | --- | --- | --- | --- | --- | --- |
| C001 | 2026-08-24 02:45–02:53Z / 04:45–04:53+02:00 | München seed; representative `ICE 699 → RB54` result | Stuttgart Hbf → Rosenheim | No live deviation shown in the returned result | Not evaluated; no qualifying live-disruption lead | Not attempted; excluded before app stage | Excluded: condition 2 not established |
| C002 | 2026-08-24 02:45–02:53Z / 04:45–04:53+02:00 | Nürnberg seed; representative `IC 2063 → Bus RE30` result | Stuttgart Hbf → Bayreuth Hbf | No live deviation shown in the returned result | Not evaluated; no qualifying live-disruption lead | Not attempted; excluded before app stage | Excluded: condition 2 not established |
| C003 | 2026-08-24 02:45–02:53Z / 04:45–04:53+02:00 | Würzburg seed; representative `ICE 827 → RB53` result | Frankfurt(Main)Hbf → Schweinfurt Hbf | A two-minute departure deviation was shown for `ICE 827` | No threatened transfer or actionable pre-transfer divergence established | Not attempted; excluded before app stage | Excluded: conditions 3 and 4 not established |
| C004 | 2026-08-24 02:45–02:53Z / 04:45–04:53+02:00 | Augsburg seed; representative `ICE 699 → RB77` result | Stuttgart Hbf → Füssen | No live deviation shown in the returned result | Not evaluated; no qualifying live-disruption lead | Not attempted; excluded before app stage | Excluded: condition 2 not established |
| C005 | 2026-08-24 02:45–02:53Z / 04:45–04:53+02:00 | Regensburg seed; representative `ICE 827 → RE22` result | Frankfurt(Main)Hbf → Landshut(Bay)Hbf | A two-minute departure deviation was shown for `ICE 827` | No threatened transfer or actionable pre-transfer divergence established | Not attempted; excluded before app stage | Excluded: conditions 3 and 4 not established |
| C006 | 2026-08-24 02:45–02:53Z / 04:45–04:53+02:00 | Ingolstadt seed; representative `ICE 1501 → RE16` result | Berlin Hbf → Donauwörth | No live deviation shown in the returned result | Not evaluated; no qualifying live-disruption lead | Not attempted; excluded before app stage | Excluded: condition 2 not established |
| C007 | 2026-08-24 02:45–02:53Z / 04:45–04:53+02:00 | Bamberg seed; representative `ICE 827 → RE19` result | Frankfurt(Main)Hbf → Coburg | A two-minute departure deviation was shown for `ICE 827` | No threatened transfer or actionable pre-transfer divergence established | Not attempted; excluded before app stage | Excluded: conditions 3 and 4 not established |
| C008 | 2026-08-24 02:45–02:53Z / 04:45–04:53+02:00 | Rosenheim seed; representative `RJX 265 → RB58` result | München Hbf → Bad Aibling | No live deviation shown in the returned result | Not evaluated; no qualifying live-disruption lead | Not attempted; excluded before app stage | Excluded: condition 2 not established |

The eight searches used the frozen seed order and an explicit
`2026-08-24 04:50` Europe/Berlin departure anchor. They are representative
discovery queries, not exhaustive evidence that no disruption existed at a seed
station. No row satisfied all five inclusion conditions, so no app observation
window opened.

### Active-effort ledger

The v2 clock starts with the final post-freeze device and surface verification.
Pre-registration drafting and the earlier setup preflight are outside the v2
ledger. Waiting time is excluded.

| UTC interval | Active minutes | Work |
| --- | ---: | --- |
| 2026-08-24 02:41:11–02:42:36Z | 2 | Post-freeze device, package-version, permission and anonymous-entry verification |
| 2026-08-24 02:42:36–02:53:52Z | 12 | First fixed-order official-web station sweep; eight German-scope discovery queries plus two rejected deviations |
| **Total** | **14** | Waiting excluded; 466 active minutes remain under the frozen cap |

## 3. Observation Contract

For each included case, record:

- `t_early`: the first time the disruption is known and a pre-transfer reroute
  remains actionable;
- `t_late`: the earliest observable among the standard warning, the best
  reroute's action deadline, or the official connection-protection processing
  point.

At each anchor preserve observation time, source and freshness; continue and
reroute destination ETAs; action deadlines; extra transfers; and the provenance
needed to distinguish product-reported facts from researcher calculations.
Ticket executability stays `UNKNOWN` unless independent binding evidence says
otherwise.

Observe apps in the frozen order: DB Navigator, MoBY, then
Wohin·Du·Willst. For each app begin at the disrupted journey detail and allow at
most three purposeful navigation taps and two minutes. Record taps, scrolls and
elapsed time. A missing app, required login or absent anonymous entry is
`surface_unavailable`; no web surface replaces it.

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
| — | — | — | — | — | — | — | No included case |

### Tool observations

| Case ID | Observed UTC / Berlin | Tool and environment | Account state | Taps / scrolls / seconds | Four criteria | `all_four` | Evidence | Limitations |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| — | — | — | — | — | — | — | — | No scored observation |

## 4. Evidence and Privacy

Raw screenshots stay outside Git under
`.private/evidence/phase0a/2026-08-24-v2/`. A committed copy must remove names,
avatars, email addresses, precise location, booking and order references,
tickets, loyalty identifiers, QR codes and unrelated notifications while
preserving the journey state, actions, times and interaction sequence needed for
scoring.

```text
evidence/phase0a/2026-08-24-v2/<case_id>/<tool>/<sequence>-<description>.png
```

| Case ID | Tool | Sanitised path | SHA-256 | Criterion or operand supported | Accepted / rejected reason |
| --- | --- | --- | --- | --- | --- |
| — | — | — | — | — | No evidence captured |

If sensitive content cannot be removed without destroying the scoring evidence,
do not commit the image and mark the affected criterion unsupported.

## 5. Frozen Metrics and Gates

The baseline ladder remains:

1. `CONTINUE_CURRENT_PLAN`;
2. connection warning;
3. manual alternatives search from disruption detail;
4. Anschlussvormeldung / connection protection.

Anschlussvormeldung is embedded in MoBY and Wohin·Du·Willst, not a fourth scored
surface. No request is submitted. Without a genuine in-route observer its live
lane remains `UNKNOWN`.

For each case record projected destination-arrival gain at `t_early`, option
decay by `t_late`, and decision lead time over the first existing baseline.
Unobserved counterfactual outcomes stay `UNKNOWN`.

### Competitor stop gate

- After three complete cases, the same app reaching `all_four=yes` in at least
  `2/3` triggers S6 immediately.
- Otherwise extend to at most five; the same app reaching at least `3/5`
  triggers S6.
- Missing surfaces never enter a smaller denominator.

### Early-reroute opportunity gate

All four conditions are required on five unique cases:

```text
projected_arrival_gain_t0 >= 10 minutes     at least 3 / 5
mean projected_arrival_gain_t0 >= 10 minutes
option_decay_count >= 1                     at least 3 / 5
positive lead versus an existing baseline  at least 3 / 5
```

### Minimum-data gate

- every metric operand must be observed at the correct decision time in all
  five cases;
- at least one obtainable source must permit the Phase 0 storage and retention
  required for measurement.

Only five unique cases, fifteen complete app observations, complete temporal
anchors and a complete evidence manifest can support continuing into full Phase
0. Any incomplete surface, denominator, temporal anchor, connection-protection
lane or acquisition-rights answer leaves the overall verdict
`inconclusive / BLOCKED`.

## 6. Result Ledger

| Gate | Raw numerator / denominator | State | Reason |
| --- | --- | --- | --- |
| Competitor stop | DB Navigator 0/0; MoBY 0/0; Wohin·Du·Willst 0/0 complete cases | `NOT_EVALUATED` | First discovery sweep produced no included case |
| Early-reroute opportunity | `0/5` cases | `INCONCLUSIVE` | Eight leads inspected; none met all five inclusion conditions |
| Informational sufficiency | `0/5` cases; `0/15` observations | `INCONCLUSIVE` | No three-app observation window opened |
| Acquisition rights | provider verdict `v4-partial / BLOCKED` | `BLOCKED` | A2c, A3c, A4 and downstream rights remain `UNKNOWN` |
| Anschlussvormeldung live lane | `0` genuine in-route observations | `UNKNOWN` | No location simulation or request is permitted |

```text
phase0a_v2_status  RUNNING — first fixed-order sweep complete
phase0a_v2_verdict INCONCLUSIVE / BLOCKED
provider_rights   BLOCKED
engineering_allowed NO
```

DELFI responded 2026-08-24, before the planned silence deadline, requesting
project context rather than answering the six original questions (see
`provider-evaluation.md` §5.3/§6.3). Written-only project context was sent on
2026-08-26; no DELFI status changes this frozen competitive denominator.
