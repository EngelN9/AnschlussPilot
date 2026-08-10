# Binding Scenario Ruleset

**Ruleset version: `UNSET` — no rules defined yet.**
**Carrier-conditions snapshot: `UNSET`.**

Phase 0 reports the opportunity rate under two ticket-binding scenarios
(`README.md` §3 A6). Stop condition **S3** is the ratio between them. That
number is only reproducible if the rules producing it are written down and
versioned — otherwise "what a bound ticket would permit" silently means
"whatever the code did that week".

> [!IMPORTANT]
> **This is a sensitivity assumption, not a legal determination.**
> The rules below model *which candidate actions to admit* in an analysis. They
> are not a statement of what any passenger is entitled to do, and nothing here
> may be surfaced to a user as entitlement (`AGENTS.md` **I14**).
> Passenger-rights adjudication remains out of scope.

> **Update trigger:** any change bumps the version and **invalidates every
> measurement produced under the previous version**. Re-running S3 requires
> re-running the analysis, not re-labelling the result. Checked at deliverable
> completion (`AGENTS.md` §4).

---

## 1. Structure

Each rule states, for a class of ticket, which candidate actions are admissible
at a given decision time.

```text
rule_id
fare_class            e.g. flexible, discounted-bound, regional pass, unknown
operator              which carrier's conditions this models
valid_from            date this rule reflects conditions from
source                URL of the conditions consulted + date consulted
admits                which candidate actions, under which preconditions
relief_condition      what lifts the binding, and the threshold
unknown_behaviour     what this rule does when a precondition is unobservable
```

`unknown_behaviour` is mandatory. A rule that cannot say what it does under
uncertainty will silently default, and the default will be optimistic
(`AGENTS.md` **I4**).

---

## 2. Scenarios

Exactly two are computed in Phase 0. More scenarios would suggest a precision
the inputs do not support.

| Scenario | Definition |
| --- | --- |
| `UNBOUND` | All operationally feasible candidates admitted. Models a fully flexible ticket. **Upper bound.** |
| `BOUND` | Only candidates admissible under the discounted-bound ruleset at the evaluated decision time. **Lower bound.** |

Reported as a band. **Neither end is "the" rate** — see
[`modelling-and-evaluation.md`](modelling-and-evaluation.md) §8.

---

## 3. Carrier-Conditions Input

The ticket issuer or train operator whose conditions govern a candidate may be
different from the realtime data provider. The data-rights matrix in
[`provider-evaluation.md`](provider-evaluation.md) therefore does **not** answer
fare validity, binding relief, or passenger-rights questions.

Before ruleset `v1` is written, perform a separate carrier-conditions check and
record:

```text
conditions_snapshot_id
ticket issuer / operator
exact primary-source URL
document or page version
valid_from
date consulted
scope and known exceptions
```

This check supplies analytical assumptions only. It does not create a legal
rules engine and does not change the runtime position in `AGENTS.md` I14.

---

## 4. Ruleset

_Empty. Requires the independent carrier-conditions check above, and must be
written from the carrier's own current conditions, never from memory
(`AGENTS.md` **I2**)._

| rule_id | fare_class | operator | valid_from | admits | relief_condition | unknown_behaviour | source |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ? | ? | ? | ? | ? | ? | ? | ? |

---

## 5. Recording a Measurement

Every reported S3 value carries:

```text
ruleset version
carrier-conditions snapshot id
Phase 0 protocol version
scenario definitions used
decision-time semantics (relief evaluated at decision time, not in hindsight)
```

A measurement without a ruleset version attached is not a result. It is a number.
The complete freeze record lives in
[`phase0-protocol.md`](phase0-protocol.md); this file remains the canonical home
of the binding rules themselves.
