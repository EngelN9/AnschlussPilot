# Railway Domain Notes

Detail for [`AGENTS.md`](../AGENTS.md) §1 (I6–I9) and §2. Read before touching
provider adapters, normalization, service identity, state reconciliation, or
anything storing observations.

> **Update trigger:** revise when a provider adapter is added or changed, when
> normalization or identity rules change, or when the observation store schema
> changes. Checked in `AGENTS.md` §4.

---

## 1. Assumptions That Are Wrong

Never assume:

```text
train number == train identity
scheduled transfer == feasible transfer
delay is the only disruption type
arrival times only move later
updates arrive in order
provider data is internally consistent
a train keeps the same stopping pattern
same-name stations are equivalent
```

Situations the domain must eventually tolerate: changing delays, delay
corrections, platform changes, full and partial cancellations, changed stopping
patterns, changed train numbers, train splitting, train joining, through
services, replacement services, journey termination short of destination,
cross-midnight journeys, duplicate events, out-of-order events, missing
observations, stale observations, and directly conflicting observations.

Shortcuts that only work for a polished demo are not acceptable here.

---

## 2. Provider Boundary

```text
External provider
      ↓  adapter          — provider-specific, isolated, individually tested
      ↓  normalization    — provider vocabulary → canonical vocabulary
Canonical railway model
      ↓
Journey state
      ↓
Risk / alternatives / decision
```

Provider-specific fields stay behind the adapter. This is what makes it possible
to test without live providers, replace a provider, evolve a schema, handle
missing information explicitly, and keep domain semantics stable.

Unknown provider values degrade gracefully — an unrecognized status code becomes
an explicit unknown, not a silent default.

---

## 3. Service Identity

A displayed train number does not uniquely identify a train run. Identity may
depend on the provider journey identifier, the service date, the route, the stop
sequence, the operating context, replacement relationships, and split/join
behaviour.

Getting this wrong corrupts journey state, historical observations, risk labels,
alternative routing, backtests, and any future training data — usually silently,
and usually discovered months later.

**Service identity logic must have its own explicit tests.**

This is not only an implementation concern — it is assumption **A7** in
[`README.md`](../README.md) §3, tested by a one-day linkage spike *before* the
collector is built, and gated by stop condition **S9**. Failure here is silent:
mis-linked runs yield coherent-looking delay sequences that quietly corrupt every
statistic derived from them.

---

## 4. Observations Are Events

Realtime ingestion is not a destructive overwrite. Distinguish four things:

```text
Scheduled state          what was planned
Realtime observations    what was reported, when, by whom
Current interpreted state  what we currently believe
Historical observations  the full sequence, retained
```

A realistic sequence:

```text
15:01  delay +3
15:04  delay +5
15:06  platform changed
15:08  delay +9
15:10  corrected to +6
15:14  partial cancellation
```

The interpreted state changes; the earlier observations remain valuable for
debugging, reproducibility, delay-evolution analysis, historical reconstruction,
statistical modelling, and decision backtesting.

Retention is subject to provider terms — see `AGENTS.md` I15.

---

## 5. State Reconciliation

Observations may be duplicated, delayed, out of order, corrected, incomplete, or
inconsistent. Reconciliation is therefore explicit logic, not an implicit
last-write-wins.

Do not assume `last event received == newest truth` without considering provider
semantics and the event's own timestamps.

Preserve enough information to answer *why did the current state change?*

---

## 6. Temporal Handling

Distinguish, wherever the difference can matter:

```text
scheduled time
provider observation time
event effective time
ingestion time
decision time
```

Use timezone-aware values. Handle cross-midnight journeys, service dates,
daylight-saving transitions, delayed observations, and late corrections
deliberately rather than incidentally.

The rule that matters most:

> **Historical evaluation may only use information that was available at the
> evaluated decision time.**

This is both a correctness requirement for backtesting and the primary defence
against feature leakage.

---

## 7. Freshness

Freshness is part of product correctness, not a display detail. The system must
be able to represent states equivalent to:

```text
LIVE         recent, trusted
STALE        old enough that confidence should drop
UNAVAILABLE  no usable realtime information
```

A recommendation must know the freshness of the evidence behind it. If freshness
is insufficient, prefer uncertainty over false confidence.

Provider failure must never silently: crash the UI, erase last known state, mark
a connection safe, or produce a confident recommendation from stale data.

---

## 8. Provenance

For normalized information it should remain possible to determine which provider
supplied it, when it was observed, when it was received, whether it was directly
reported or inferred, and how it was normalized.

Never silently convert an inference into a provider-reported fact. This
distinction has to survive all the way to the user interface
(`AGENTS.md` I9, I5).

---

## 9. Collection Integrity

A collector that stops silently leaves a permanent hole. Nothing later can fill
it. This is not an operations concern to address after the pipeline works — it
ships in the collector's first commit (`README.md` §4, decision 4).

### Gaps are recorded, not merely absent

The store must carry explicit *"no observation in this window"* records. Without
them, replay cannot distinguish:

```text
nothing happened          — the service was observed, and was unchanged
we were not looking       — no observation exists for this window
```

Those two produce identical-looking data and opposite conclusions. Absence of a
delay record is not evidence of no delay.

### Heartbeat

The collector emits a liveness signal and the timestamp of its last *successful*
observation — not the last attempt. A process that is running but failing every
request is down for the purposes of this system.

### Detection

Gap detection and alerting belong to the collector itself. A gap that is noticed
weeks later has already contaminated whatever was computed from that window.

### Effect on freshness and replay

Any state reconstructed over a gap window is untrustworthy by construction and
must be marked as such (§7). Evaluation runs covering a gap either exclude that
window explicitly or report it — they never silently interpolate across it.

---

## 10. Observation Contract

Fixed before the collector's first run. The argument is the same one that puts
the collector ahead of the domain model, applied one level deeper:

> Schemas can be migrated. **A field that was never collected cannot be
> back-filled** — it is as unrecoverable as elapsed time.

The collector may start before the domain model, the risk engine, or any type
definitions exist. It may not start before these fields are decided.

### Field slots

```text
identity      provider journey id, train number, service date
              — all three slots exist even when a provider fills only some (§3)

timestamps    scheduled       what the timetable said
              observed        when the provider says it happened
              effective       when the change takes effect
              received        when this process ingested it
              — four distinct slots; collapsing any pair loses replay fidelity (§6)

state         delay, platform, cancellation flags, stop pattern,
              raw provider status value as supplied

field envelope
              availability: present | not_provided | unavailable | malformed
              provenance:   reported | derived | absent            (§8)
              derivation_rule_id
              raw_value_reference

record        provider identifier
              schema version
```

### Missing is a value, not an empty string

**A slot existing does not mean a provider fills it.** Many will not supply a
journey identifier, an observation time, or an effective time. A contract that
demands all of them forces the collector into the two worst options: invent the
value, or throw the observation away. Both are forbidden — the first by
`AGENTS.md` **I5** and **I9**, the second because the discarded data cannot be
recovered.

Every optional slot therefore carries two orthogonal dimensions:

```text
availability
  present         a normalized value is available
  not_provided    this provider never supplies this field
  unavailable     this provider supplies it, but not for this observation
  malformed       supplied but unparseable

provenance
  reported        the usable or malformed value came from the provider
  derived         computed by this system
  absent          no value exists
```

`not_provided` and `unavailable` must stay distinct: the first is a property of
the feed, the second of a moment. Collapsing them destroys the ability to tell a
provider limitation from a provider outage.

Valid combinations are deliberately narrow:

```text
present + reported       provider supplied a usable value
present + derived        system computed a usable value; derivation_rule_id required
not_provided + absent    feed-level absence
unavailable + absent     observation-level absence
malformed + reported     provider supplied an unusable value
```

`raw_value_reference` is populated only when the provider verdict permits
retaining that raw value. Availability and provenance are not collapsed into a
single enum: doing so would make combinations such as *present and derived*
impossible to express without ambiguity.

### The one field that must always be present

```text
received_at     when this process ingested the observation
```

It is the only field this system generates itself and can therefore always
guarantee. Every other field may legitimately be absent.

### Derivation must be labelled

When a value is computed rather than reported — an effective time inferred from a
delay, an identity reconstructed from number plus date — it is stored with
`availability = present`, `provenance = derived`, and the stable
`derivation_rule_id`. A derived value is never written into the slot a reported
value would occupy without that label (`AGENTS.md` **I9**).

### Retain, degrade, or reject

```text
received_at missing or unparseable      → reject; the observation is unusable
identity entirely absent                → retain, flag unlinkable, exclude from
                                          run-level analysis, keep for A7 counting
any other field absent                  → retain, status recorded
payload unparseable at the top level    → mark malformed and alert; retain raw
                                          only when provider terms permit it
```

When raw retention is permitted, a malformed record carries a reference to the
retained raw payload. When it is prohibited, retain only the error
classification, `received_at`, provider identifier, payload byte length, and an
irreversible payload digest; the raw bytes and raw value are discarded. This
fallback is metadata about an ingestion failure, not permission to reconstruct
or redistribute the payload.

**The default is retain within verified data rights.** Rejection is reserved for
the single case where the observation cannot be placed in time at all. Anything
retained with a recorded status can be reinterpreted later; anything rejected
cannot. This default never overrides the provider verdict in
[`provider-evaluation.md`](provider-evaluation.md).

### Required per poll

```text
poll attempted        timestamp, target scope
poll succeeded        or the failure classification
gap marker            explicit "no observation in this window"     (§9)
heartbeat             last successful observation time
```

### Open until provider terms are known

```text
raw payload retained?   ← gated by AGENTS.md I15 and docs/provider-evaluation.md
retention period        ← same
```

Retaining the raw payload is the single cheapest insurance against a
normalization mistake discovered later — and it is exactly the thing provider
terms are most likely to forbid. Decide it deliberately, not by default.
