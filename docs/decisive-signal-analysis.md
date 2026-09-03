# Decisive-Signal Analysis — can `REROUTE_EARLY` be decided without the hold flag?

**Status: `COMPLETE — paper analysis`. Verdict: conditional pass (see §6).**
**Executes [`decisions.md`](decisions.md) D049 activity (a). Opens A8.**

> **Update trigger:** revise if the hold-duration bound in §3 is established
> against a provider statement, if **A8** is measured, or if the candidate action
> set in [`decision-model.md`](decision-model.md) changes. Checked in
> `AGENTS.md` §4.

> [!IMPORTANT]
> **No data was fetched, polled or stored to produce this document.** It is a
> structural argument over the model already specified in `README.md` §6 and
> [`decision-model.md`](decision-model.md). It changes **no** matrix field: A2c,
> A3c and A4 remain `UNKNOWN`, and it authorises no collection (**I15**, D044).

---

## 1. The question

D049 authorised determining, on paper, whether the Phase 0 decisive signal can be
defined on **expected arrival at the final destination at the decision time**,
using forecast and cancellation data, with the *Anschlusssicherung* hold flag as
one input that degrades to `UNKNOWN` when absent — rather than as a precondition.

The concrete test for this document:

> Does a forecast-only estimator separate `CONTINUE_CURRENT_PLAN` from
> `REROUTE_EARLY` at a decision lead time of **≥ 10 minutes** before the latest
> action time?

**Scope limit, stated first.** This asks about **one action pair only**:
`CONTINUE_CURRENT_PLAN` vs `REROUTE_EARLY`. That is the pair D046 narrowed the
Phase 0A kill-check to. It does **not** ask about `WAIT_FOR_CONNECTION` vs
`TAKE_LATER_CONNECTION` at the transfer station itself, and §6 records why the
answer there is different.

---

## 2. The estimator under test

`from repo` — `README.md` §6, [`decision-model.md`](decision-model.md) §1.

Notation: `Cur` is the current service, `T` the transfer station, `C` the booked
connection, `D` the final destination, and `T'` an upstream station at which an
alternative onward path exists.

```text
B_eff = dep_forecast(C, T) - arr_forecast(Cur, T) - T_transfer - T_safety
```

Expected final-destination arrival under each action:

```text
E[arr(D) | CONTINUE]      = p * arr(C, D) + (1 - p) * arr(C_fallback, D)
E[arr(D) | REROUTE_EARLY] = arr(C_alt, D)
```

with `p = P(catch C)`, `C_fallback` the next feasible service on the same
relation after `C`, and `C_alt` the alternative reached by alighting at `T'`.

**Where the hold flag enters.** It enters `p`, and nowhere else. A *wartet* state
drives `p` toward 1 for one candidate; *nicht wartet* drives it toward 0. It does
not appear in `arr(C, D)`, `arr(C_fallback, D)`, `arr(C_alt, D)`, or in `B_eff`.
This is the structural fact the rest of the document rests on.

**The framing error being corrected.** The hold flag has been treated as a
feasibility precondition for the entire measurement. It is a **variance-reduction
input on one term of one branch**. It sharpens a branch; it does not create one.

---

## 3. The hold-relevant band

A hold is bounded. Connection-securing holds a connecting service for a limited
number of minutes; beyond that bound the connection departs regardless of intent,
because holding longer propagates delay into the connecting service's own
downstream commitments.

Let `h_max` be that bound.

> **`requires verification`.** `h_max` is not established here. Public
> descriptions of *Anschlusssicherung* indicate a small number of minutes for
> routine holds, with longer dispatcher-discretion exceptions. No provider
> statement has been read. **Nothing below depends on a specific value** — only
> on `h_max` being bounded and small relative to forecast error, which §4 turns
> into a testable claim rather than an assumption.

The hold flag can change the `CONTINUE` / `REROUTE_EARLY` decision only when
`B_eff` lies inside:

```text
-h_max  <=  B_eff  <=  +T_safety
```

- **`B_eff > +T_safety`** — the connection is caught whether or not it waits. The
  flag is redundant.
- **`B_eff < -h_max`** — the connection cannot be held long enough to be caught.
  The flag is redundant, and a forecast-only estimator is *correct with certainty*
  in this region.
- **Inside the band** — the flag is decisive.

**The flag's decision-relevant region is therefore a band of width
`h_max + T_safety`**, plausibly on the order of 5–12 minutes of `B_eff`. It is
not a precondition for the estimator; it is a refinement over a bounded interval.

---

## 4. The lead-time argument

This is the load-bearing step.

`REROUTE_EARLY` must be acted upon **before** `T` — the passenger alights at `T'`,
upstream. The decision time `t` is therefore not "shortly before the connection
departs"; it is at or before `T'`, typically some tens of minutes ahead of
`arr(Cur, T)`.

Let `σ(t)` be the standard deviation of the arrival-delay forecast error for
`arr(Cur, T)` as forecast at time `t`.

At the lead times where `REROUTE_EARLY` is actionable, `B_eff` is itself known
only to within `σ(t)`. Two consequences follow:

1. **If `σ(t)` is comparable to or larger than `h_max + T_safety`**, the estimator
   cannot even locate `B_eff` inside or outside the hold band with confidence at
   decision time. The dominant uncertainty is the forecast, not the flag. Adding
   the flag reduces variance on the smaller of the two terms.
2. **The flag becomes decisive precisely as `REROUTE_EARLY` expires.** As the
   train approaches `T`, `σ(t)` collapses and `B_eff` resolves into or out of the
   band — but by then the passenger is past `T'`, and the remaining choice is
   `WAIT_FOR_CONNECTION` vs `TAKE_LATER_CONNECTION`, not `REROUTE_EARLY`. The
   `window_state` vocabulary in [`decision-model.md`](decision-model.md) already
   names this: the flag arrives useful at `EXPIRED`.

**This is the A6 collision seen from the other side.** A6 records that
`REROUTE_EARLY` is most valuable when the passenger is least free to act on it.
This section records that the hold flag is most informative when `REROUTE_EARLY`
is no longer available. Both say the same thing: the early window is where the
product lives, and it is where the sharpest signals are absent.

---

## 5. Constructed scenarios

> [!WARNING]
> **These are constructed parametric scenarios, not observed cases, and not drawn
> from any published timetable.** Times are illustrative. They test the logic of
> §2–§4, not the frequency of anything. Nothing here is evidence about A1, and
> none of it moves the Phase 0A counts, which remain **0/5** and **0/15**
> (**I2**, **I17**).

Assume `T_transfer + T_safety = 8 min` throughout, and an illustrative
`h_max = 5 min` for the §3 band. `δ` is the forecast delay of `Cur` at decision
time `t`; `B_sched` is the scheduled transfer buffer.

| # | δ at `t` | `B_sched` | `B_eff` | Fallback headway | Reroute gain vs fallback | Forecast-only decision | Hold flag decisive? |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **S-1** | 5 | 18 | **+13** | 60 | — | `CONTINUE` | **No** — outside band; caught regardless |
| **S-2** | 35 | 12 | **−23** | 60 | +40 min | `REROUTE_EARLY` | **No** — outside band; `h_max` cannot bridge 23 min |
| **S-3** | 14 | 12 | **−2** | 60 | +38 min | **marginal** | **Yes** — inside band |
| **S-4** | 30 | 10 | **−20** | 20 | +8 min | `CONTINUE` (accept the miss) | **No** — reroute gain below the material-improvement filter |
| **S-5** | 12, drifting | 12 | **≈0 ± σ** | 60 | +38 min | **depends on `σ`** | Dominated by forecast error, not by the flag |

**Reading.**

- **S-1, S-2 and S-4 are decided forecast-only, with the flag contributing
  nothing.** Note especially **S-4**: a *certain miss* still yields `CONTINUE`,
  because the fallback is dense and the reroute gain falls below the
  material-improvement rung of the filter ladder. That is **I3** in action — a
  high-risk transfer whose correct action is to continue — and it is invisible to
  any tool that reports connection risk instead of comparing outcomes.
- **S-3 is the genuine hold-flag case.** Inside the band, the flag is worth real
  accuracy. But §4 applies: at a decision time 10+ minutes before the latest
  action, a nominal `B_eff` of −2 is not distinguishable from +3 or −7 without
  knowing `σ`.
- **S-5 is the case that is actually load-bearing.** The decision turns entirely
  on forecast error, with the flag irrelevant at that lead time. **This scenario
  is A8.**

---

## 6. Verdict

**Conditional pass, against the test in §1.**

> A forecast-only estimator **can** separate `CONTINUE_CURRENT_PLAN` from
> `REROUTE_EARLY` at ≥ 10 minutes of lead time for every case in which `B_eff`
> lies outside the bounded hold band of §3. The hold flag is a variance-reduction
> input over a band of plausibly 5–12 minutes, not a precondition for the
> comparison. **It is therefore not required in order to measure A1 for the
> `REROUTE_EARLY` kill-check.**

**The condition, and it is not a small one.** The argument replaces a dependency
on the hold flag with a dependency on **forecast informativeness** — `σ(t)` at the
20–40 minute horizons where `REROUTE_EARLY` is actionable. That quantity is
untested, and it is now load-bearing for the whole product. It is recorded as
**A8** in `README.md` §3 with a paired stop condition **S13**, and it is testable
on the same free feed and in the same sitting as the **A7** identity spike.

**Two things this verdict does not say.**

1. **It does not say the hold flag is unimportant.** For `WAIT_FOR_CONNECTION` vs
   `TAKE_LATER_CONNECTION` at the transfer station — short lead time, small `σ`,
   `B_eff` frequently inside the band — the flag is decisive, and a product
   covering that action pair needs it. D046 narrowed Phase 0A away from that pair;
   this verdict is scoped to that narrowing and expires with it.
2. **It does not open the rights gate.** A2c, A3c and A4 remain `UNKNOWN`. What
   changes is that they are no longer *existential* for the Phase 0A measurement:
   a published-licence feed carrying forecasts and cancellations is sufficient for
   the estimator described here, subject to the licence read D049(b) requires.

---

## 7. Effect on D049's reversal condition

D049 states the reversal condition as: *"the paper analysis showing that a
forecast-only decisive signal cannot separate `CONTINUE` from `REROUTE_EARLY` at
a useful lead time, which would make the hold flag genuinely mandatory and push
the project toward **S12**."*

**That condition did not trigger.** The separation holds outside the hold band,
and the band is bounded. **S12 does not fire.** The DELFI thread continues on its
recorded schedule (reminder 2026-09-07, `inconclusive` no earlier than
2026-09-17), unchanged and no longer gating any data-dependent work.
