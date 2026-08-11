# Modelling and Evaluation

Detail for [`AGENTS.md`](../AGENTS.md) §1 (I7, I12). Read before touching the
historical store, the replay harness, backtesting, or any statistical model.

**Nothing in this document is currently implemented.** It describes the standard
that applies when it is.

> **Update trigger:** revise when the historical schema, replay harness,
> baseline definition, evaluation metrics, or any model changes. Checked in
> `AGENTS.md` §4.

---

## 1. Historical Reconstruction

The historical store is useful only if it can answer, for any past moment:

```text
What was known at time t?
What journey state existed?
What actions were available?
What would the policy have recommended?
What actually happened afterward?
```

Storing final delay values does not support any of this. Preserve the temporal
structure and provenance of observations, subject to provider terms
(`AGENTS.md` I15).

---

## 2. Measurement Design

Decided **before** collection starts. A sample size chosen after seeing the data
is not a sample size, it is selective reporting.

The complete combination of corridor, provider snapshot, observation schema,
binding rules, episode construction, enumeration, baseline, policy, and
measurement window is frozen in
[`phase0-protocol.md`](phase0-protocol.md). This document owns the statistical
rules; the manifest records the exact versions and Git commit used for a run.

### Target n

The A1 criterion has two halves — how often the opportunity occurs, and how much
it is worth. They need very different sample sizes.

| Goal | Requirement |
| --- | --- |
| Estimate an opportunity rate near 5% to ±2pp (95% CI) | **n ≈ 450** monitored transfers |
| Characterize the *magnitude* distribution of those opportunities | 50–100 positive cases → **n ≈ 1000–2000** |

The second row governs. An estimate of the rate with ~22 positive cases says
nothing about whether the opportunities are worth 6 minutes or 45, and the
expected-value judgement in [`README.md`](../README.md) §3 A1 needs both.

### Analysis unit and clustering

**The n above is not a count of independent observations**, and treating it as
one is the fastest way to produce a confident wrong answer.

Enumerated itineraries share structure massively: dozens of them ride the same
delayed train, on the same day, through the same disruption, onto the same
alternatives. Their outcomes are correlated by construction. Pooling them as
independent samples produces confidence intervals that are far too narrow and an
effective sample size far smaller than the raw count.

- **Analysis unit: the disruption episode** — the set of itineraries affected by
  the same underlying disruption on the same corridor-day, constructed by the
  frozen rules below. Independence is claimed between episodes, never within one.
- **Report both counts.** Enumerated transfers *and* independent episodes. The
  second is the one that constrains what can be concluded.
- **Confidence intervals by cluster bootstrap** at the episode level. Report the
  **effective sample size**, not only the raw n.
- **Never convert an enumerated rate into a passenger encounter rate.** *"3% of
  enumerated transfers"* and *"a passenger meets this every N journeys"* are
  different quantities; the second needs real demand weighting, which Phase 0
  does not have.

> **Honest consequence.** Episode-level n is much harder to reach than
> transfer-level n — a single storm day may contribute one episode, not four
> hundred observations. This can extend the measurement window substantially and
> puts pressure on stop condition **S8** (`README.md` §12). That tension is real
> and is not resolved by relabelling clusters as independent.

### Episode construction — frozen before measurement

"The same underlying disruption" is not an operational definition. Claiming
independence between episodes while leaving their boundaries to judgement moves
the arbitrariness rather than removing it — and boundaries drawn after seeing
results will be drawn where they help.

**These rules are fixed before the measurement window opens, alongside the policy
freeze below.**

```text
temporal window     two events belong to the same episode if they occur
                    within W minutes of each other        W = TBD, frozen in advance

service relation    shared service run, or shared station within the window,
                    or an explicit provider-supplied incident reference

merge               two candidate episodes sharing any service run merge
split               a gap longer than W with no shared service splits them

boundary            episodes do not span corridor-days; a cross-midnight
                    disruption is assigned by service date (railway-domain.md §6)
```

Also fixed in advance:

- **Minimum episode count** below which no rate is reported at all — a bootstrap
  over eight episodes is arithmetic, not evidence.
- **Clustering sensitivity analysis** — repeat the headline result under a wider
  and a narrower `W`. If the conclusion flips, the conclusion is about the
  clustering choice, not about the railway, and must be reported as such.

**Residual risk, stated:** episodes constructed this way are still only
*approximately* independent — a national-scale weather event correlates episodes
across an entire day. Where that occurs, corridor-day is the safer unit and the
effective sample falls further.

The manifest records `W_primary`, `W_narrow`, `W_wide`, the minimum episode
count, and the corridor-day fallback rule. `TBD` or `UNSET` in any of those slots
blocks measurement; it is not permission to choose a value during analysis.

### Reaching n: synthetic itineraries

Real journeys on one corridor produce nowhere near that volume in a useful
timeframe. Phase 0 therefore needs **no real users**: with corridor-wide
collection (`README.md` §4, decision 1), enumerate plausible `A → B → C`
itineraries from the timetable and evaluate every one of them. This converts a
multi-month wait into weeks.

Here, *plausible* is not analyst judgement. The eligible population, exact
one-transfer rule, corridor boundary, timetable window, transfer bounds, route
detour limit, deduplication key, and equal-weighting rule are frozen in
[`phase0-protocol.md`](phase0-protocol.md) §3 before measurement.

An alternative that was not fully observed is not evidence that no opportunity
existed. A1 therefore reports evaluable-conditional coverage plus lower and upper
bounds over the full eligible population, with out-of-scope and insufficient-data
cases explicit, as specified in the protocol manifest §4.

### Two uncertainties, never merged

A1 now carries **two independent kinds of uncertainty**, and reporting either
without the other misleads:

```text
sampling uncertainty    how much the result would move on another draw
                        → clustered bootstrap over disruption episodes

coverage uncertainty    how much of the population was never observable
                        → the o/N … (o+u)/N interval, width exactly u/N
```

Rules:

- **Bootstrap inside each bound.** Compute the clustered interval separately for
  the lower and the upper bound, and report the result as a nested interval:

  ```text
  o/N [CI_low]  …  (o+u)/N [CI_high]        coverage gap u/N = ..%
  ```

- **Never publish a confidence interval without the coverage gap beside it.** A
  tight CI on `o/n` while `u/N` is 25% is the most misleading form this result
  can take: it presents precision about a subpopulation as precision about the
  question.
- **Never combine them into one interval.** They answer different questions —
  one is about resampling, the other about what was never in the sample. A merged
  interval leaves neither recoverable.

If the two point in different directions — a narrow CI inside a wide coverage
band — the honest headline is the coverage band, and **S10** (`README.md` §12)
governs whether the result may be used as a gate at all.

**Stated limitation.** Enumerated itineraries are not distributed like real
passenger demand — they over-represent routings nobody books. Any reported rate
must say so. The measurement answers *"how often does the opportunity exist on
this corridor"*, not *"how often would our users encounter it"*.

### Corridor conditionality

Alternative density drives the result. A corridor with frequent alternatives
produces systematically more opportunities than a sparse one.

Every reported A1 figure carries the corridor it was measured on and that
corridor's alternative density. **A1 is not extrapolated across corridors**
without measuring the second one.

**One corridor is measured to completion first** (`decisions.md` D009). A second
corridor would strengthen the finding, but doubles the time to any finding at
all, and elapsed time is the dominant risk to completion. A single-corridor
result carrying an explicit non-extrapolation statement is complete and
publishable; two unfinished corridors are not.

### Policy freeze and holdout

**Deterministic rules overfit too.** Thresholds, safety margins, and filter order
are free parameters; tuning them while watching the score is overfitting whether
or not a model is involved. The temporal-validation guidance in §6 is not an
ML-only concern — it applies here first.

**Primary approach — pre-registration.** Phase 0 does not fit or freeze a
weighted utility function. It freezes the lexicographic filter order,
material-improvement threshold, intervention bar, safety margins, and stability
policy described in [`decision-model.md`](decision-model.md) §6. The rationale is
recorded in [`decisions.md`](decisions.md); exact versions and the corresponding
Git commit are recorded in [`phase0-protocol.md`](phase0-protocol.md). The
measurement is then scored once against those frozen rules.

**Fallback — time-ordered split**, if tuning turns out to be unavoidable:

```text
development window   first ~30% of collection    tuning permitted
        ↓ freeze, recorded in decisions.md with a date
final holdout        remaining ~70%              scored once
```

**Any change to the policy after the freeze invalidates that holdout.** Scoring
again requires a fresh window that the tuning never saw. This is inconvenient by
design — it is what stops "one more small adjustment" from silently consuming the
evidence.

---

## 3. Backtesting

Changes to rules, models, or decision policies are evaluated against historical
data before production use. A backtest preserves:

- chronological information availability;
- provider-state semantics as they were;
- which candidate actions were actually available;
- decision timestamps;
- observed outcomes.

> **Never use future observations to improve past decisions.**

If the replay harness cannot reproduce the information state at time *t*, the
backtest result means nothing.

---

## 4. Baselines First

Establish meaningful deterministic baselines before any model:

```text
simple transfer-buffer rule
risk-threshold rule
continue-unless-impossible
next-reasonable-connection rule
```

ML is evaluated against the baseline, never against "no system at all".

---

## 5. When a Model Is Allowed

All eight required before a model ships:

1. a clear prediction target;
2. adequate historical data;
3. temporally correct labels;
4. a deterministic baseline to beat;
5. a leakage analysis;
6. appropriate evaluation metrics;
7. documented uncertainty;
8. evidence of **product** improvement.

Plausible eventual targets:

$$P(\text{miss connection} \mid X_t) \qquad P(T_{\text{arrival}} \le t \mid X_t) \qquad P(Y \mid X_t, a)$$

The last — action-conditioned outcomes — is the one that actually matches the
product. The first two are stepping stones.

---

## 6. Leakage

Railway prediction is unusually vulnerable. Typical leaks:

- final arrival delay used as a feature;
- cancellation events that occurred after prediction time;
- later platform changes;
- any downstream event post-dating the decision;
- outcome labels joined back into inputs.

Random row-level train/test splitting is usually invalid here. Prefer temporal
splits, and check that the split boundary respects observation *ingestion* time,
not just scheduled time.

---

## 7. Calibration

Probabilistic outputs are evaluated probabilistically: Brier score, log loss,
calibration curves, reliability diagrams, temporal holdouts, confidence
intervals, subgroup evaluation, distribution-shift monitoring.

If the product displays `78%`, then cases assigned ≈78% must fail ≈78% of the
time over an appropriate population. **Do not display a number merely because a
classifier emitted `0.78`.**

If calibration is inadequate, categorical risk states are more honest and more
useful.

---

## 8. Evaluate the Policy, Not Only the Prediction

A model can improve AUC while making worse journey recommendations. Evaluate
product outcomes:

```text
destination delay                successful journey completion
missed connections               false interventions
unnecessary rerouting            warning lead time
recommendation stability         unknown-state frequency
```

Targets for these live in [`README.md`](../README.md) §12.

**Report under each binding scenario.** Every one of these outcomes is reported
twice: once admitting all candidates (`UNBOUND`), once admitting only those a
bound ticket would permit (`BOUND`) — see `decision-model.md` §5. A policy that
scores well by recommending routes a bound passenger could not take has not
improved anything for that passenger.

**These are scenarios, not observations.** Phase 0 has no passengers and
therefore no measured binding status; the two numbers are the ends of a
sensitivity band, and the gap between them *is* the A6 finding in
[`README.md`](../README.md) §3. Do not report either end as *the* rate.

**The best predictive model is not automatically the best product policy.**

---

## 9. Operational Metrics

Prefer metrics that expose user-impacting failure:

```text
provider availability            provider latency
data freshness                   normalization failures
state-reconciliation failures    risk-evaluation failures
alternative-generation failures  decision-engine failures
recommendation changes           API latency
database health
```

Avoid vanity product analytics. A system built to compensate for unreliable
travel must itself fail transparently. Analytics respect the privacy rules in
`AGENTS.md` I16.
