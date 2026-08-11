# Testing Catalogue

Detail for [`AGENTS.md`](../AGENTS.md) §3 step 6 and §4. This is the list of
cases that must eventually have tests — not a claim that any of them do.

> **Update trigger:** revise whenever a new railway edge case, decision case,
> provider behaviour, or failure mode is discovered — especially one found in
> production rather than in a test. Checked in `AGENTS.md` §4.

Core decision logic must be testable without a browser, live railway APIs,
production credentials, or production infrastructure. Tests supply journey state,
observations, candidate alternatives, and expected outcomes, then assert the
resulting recommendation.

---

## 1. Railway Scenarios

```text
normal connection
incoming delay
outgoing delay
shrinking transfer buffer
delay correction (backwards revision)
platform change
full cancellation
partial cancellation
changed stopping pattern
changed train number
split / join service
through service
replacement service
service terminated short of destination
duplicate realtime event
out-of-order realtime event
provider timeout
stale data
missing data
same-name stations
cross-midnight journey
earlier rerouting opportunity
alternative becomes unavailable mid-journey
recommendation reversal pressure
```

---

## 2. Decision Cases

These exist specifically to prevent accidental coupling between risk state and
action (`AGENTS.md` I3):

```text
HIGH_RISK but CONTINUE is correct
    — connecting service is also heavily delayed

ATTENTION but REROUTE_EARLY is correct
    — a clearly better alternative exists before the transfer station

MISSED but a later alternative is preferred over immediate rerouting

UNKNOWN ⇒ NO_RELIABLE_RECOMMENDATION
    — never a default to SAFE / CONTINUE

alternative arrives earlier but carries much higher risk
    — reliability target, not earliest-arrival target

minor improvement must not trigger a recommendation change
    — stability threshold

provider goes down mid-journey
    — degrades to UNAVAILABLE, retains last known state, warns
```

---

## 3. Ticket Constraint Cases

Guards `AGENTS.md` I14 and `docs/decision-model.md` §5. The failure mode these
prevent is the system quietly telling a passenger to do something they are not
free to do.

```text
BOUND + REROUTE_EARLY before any binding relief applies
    ⇒ candidate marked possibly-not-executable, still shown, clearly labelled

UNKNOWN binding
    ⇒ executability stays unknown; never rendered as executable

UNBOUND
    ⇒ full candidate set, no executability caveat

binding status absent from input
    ⇒ defaults to UNKNOWN, never to UNBOUND

any binding status
    ⇒ output contains no entitlement wording
      (no "entitled", "permitted", "may legally board", "you must")

outcome comparison
    ⇒ reportable both with and without non-executable candidates

Phase 0 evaluation (no passenger present)
    ⇒ scenarios enumerated, not inputs read;
      neither scenario reported as "the" rate

binding or carrier-conditions snapshot version is UNSET or mismatched
    ⇒ measurement gate stays closed; no A6 result is emitted
```

---

## 4. Decision Window Cases

Guards [`decision-model.md`](decision-model.md) §5. Actionability is domain
logic; a UI that hides an expired option is covering for a decision-layer defect.

```text
candidate whose action window has EXPIRED
    ⇒ never recommended, never counted as an available alternative

candidate CLOSING
    ⇒ surfaced with its remaining margin, not silently downgraded

alighting window derived from a departure time
    ⇒ closes before departure, not at it

decision_margin that cannot be pinned down
    ⇒ expressed as a range, never as a false-precise number

opportunity whose window had already closed at decision time
    ⇒ excluded from the A1 measurement — that is hindsight, not opportunity
```

---

## 5. Service Identity Tests

Required explicitly (`docs/railway-domain.md` §3):

```text
same train number, different service date
changed train number, same physical run
split service — correct leg followed
joined service — correct identity retained
replacement service linked to the original
same-name stations not conflated
```

---

## 6. Provider Contract Tests

Per adapter:

```text
valid response
missing optional fields
missing required fields
unknown status values
malformed timestamps
duplicate observations
cancellation payloads
platform-change payloads
syntactically valid but semantically unexpected values

received_at absent or unparseable
    ⇒ observation rejected as temporally unusable

optional slot omitted by the feed
    ⇒ availability = not_provided, provenance = absent

slot normally supported but unavailable for this observation
    ⇒ availability = unavailable, provenance = absent

provider-reported value
    ⇒ availability = present, provenance = reported

derived value
    ⇒ availability = present, provenance = derived;
      derivation_rule_id recorded; raw_value_reference recorded only when
      the provider verdict permits retaining the referenced raw value

invalid availability / provenance combination
    ⇒ rejected at the adapter boundary

top-level malformed payload, raw retention permitted
    ⇒ raw payload retained under the verified retention policy

top-level malformed payload, raw retention denied
    ⇒ retain only error class, received_at, provider, payload byte length,
      and irreversible digest; raw bytes are absent
```

Unknown provider values degrade gracefully into explicit unknowns.

---

## 7. Error Classification Tests

Assert these stay distinguishable rather than collapsing into one generic failure:

```text
provider unavailable          provider rejected request
malformed provider data       unsupported journey
ordinary missing information  ordinary UNKNOWN risk
internal processing error     database failure
```

---

## 8. Temporal Tests

```text
decision at time t uses only observations ingested by t
late-arriving observation does not retroactively alter a past decision record
replay reproduces the historical information state exactly
DST transition does not shift service dates
cross-midnight arrival computed on the correct service date
```

These protect the backtest. If they fail, every downstream evaluation result is
meaningless — see [`modelling-and-evaluation.md`](modelling-and-evaluation.md).

---

## 9. Phase 0 Enumeration and Coverage Tests

Guards [`phase0-protocol.md`](phase0-protocol.md). These are measurement-contract
tests, not claims that an implementation exists.

```text
same timetable + corridor + enumeration version
    ⇒ identical ordered itinerary set on repeated runs

duplicate candidate itineraries
    ⇒ deduplicated by
      (service_date, origin, transfer, destination, incoming_run, outgoing_run)

eligible itinerary crosses outside the frozen corridor graph
    ⇒ unobserved_out_of_scope, never counted as "no opportunity"

eligible itinerary lacks enough evidence for outcome comparison
    ⇒ insufficient_data, never counted as "no opportunity"

mixed evaluable / unknown population
    ⇒ reports N, n, u, opportunities, conditional rate, lower bound,
      upper bound, and coverage gap from the same frozen denominator

n = 0
    ⇒ conditional rate is undefined; bounds and coverage counts still reported

itinerary with any non-evaluable candidate
    ⇒ counted in u with its reason; produces no opportunity verdict;
      never counted as "no opportunity"

runtime facing the same partially covered itinerary
    ⇒ still ranks among evaluable candidates and recommends;
      does not refuse to answer

u/N above 20%
    ⇒ S10 fires: re-scope, not stop;
      A1 may be reported only as bounds and feeds no go/no-go

confidence interval reported without the coverage gap beside it
    ⇒ reporting-format defect

coverage bounds and sampling CI merged into one interval
    ⇒ defect; they answer different questions

high UNKNOWN risk-state share with low u/N
    ⇒ S4 path, not S10; re-scoping is not the remedy

episode rules run with W_primary, W_narrow and W_wide
    ⇒ all three are recorded; any conclusion flip is explicit

episode count below the frozen minimum
    ⇒ no headline rate is reported

protocol component version or measurement Git commit mismatch
    ⇒ prior result is invalid under the new protocol version
```

---

## 10. Collection Integrity Tests

Guards [`railway-domain.md`](railway-domain.md) §9. These are the only tests that
must exist before the collector runs in earnest, because what they protect
against cannot be repaired afterwards.

```text
collector restarts after an outage
    ⇒ the gap is recorded explicitly, never silently closed

replay covers a gap window
    ⇒ refuses to produce an assessment for that window;
      no optimistic default, no interpolation across it

heartbeat stops
    ⇒ alert fires; last successful observation time is reported,
      not last attempt

"no observation" vs. "observed, unchanged"
    ⇒ distinguishable at the storage layer, not inferred at read time

collector running but every request failing
    ⇒ treated as down, not as live
```
