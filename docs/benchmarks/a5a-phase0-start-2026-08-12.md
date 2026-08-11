# A5a Phase 0-Start Live Benchmark — 2026-08-12

> **Update trigger:** update only when a candidate is inspected, mobile evidence
> is received or rejected, the four-hour active-effort cap is reached, or the
> checkpoint verdict is computed. Do not change frozen method fields after the
> first candidate is inspected; append deviations and their effect instead.

This is the auditable record for the first A5a checkpoint. It tests whether a
live mobile tool explicitly compares the destination outcome of continuing with
that of changing while the action is still available. It does not test provider
rights, ticket entitlement, market share or the eventual AnschlussPilot policy.

## 1. Freeze Record

```text
run_id                         a5a-phase0-start-2026-08-12-v1
checkpoint                     Phase 0 start
method_frozen_at_utc           2026-08-11T22:40:26Z
method_frozen_at_berlin        2026-08-12T00:40:26+02:00
status                         PRE_REGISTERED — no v1 candidate inspected
active_effort_cap              4 hours
sample                         10 unique cases; 2 per frozen archetype
within-checkpoint comparison   same case on every frozen mobile surface
frozen_surfaces                DB Navigator; Trainline; Google Maps
account mode                   anonymous / guest / incognito only
web substitution               prohibited
provider API use               prohibited
```

The four-hour cap measures active discovery, handoff, evidence review and
scoring. Waiting for the author's mobile response does not consume active effort,
but evidence arriving after the action window closes cannot rescue an expired
case.

### Prior exploratory evidence is not imported

The existing `codex/a5a-20260811` branch (commits `ed1bb00`, `45e2b30` and
`55063a4`) is preserved unchanged. It records two partial bahn.de observations
and two unsuccessful Trainline web attempts, all without retained screenshots;
the branch itself reports every complete denominator as `0/10` and issues no
verdict. Those rows remain provenance only and contribute neither cases nor
scores to this v1 run.

## 2. Frozen Cases and Discovery

The five archetypes and inclusion rules remain owned by
[`../market-and-validation.md`](../market-and-validation.md) §2:

| Archetype ID | Qualifying condition | Quota |
| --- | --- | ---: |
| `incoming_buffer_erosion` | Incoming delay erodes a scheduled 10–15 minute transfer | 2 |
| `outgoing_also_delayed` | The threatened outgoing service is also delayed | 2 |
| `transfer_connection_cancelled` | The transfer connection is cancelled outright | 2 |
| `reroute_early` | An actionable alternative diverges before the transfer station | 2 |
| `realtime_degraded_or_absent` | An active disruption is independently evidenced while realtime information is unavailable, stale or conflicting | 2 |

A case must be German long-distance rail, contain at least one transfer, have a
live disruption at observation time, and have at least one alternative then
available. Cases are unique. If one case matches multiple archetypes, assign it
once using this priority:

```text
reroute_early
  → transfer_connection_cancelled
    → realtime_degraded_or_absent
      → outgoing_also_delayed
        → incoming_buffer_erosion
```

### Candidate discovery order

Use public web interfaces only as discovery aids; they are not scored surfaces.
Do not register, log in, call an API or retain provider payloads. At each station,
inspect ICE / IC / EC arrivals and departures in the next 120 minutes, then move
to the next station in this fixed order:

```text
Berlin Hbf → Frankfurt(Main)Hbf → Hamburg Hbf → Hannover Hbf → Köln Hbf
→ Leipzig Hbf → Mannheim Hbf → München Hbf → Nürnberg Hbf → Stuttgart Hbf
```

Repeat the order only if a quota remains. Record every inspected candidate,
including exclusions; do not silently discard an inconvenient case.

### Candidate ledger

| Candidate ID | Discovered UTC / Berlin | Seed station and service | Proposed itinerary | Live disruption evidence | Alternative evidence | Include / exclude | Reason | Assigned archetype | Action deadline |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| — | — | — | — | — | — | — | No v1 candidate inspected before the freeze commit | — | — |

## 3. Mobile Observation Contract

For each included candidate, issue one operation card containing the case ID,
origin, transfer, destination, travel date, scheduled services and times, observed
disruption, known alternative and action deadline. The author observes that same
case in all three mobile apps before another case is accepted.

For each app:

1. use anonymous, guest or incognito mode; do not create an account, buy a ticket
   or save the trip;
2. record app version, device, OS, locale and actual account state;
3. open the disrupted journey detail, capture the start screen and start the
   timer;
4. inspect visible content and at most three purposeful navigation taps for no
   more than two minutes; record every tap and elapsed time;
5. capture any screen claimed to satisfy a criterion. If no comparison is found,
   retain the start screen and interaction trail; an unsupported statement that
   a feature was absent is `unobserved`, not `no`.

Scrolling is logged as friction but does not consume a navigation tap unless it
opens or changes a view. If an app is missing, requires a new login, or cannot
show the case anonymously, record `surface_unavailable`; do not substitute its
web version. The checkpoint then has an incomplete denominator.

### Four decision-grade criteria

| Criterion | `yes` only when |
| --- | --- |
| `both_branches` | Continuing and changing are labelled or otherwise explicit in one view |
| `outcome_stated` | Expected destination arrival is shown for both branches, not only alternative departure times |
| `at_decision_time` | The comparison is visible while the recorded change action remains available |
| `reachable` | The comparison is reached within three navigation taps and two minutes from the disrupted journey detail |

`all_four=yes` only when all four cells are `yes` and each is backed by a
sanitised screenshot plus the interaction log.

## 4. Evidence Schema and Privacy

Raw screenshots remain outside Git. Before committing a copy, remove names,
avatars, email addresses, precise location, booking references, ticket numbers,
loyalty IDs, QR codes and unrelated notifications. Preserve journey times,
service identifiers, disruption state, available actions and the interaction
sequence needed for scoring.

Sanitised evidence paths use:

```text
evidence/a5a/2026-08-12/<case_id>/<tool>/<sequence>-<description>.png
```

Each accepted file receives a SHA-256 entry below. A missing file or hash makes
the associated criterion `unobserved`.

### Case registry

| Case ID | Archetype | Origin → transfer → destination | Services and scheduled times | Included at UTC / Berlin | Action window | Status |
| --- | --- | --- | --- | --- | --- | --- |
| — | — | — | — | — | — | No included v1 case yet |

### Tool observation schema

Each case/tool observation records these fields; values are appended after
evidence is received and checked:

```text
case_id; observed_at_utc; observed_at_berlin; tool; app_version; device; os;
locale; account_state; action_window_state; continue_final_arrival;
change_final_arrival; navigation_taps; scrolls; elapsed_seconds; both_branches;
outcome_stated; at_decision_time; reachable; all_four; evidence_paths;
evidence_sha256; limitations
```

### Evidence manifest

| Case ID | Tool | Sanitised path | SHA-256 | Criterion supported | Accepted / rejected reason |
| --- | --- | --- | --- | --- | --- |
| — | — | — | — | — | No evidence received |

## 5. Result and Stop Rule

The same competitor triggers A5a / S6 only at both thresholds:

```text
all cases             at least 6 / 10 with all_four=yes
reroute_early cases   exactly 2 / 2 with all_four=yes
```

| Tool | Complete denominator | `all_four` numerator | `reroute_early` denominator | `reroute_early` numerator | Surface status | S6 |
| --- | ---: | ---: | ---: | ---: | --- | --- |
| DB Navigator | 0 / 10 | 0 | 0 / 2 | 0 | not started | not evaluated |
| Trainline | 0 / 10 | 0 | 0 / 2 | 0 | not started | not evaluated |
| Google Maps | 0 / 10 | 0 | 0 / 2 | 0 | not started | not evaluated |

```text
checkpoint_status   PRE_REGISTERED
a5a_verdict         NOT_ISSUED
s6                   NOT_EVALUATED
```

Exactly ten unique cases, two per archetype, and thirty complete tool
observations are required for a complete checkpoint. Any missing archetype,
surface, screenshot-backed score or denominator makes the checkpoint
`inconclusive`. Incompleteness never supports a claim that a competitor lacks
the capability.

At four active hours, stop immediately, append achieved counts and calculate
only the verdict the denominators permit. The result update also changes the
repository status in `README.md`, the baseline section in
[`../market-and-validation.md`](../market-and-validation.md), and the current
reality in `AGENTS.md`. It is committed separately from this freeze.
