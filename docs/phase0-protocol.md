# Phase 0 Protocol Manifest

**Protocol version: `UNSET`**

**Status: `BLOCKED` — this manifest is incomplete and no Phase 0 measurement may
start from it.**

This is the single freeze record for Phase 0. Detailed rules remain in their
own documents; this manifest records the exact version of each rule set used by
a collector or measurement. It is an index, not a second copy of the rules.

> **Update trigger:** update before any collector or measurement gate changes,
> and whenever a referenced artefact changes version. Once measurement starts,
> any change creates a new protocol version and invalidates results produced
> under the old combination. Checked at deliverable completion (`AGENTS.md` §4).

---

## 1. Freeze Record

Every `UNSET` below is a blocker for the gate that references it. A date or a
filename without a version is not a freeze.

| Item | Frozen value | Canonical detail |
| --- | --- | --- |
| Protocol version | `UNSET` | This file |
| Git commit | `UNSET` | Commit containing the frozen implementation and rules |
| Corridor scope version | `UNSET` | §2 |
| Provider verdict and terms snapshot | `UNSET` | [`provider-evaluation.md`](provider-evaluation.md) |
| Observation schema version | `UNSET` | [`railway-domain.md`](railway-domain.md) §10 |
| Synthetic enumeration version | `UNSET` | §3 |
| Binding ruleset version | `UNSET` | [`binding-scenarios.md`](binding-scenarios.md) |
| Episode construction version | `UNSET` | [`modelling-and-evaluation.md`](modelling-and-evaluation.md) §2 |
| Deterministic baseline version | `UNSET` | [`modelling-and-evaluation.md`](modelling-and-evaluation.md) §4 |
| Decision-policy version | `UNSET` | [`decision-model.md`](decision-model.md) §§5–8 |
| Measurement window | `UNSET` | Start and end instants, timezone-aware |
| Development / holdout split | `UNSET` | `none` when the pre-registered policy is scored once; otherwise dated boundary |

The provider snapshot records the provider, exact terms URLs, consultation
dates, permitted fields, raw-payload permission, retention period, attribution,
rate limits, and R/P verdicts. Ticket conditions are not part of that snapshot;
they are a separate carrier-conditions input to the binding ruleset.

---

## 2. Corridor and Observation Scope

Freeze all of the following as corridor-scope version `v1` before collection:

```text
corridor id                 UNSET
included stations           UNSET
included service edges      UNSET
included branches           UNSET
included service classes    UNSET
timezone                    Europe/Berlin
service-date assignment     UNSET
polling cadence             UNSET
```

The scope is a service graph, not a marketing name for a route. A counterfactual
candidate is fully observed only when every service run needed to evaluate its
destination outcome lies inside that graph and inside the collection window.

Use these coverage states:

```text
evaluable                  candidate set and required outcomes fully observed
unobserved_out_of_scope    a required service or edge lies outside the frozen graph
insufficient_data          in-scope, but missing, stale, malformed, or gapped data
```

An unobserved candidate is never converted to *"no alternative"*. It cannot be
ranked as available, and the affected assessment remains explicit uncertainty
under `AGENTS.md` I4.

---

## 3. Synthetic Itinerary Population

Phase 0 enumeration version `v1` has these fixed structural rules:

- exactly one planned transfer, represented as `A → B → C`;
- at least one leg is a German long-distance service;
- origin, transfer station, and destination are distinct and inside the frozen
  corridor graph;
- no repeated station or loop;
- one record per unique key
  `(service_date, origin, transfer, destination, incoming_run, outgoing_run)`;
- every unique eligible itinerary has equal weight. It is an operational rate,
  never a passenger-demand-weighted rate.

The following parameters must be set after corridor selection and frozen before
measurement:

```text
timetable window                 UNSET
minimum scheduled transfer       UNSET
maximum scheduled transfer       UNSET
maximum scheduled detour         UNSET
eligible long-distance classes   UNSET
cross-midnight inclusion         UNSET
```

Changing any structural rule or parameter creates a new enumeration version.
Results from different enumeration versions are not pooled.

---

## 4. Coverage and A1 Reporting

For every A1 result report:

```text
N    all eligible enumerated itineraries
n    evaluable itineraries
u    unobserved_out_of_scope + insufficient_data itineraries
o    opportunities among the evaluable itineraries
```

By construction, `N = n + u`; any record outside those categories is a schema
or classification defect, not a fourth denominator state.

Report all of these together:

- evaluable conditional rate: `o / n`;
- conservative lower bound: `o / N`;
- conservative upper bound: `(o + u) / N`;
- coverage gap: `u / N`;
- raw `N`, `n`, `u`, and `o` counts.

If `n = 0`, the conditional rate is `undefined`, never zero. These bounds express
data coverage uncertainty; they do not assume that every unknown itinerary is a
real opportunity. `u / N` also feeds the `UNKNOWN` analysis and stop condition
S4.

---

## 5. Episode and Policy Freeze

The episode construction version records:

```text
W_primary                 UNSET
W_narrow                  UNSET
W_wide                    UNSET
minimum episode count     UNSET
corridor-day fallback     enabled for corridor-wide correlated disruptions
```

The decision-policy version records the ordered Phase 0 filters, material-
improvement threshold, intervention bar, safety margin, and stability rules.
Phase 0 has no fitted utility function. If tuning is unavoidable, the dated
development/holdout boundary in §1 becomes mandatory.

---

## 6. Gates

### Collector gate

Collection may start only when:

- provider R rights and retention are verified;
- corridor scope `v1` and polling cadence are frozen;
- observation schema `v1` is frozen, including missing-value semantics;
- raw-payload handling matches the provider verdict;
- collection-integrity tests cover heartbeat, gaps, restart, and request failure.

The A7 spike is exploratory input to this gate. Its scratch observations never
enter the Phase 0 measurement store unless they independently satisfy the final
contract and provider terms.

### Measurement gate

Measurement may start only when every item in §1 is set and:

- enumeration `v1` and the A1 denominator are frozen;
- binding ruleset `v1` is sourced and frozen;
- episode windows and minimum episode count are frozen;
- deterministic baseline and decision policy are frozen;
- measurement window and any holdout boundary are dated;
- the Git commit containing all of the above is recorded.

---

## 7. Change Control

Before measurement, a change updates the relevant artefact version and this
manifest. After measurement begins, a change additionally creates a new protocol
version; results from the old version remain labelled with it and are never
silently re-labelled. A reported result without the complete freeze record from
§1 is not a Phase 0 result.
