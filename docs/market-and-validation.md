# Market and Validation

The technical layer of this project is well specified: railway domain, decision
model, measurement design, provider rights, tests. **This file is the missing
counterpart** — who the system is for, whether anyone would use it, and how it
would ever reach them.

It is held to the same standard as the technical documents: every claim is a
hypothesis with a stated test and a stated reversal condition, and nothing is
asserted as fact unless it was verified against a primary source
(`AGENTS.md` **I2**).

> **Update trigger:** revise when a customer hypothesis is tested, when the
> competitive benchmark is re-run, when a commercial hypothesis changes, or when
> any figure here is re-verified. Checked at deliverable completion
> (`AGENTS.md` §4).

> **Nothing here establishes product viability.** No user has been interviewed
> and no segment has been measured. The first D045 benchmark run closed
> `inconclusive` after one of thirty app observations. The initial D046 run is
> also `inconclusive / BLOCKED`: no candidate reached the frozen three-app
> denominator, so neither a continuation nor a stop/pivot result is supported.
> A separate D046 v2 denominator is pre-registered after all three anonymous
> app entries passed setup preflight; no v2 candidate has been inspected.
> No synthetic-user evaluation has been implemented or run.

---

## 1. Initial Customer Profile — a hypothesis, not a segment

*"German rail passengers"* is not an actionable segment. The working hypothesis
is much narrower:

> **A long-distance German rail traveller, on an itinerary with at least one
> transfer, currently experiencing a disruption, for whom a real alternative
> routing exists, whose time has high enough value to justify acting, and whose
> ticket permits — or may permit — changing.**

Every clause is a filter, and all six must hold simultaneously. That conjunction
is the product's real market, and it is much smaller than "rail passengers".

**Explicitly not the ICP:**

```text
direct journeys with no transfer          — nothing to decide
no reasonable alternative exists          — nothing to recommend
regional commuting                        — low stakes, high familiarity
all decision windows already closed       — too late to help
travellers indifferent to arrival time    — no value in intervening
```

**Where to look first (not a claim about the market):** frequent and business
travellers plausibly concentrate the conjunction above. This is a starting point
for recruitment in Phase 0.5, **not** an assertion that they are the market.

- **Test:** the segment analysis added to Phase 0 (`README.md` §4) — which
  stations / segments, journey shapes, and times of day within the measured
  corridor concentrate the opportunity — plus Phase 0.5 interviews.
- **Reversal:** if opportunities are spread evenly across all journey types, the
  ICP framing is wrong and targeting has no leverage.

---

## 2. Competitive Benchmark (A5a / A5b)

A one-off existence check is not enough, because the gap can close while the
project is being built.

### Current Phase 0A — isolate `REROUTE_EARLY`

The immediate question is narrower than a general feature inventory:

> **Before the action window closes, does an existing surface compare the
> destination outcome of continuing with that of rerouting before the threatened
> transfer, and is the forecast advantage large enough to matter?**

D045's five-archetype run is archived `inconclusive` at one of thirty planned
app observations. D046 starts a fresh denominator: target three, extend to at
most five, live-disrupted Bavarian `Fernverkehr → Nahverkehr` cases with an
actionable pre-transfer divergence. The scored apps are DB Navigator, MoBY and
Wohin·Du·Willst. This is a hypothesis-driven test set, not a market-share or
feature-completeness claim.

The initial D046 attempt inspected two candidates. One lacked a live timing or
connection disruption. The other was visible anonymously in DB Navigator and
MoBY while its action window remained open, but Wohin·Du·Willst repeatedly
failed to load any place or regional version before journey search. It was
therefore excluded under the frozen three-surface rule. The raw result is 0/5
complete cases and 0/15 scored observations; this run-specific
`surface_unavailable` result is not evidence that a product feature is absent.
See the [run artifact](benchmarks/phase0a-reroute-early-start-2026-08-12.md).

On 2026-08-24, a physical-device setup preflight reached the anonymous
journey-search entry in all three frozen apps. Because DB Navigator had also
changed version, D046 v2 starts a new denominator rather than reopening v1. Its
pre-registered method is in
[`benchmarks/phase0a-reroute-early-start-2026-08-24-v2.md`](benchmarks/phase0a-reroute-early-start-2026-08-24-v2.md).
Setup availability is not a scored observation, and v2 begins at zero.

The baseline ladder is:

```text
1. CONTINUE_CURRENT_PLAN
2. connection warning
3. manual alternatives search from disruption detail
4. Anschlussvormeldung / connection protection
```

Anschlussvormeldung is embedded in MoBY and Wohin·Du·Willst, not counted as a
fourth app. The
[official DB Regio Bayern FAQ](https://regional.bahn.de/regionen/bayern/service/anschluss-voranmeldung/anschlussvormeldung-faq)
documents the route-position check and late operational decision process. It
does not replace a live observation. No location is simulated and no connection
request is sent; without a genuine in-route observer, that live lane stays
`UNKNOWN`.

For every case, record `t_early`, `t_late` and the raw operands for:

- `projected_arrival_gain_t0`: the continue destination ETA minus the best
  reroute destination ETA at `t_early` — a forecast advantage, not actual time
  saved;
- `option_decay_count`: forecast-better alternatives open at `t_early` but no
  longer actionable at `t_late`;
- `decision_lead_time`: the best reroute deadline minus the first complete
  comparison time, plus its lead over the first existing-baseline warning.

Extra transfers, ticket binding, freshness, recommendation reversal and any
observable false-intervention outcome remain separate guardrails. Ticket
executability is `UNKNOWN` unless independent evidence establishes it.

The run is frozen in
[`benchmarks/phase0a-reroute-early-start-2026-08-12.md`](benchmarks/phase0a-reroute-early-start-2026-08-12.md).
It uses the same three-tap, two-minute, four-criterion decision-grade contract
below.

### A5a — the feature gap exists today

The question is **not** *"does anyone else show alternatives?"* — several tools
do. It is:

> **At decision time, does any existing tool explicitly compare the
> counterfactual outcome of continuing against that of changing?**

### A5b — the gap is defensible long enough to matter

A gap that closes in six months does not justify a multi-month build.

### Archived D045 method — archetypes, not replayed cases

The following block records D045 and is not the current denominator. **A live
railway disruption cannot be replayed months later.** Fixing the
individual cases would make the benchmark unrunnable after its first use. What is
fixed is the *archetype* and the inclusion criteria; the cases are sampled fresh
from live events at each checkpoint.

```text
Archetypes       fixed set of five
                 - incoming delay erodes a 10–15 min transfer
                 - outgoing service also delayed
                 - transfer connection cancelled outright
                 - an earlier reroute exists before the transfer station
                   (`REROUTE_EARLY` core archetype)
                 - realtime information degraded or absent

Inclusion        long-distance, ≥1 transfer, disruption live at observation time,
                 at least one alternative existing at that moment

Sample           exactly 2 qualifying cases per archetype at the first run
                 (10 total),
                 toward 20–30 by the Phase 0 report;
                 stratified across archetypes, not drawn freely

Tools            AnschlussPilot (once it exists); initial checkpoint:
                 DB Navigator, Trainline and Google Maps mobile apps
                 (a test set, not a market-share claim)

Cadence          Phase 0 start, Phase 0 report, Phase 0.5 exit
                 — checkpoints, not continuous monitoring

Recorded         per run: product name and version, date and time, account
                 state (logged in / anonymous), device, OS, app locale
                 per case: archetype, screenshots, interactions to reach the
                 comparison, elapsed time
```

**Across checkpoints, comparisons are across samples, not paired.** Different
fresh cases each run are the price of running at all; report per archetype so a
change in one is not hidden by the mix. **Within one checkpoint, the same case
is observed on every frozen surface.** A missing surface leaves that case and
the checkpoint denominator incomplete; it is not replaced by a web surface or a
different disruption.

Kept deliberately small at first: the first run stops after half a day. If ten
qualifying cases cannot be found in that time, record the checkpoint as
`inconclusive`, report the achieved counts by archetype, and continue to the next
planned deliverable. **Never infer from an incomplete sample that a competing
capability does not exist.** Re-run at the next checkpoint.

### What counts as a decision-grade comparison

Pre-defined, so the verdict is not decided by whoever runs the benchmark. All
four must hold:

```text
1. both branches shown   continuing and changing, in one view
2. outcome stated        expected destination arrival for each, not just
                         departure times of alternatives
3. at decision time      surfaced while the action is still available,
                         not after the connection is missed
4. reachable             within a small, recorded number of interactions
                         from the disrupted journey
```

For D045 and both D046 Phase 0A versions, *reachable* means within at most
three purposeful navigation taps and two minutes from the disrupted journey
detail. Scrolling is recorded but does not consume a navigation tap unless it
opens or changes a view. D045's archived evidence and result remain in
[`benchmarks/a5a-phase0-start-2026-08-12.md`](benchmarks/a5a-phase0-start-2026-08-12.md).

Anything less — an alternatives list, a "connection at risk" badge, a
push notification — is **not** a counterfactual comparison, however useful.

### Reversal, with thresholds

`S6` in `README.md` §12 is not a binary "does the feature exist". Record and
apply:

```text
coverage      share of benchmark cases where the comparison appears
quality       how many of the four criteria above are met
friction      interactions and elapsed time to reach it
trajectory    change in coverage between checkpoints
```

- **Phase 0A triggers S6 after three cases** when the same app reaches
  `all_four=yes` in at least `2/3`; otherwise sampling may extend to five, where
  at least `3/5` triggers S6.
- **Continuing beyond Phase 0A requires five unique cases and fifteen complete
  app observations**, plus at least `3/5` cases with forecast gain of 10 minutes
  or more, mean forecast gain of at least 10 minutes, at least `3/5` cases with
  option decay, and at least `3/5` with positive decision lead over an existing
  baseline. Informational sufficiency and acquisition rights must also pass.
- Any missing surface, temporal anchor, evidence manifest entry,
  Anschlussvormeldung live lane or acquisition-rights answer makes the overall
  Phase 0A verdict `inconclusive / BLOCKED`.
- **A5b fails** when overall coverage rises by at least **20 percentage points**
  between checkpoints **and** `REROUTE_EARLY` coverage rises in the same
  direction. Report the raw numerator and denominator for both populations at
  both checkpoints. Otherwise the trajectory gate has not fired.

#### The trajectory gate alone models the wrong threat

A percentage-point trajectory models a competitor *gradually improving*. The
actual threat is **discontinuous**: an incumbent that already holds the data, the
dispatch relationship and the users makes one product decision, and the gap
closes between two checkpoints. A trajectory gate cannot see a step function
until after the step.

So A5b is also **event-driven**. Any of the following triggers an immediate A5a
re-run, without waiting for the next checkpoint:

```text
any existing tool ships an explicit continue-vs-change outcome comparison

a "declare your intended connection" feature (passengers pre-registering a
transfer, operator responding with alternatives) expands in scope or geography

a public announcement of decision-grade connection guidance from an operator
or a major aggregator

a feed that exposes hold signals becomes available at a self-service tier
    — this cuts both ways: it weakens A5b and strengthens A2c
```

The Bavarian Anschlussvormeldung workflow is now verified from the official FAQ
as an incumbent connection-protection baseline. BEG (MoBY's operator) answered
the product-support enquiry in writing on 2026-08-13: eligible connections
carry no public list or marking and are derived from timetable data
(arrival/departure times, defined transfer walk times); only Nahverkehr and
Fernverkehr → Nahverkehr transfers within Bavaria qualify (S-Bahn excluded;
Fernverkehr generally does not wait for a delayed Nahverkehr service); the
pre-notification button appears only when a transfer is eligible, and tapping
it triggers a server-side plausibility check including the device's
geoposition along the requested connection. **BEG confirmed that MoBY does
not, before the wait decision, simultaneously show both "continue the current
journey" and "reroute early" together with the expected final-destination
arrival for either — the wait decision is communicated separately, shortly
before the transfer.** BEG could not answer for Wohin·Du·Willst, which it does
not operate (that app is run by DB Regio Bus Bayern). No test or demo access
is offered externally; a conceptual explainer video is published at
bahnland-bayern.de/de/anschluss. BEG granted permission to publish anonymised
screenshots of the public, no-login passenger interface in a non-commercial
report, with attribution "MoBY/BEG".

Per the frozen protocol
([`benchmarks/phase0a-reroute-early-start-2026-08-12.md`](benchmarks/phase0a-reroute-early-start-2026-08-12.md)
§2), a provider's written statement is primary evidence, not a scored live
observation, until a current surface or official current screen verifies it —
so this answer does not move the `0/5` complete-case or `0/15` observation
denominators, and does not by itself trigger S6. It strongly corroborates what
a live MoBY observation would show and closes the question this section
previously left open for MoBY specifically; Wohin·Du·Willst's equivalent
behaviour remains unverified.

Watching costs nothing until something fires. Not watching costs the difference
between learning in week two and learning at the Phase 0 report.

### Baseline observation (2026-08-10, pre-benchmark)

DB's own feature page for the digital travel companion describes realtime
connection information, push notification of changes such as platform switches,
and an *"Alternativen suchen"* button appearing on schedule deviations. It does
**not** describe comparing the outcome of continuing against the outcome of
changing.
Source: [bahn.de digitale Reisebegleitung](https://www.bahn.de/service/fahrplaene/digitale-reisebegleitung),
fetched 2026-08-10.

> **This is not evidence that the feature is absent.** A marketing page's silence
> says nothing about a shipped product. It is a prior to be tested, and it is
> exactly why A5a is run against the live app rather than against documentation.

---

## 3. Distribution Risk

**This is a first-class product risk, not a launch detail.**

The incumbent is not absent. Verified figure:

> **DB Navigator averaged 23.7 million active users per month** over the six
> months preceding the report's June 2026 status date.
> Source: [DB DSA transparency report](https://int.bahn.de/en/site-notice/transparency_report_dsa),
> fetched 2026-08-10.

That app already provides realtime trip monitoring, alternative search, and push
notifications. So the product question is not the one the technical documents
answer. It is:

> **Is the recommendation good enough that a passenger, mid-disruption, on a
> platform, under time pressure, opens a second application to get it?**

A decision that is 8 minutes better but costs 90 seconds of app-switching and
journey re-entry may be worth nothing. The technical thesis can pass completely
while this fails.

### Unverified claim, recorded as such

The review that prompted this section also stated that DB has publicly announced
continued 2026 improvements to disruption and alternative information in
DB Navigator / bahn.de, citing the DB annual report. **This could not be
verified** — the cited page returned navigation content only on 2026-08-10. It
is recorded here as a claim to check, not as a fact, and **no conclusion in this
repository rests on it**.

- **Test:** the Phase 0.5 friction metrics below, especially
  `time-to-first-useful-decision` including journey entry.
- **Reversal:** if entry friction consistently exceeds the decision's value, the
  standalone B2C product is not viable regardless of decision quality → §4.

---

## 4. Commercial Hypotheses

Two tracks, both open. Neither requires any code now.

### P-B2C — passenger-facing decision companion

The product is an app the traveller opens. Requires overcoming the distribution
risk in §3.

### P-B2B2C — decision engine inside an existing travel surface

The product is the decision layer, consumed by something that already has the
user: a corporate travel tool, an operator, an aggregator.

**Why keep this open.** If the algorithmic and data layers prove strong but user
acquisition proves weak, that is not failure — it is evidence about *where the
value sits*. Decision intelligence may be worth more than another travel app.

- **Test:** Phase 0.5 acquisition friction (B3) and integration willingness (B4).
- **Reversal:** decision quality is strong and acquisition friction is high →
  favour B2B2C. Decision quality is only modest but users adopt readily →
  favour B2C. Both weak → **S2** applies.

**Explicitly deferred:** no API design, no pricing, no partner outreach until
Phase 0 reports.

---

## 5. Phase 0.5 Hypotheses (B1–B4)

Phase 0 asks *can we make a useful decision?* These ask *will anyone act on it?*
They are separate questions and are not merged into the technical assumptions.

### Optional Phase 0.5-S — synthetic UX preflight

Phase 0.5-S is a **deferred, optional preflight** between a passing Phase 0
report and recruitment for the real Phase 0.5 pilot. Its contract is recorded in
[`synthetic-ux-preflight.md`](synthetic-ux-preflight.md); no harness, cohort,
dataset, model integration or result exists now.

If its separate implementation gate later opens, simulated users may inspect
human-authored frozen recommendation presentations to surface likely wording,
hierarchy and uncertainty-communication failures. A human may use those findings
to remove an obviously risky wording candidate or formulate questions for the
real pilot. The output remains hypothesis-generating evidence even when the raw
numerator and denominator are reproducible.

It does **not**:

- prove recommendation correctness, transfer feasibility, ticket permissibility,
  connection-hold observability or any A1–A7 proposition;
- measure real comprehension, trust, willingness to act, acquisition friction,
  willingness to pay, market size or safety;
- satisfy B1–B4, fire SB1–SB4, change S1–S12 or promote a product;
- replace D026's 2–3-person exploratory pilot and 5–8-person confirmatory round.

Abandoning the synthetic lane because its outputs are implausible, too costly or
legally unusable is not evidence against AnschlussPilot. Conversely, a favourable
synthetic run is not evidence that a passenger would understand or act.

| | Hypothesis | Test | Reversal |
| --- | --- | --- | --- |
| **B1** | **Comprehension** — the participant correctly states the recommended action and why | Paper scenarios | Consistent misreading → the decision-first UX premise is wrong, not just the layout |
| **B2** | **Stated willingness to act** — they say they would do it | Paper scenarios; **live use by the author only** | Understood but not trusted → the gap is explanation and uncertainty display, not accuracy |
| **B3** | **Acquisition friction** — reaching a useful decision is fast enough to be worth it | `time-to-first-useful-decision` including journey entry, **author's own trips** | Friction exceeds value → B2C not viable (§3, §4) |
| **B4** | **Willingness to pay / integrate** — someone would pay, or a surface would embed it | Conversations, no product | Neither → the project stays a research artifact, which is a valid outcome |

### Early B4 is evidence-only

B4 is a cheap way to expose integration interest, so one exploratory round may
run before the schema freeze under the property gate in `README.md` §4 and
[`decisions.md`](decisions.md) **D037**. It is capped at half a day and collects
evidence only.

It does **not**:

- change the Phase 0 observation schema or measurement question;
- select B2C, B2B2C, or research-only;
- authorize an API, interface, integration, or other product implementation;
- trigger SB4 merely because an exploratory contact did not express interest.

Phase 0 must first establish decision quality. Phase 0.5 then measures second-app
friction and completes the formal B4 round. Only that combined evidence selects a
commercial route (D042).

```text
B4 conversations        no product, no prototype, no commitment
                        ≤ half a day, qualifies under the property gate
                        in README §4
```

Its outcome is labelled exploratory context in the Phase 0 report, never a route
verdict.

### Exits, not only reversals

B1–B4 had reversal conditions — statements that change what one believes — but no
**exits**: conditions that stop or redirect the project. The technical track has
twelve. The market track had none, which quietly made market evidence unable to
change the plan.

| | Exit condition | Verdict |
| --- | --- | --- |
| **SB1** | Comprehension fails in the confirmatory round *after* the materials were revised once | **Stop the decision-first UX premise**, not the layout. Either the hierarchy is wrong or the decision cannot be conveyed under time pressure. Research track continues. |
| **SB2** | Participants understand the recommendation and still would not act on it, and the stated reason is not fixable by explanation or uncertainty display | **Drop P.** A correct recommendation nobody acts on has no product value. R stands. |
| **SB3** | `time-to-first-useful-decision`, including journey entry, exceeds the decision's own value on the author's real trips | **B2C not viable** — pivot to B2B2C, where the surface already holds the user (§4). |
| **SB4** | No buyer and no surface expresses interest after the formal post-Phase 0 B4 round | **Research-only.** Publish and stop building. The early exploratory round cannot fire this exit. |

These sit alongside `README.md` §12's S-conditions and are reported in the same
place. An exit fires on evidence, not on mood.

### Who gets live recommendations

**Only the author.** Everyone else sees paper scenarios.

Giving an unvalidated live recommendation to a stranger mid-journey can cause
them to miss a train. Until the policy has been validated, the only person whose
journey may be put at risk by it is the person who built it. Extending live use
beyond the author is a Phase 1 decision, not a Phase 0.5 convenience.

This also resolves an ambiguity: `README.md` §4 describes Phase 0.5 as
single-user, and B1/B2 recruit 5–8 participants. Both are true — participants
evaluate static scenarios; only the author acts on live output.

### Exploratory, then frozen, then confirmatory

Setting B1–B4 thresholds after seeing the first results, with no further round,
is the confirmation bias the technical side already pre-registers against
(`decisions.md` **D006**, **D014**). The market side gets the same discipline:

```text
1. exploratory pilot      2–3 participants; refine the materials and the
                          questions. Produces no verdict.
2. freeze                 thresholds written into decisions.md, with a date,
                          before any further participant is recruited
3. confirmatory round     5–8 new participants who did not see the pilot;
                          scored once against the frozen thresholds
```

Any material change to the prototype or the questions after the freeze requires a
new confirmatory round. Metrics are defined now; **thresholds are set at step 2,
which is after the pilot and before the evidence.**

Additional Phase 0.5 measures: recommendation comprehension rate, stated action
willingness, return intent, journey-input friction.

### Participant safety and data handling

Except for the author, participants evaluate static scenarios only; they do not
act on live recommendations. Research records use a participant ID. Audio is not
recorded by default. Recruitment and contact details stay outside the analysis
dataset and are never used as analytical variables.

---

## 6. Market Sizing — assumptions only

No sizing is attempted yet, and a number produced now would be the anchor
problem `decisions.md` **D006** exists to prevent.

What a defensible size would eventually require:

```text
long-distance journeys with ≥1 transfer      timetables            derivable
share experiencing relevant disruption        Phase 0, one corridor measured
share where an alternative materially helps   Phase 0, one corridor measured (A1)
share where the ticket permits acting         —                     NOT measured
                                              Phase 0 produces a scenario band
                                              (A6), not a population share;
                                              converting it needs a real fare mix
share of passengers who would act             Phase 0.5 (B2, B3)    not railway data
```

**Only the first three are measurable in Phase 0**, and the second and third only
for one corridor. The fourth needs a fare-mix distribution the project does not
have; the fifth cannot be derived from railway data at any point. The fifth is
also the one that decides whether this is a product or a research result.

Multiplying a measured share by a scenario bound produces a number with no
defensible interpretation. Do not do it.

### One order-of-magnitude ceiling check — once, early, explicitly crude

The rule against market sizing stands. But refusing to size is not the same as
refusing to notice a hard ceiling, and discovering one after a year of work would
be avoidable waste.

So: **one** deliberately crude funnel, computed once, written down, never
refined.

```text
long-distance journeys per year          published figure        requires verification
× share not arriving on time             published figure        requires verification
× share with a transfer
× share where a real alternative exists
× share where the ticket permits acting
× share who would open a second app
= addressable events per year            order of magnitude only
```

Rules that keep this from becoming a forecast:

- **Order of magnitude only.** Round to powers of ten. A result of "millions of
  events, tens of thousands of plausible users" is the intended precision; any
  figure with two significant digits is misuse.
- **Every input carries its source and a `requires verification` flag** until
  checked against a primary source (`AGENTS.md` **I2**).
- **It cannot justify continuing** — only stopping. A large number means nothing;
  a small number is informative.
- Not repeated, not updated, not used in any report as a market estimate.

> **Working prior, unverified.** Figures on the order of ~130 million German
> long-distance passenger journeys per year and roughly one third not arriving on
> time have been cited to this project but **not verified against a primary
> source**. They are recorded here as inputs to check, not as facts, and no
> conclusion rests on them.

---

## 7. Publishing Before the End

The Phase 0 report is this project's only distribution asset — and it does not
exist until the end, which is exactly when it is least useful.

Two problems follow from holding everything back:

- **No credibility during the build.** B2B2C conversations (B4) happen months
  before the report. Something citable must exist by then.
- **No external error correction.** Pre-registration protects against fooling
  oneself *during* measurement. It does nothing about a wrong premise. A solo
  project that produces no external output for a year has no mechanism to
  discover that someone already knows the answer is no.

**Publish the rights-and-access matrix around month two** — the provider
verdict, the A2c/A3c access findings, the carrier-conditions snapshot. It is
cheap, it is finished early, it is genuinely useful to others working on German
rail data, and it is the fastest way to attract a correction if the reading of
the terms is wrong.

Subject to `AGENTS.md` **I15**: publish the *findings about* terms, never
provider data whose redistribution is not permitted.

Later publishable units, in order of readiness: the identity-resolution result
(A7), the competitive benchmark method, then the Phase 0 report itself.

---

## 8. What This File Is Not

Not a business plan, not a pitch, not a projection. It is a register of
commercial hypotheses held to the same evidentiary standard as the technical
ones — so that a negative market finding is as informative, and as publishable,
as a negative technical one.
