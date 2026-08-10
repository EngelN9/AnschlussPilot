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

> **Nothing here is validated.** No user has been interviewed, no benchmark has
> been run, no segment has been measured.

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

### A5a — the feature gap exists today

The question is **not** *"does anyone else show alternatives?"* — several tools
do. It is:

> **At decision time, does any existing tool explicitly compare the
> counterfactual outcome of continuing against that of changing?**

### A5b — the gap is defensible long enough to matter

A gap that closes in six months does not justify a multi-month build.

### Method — archetypes, not replayed cases

**A live railway disruption cannot be replayed months later.** Fixing the
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

Tools            AnschlussPilot (once it exists), DB Navigator,
                 significant third-party tools

Cadence          Phase 0 start, Phase 0 report, Phase 0.5 exit
                 — checkpoints, not continuous monitoring

Recorded         per run: product name and version, date and time, account
                 state (logged in / anonymous), device, OS, app locale
                 per case: archetype, screenshots, interactions to reach the
                 comparison, elapsed time
```

**Comparisons are across samples, not paired.** Different cases each run is the
price of running at all; report per archetype so a change in one is not hidden by
the mix.

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

- **A5a fails** only when the same competing tool meets all four criteria on a
  majority of the complete benchmark sample **and** on a majority of the
  `REROUTE_EARLY` cases → **S6**; repivot or stop. Either denominator being
  incomplete makes the checkpoint `inconclusive`.
- **A5b fails** when overall coverage rises by at least **20 percentage points**
  between checkpoints **and** `REROUTE_EARLY` coverage rises in the same
  direction. Report the raw numerator and denominator for both populations at
  both checkpoints. Otherwise the trajectory gate has not fired.

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

| | Hypothesis | Test | Reversal |
| --- | --- | --- | --- |
| **B1** | **Comprehension** — the participant correctly states the recommended action and why | Paper scenarios | Consistent misreading → the decision-first UX premise is wrong, not just the layout |
| **B2** | **Stated willingness to act** — they say they would do it | Paper scenarios; **live use by the author only** | Understood but not trusted → the gap is explanation and uncertainty display, not accuracy |
| **B3** | **Acquisition friction** — reaching a useful decision is fast enough to be worth it | `time-to-first-useful-decision` including journey entry, **author's own trips** | Friction exceeds value → B2C not viable (§3, §4) |
| **B4** | **Willingness to pay / integrate** — someone would pay, or a surface would embed it | Conversations, no product | Neither → the project stays a research artifact, which is a valid outcome |

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

---

## 7. What This File Is Not

Not a business plan, not a pitch, not a projection. It is a register of
commercial hypotheses held to the same evidentiary standard as the technical
ones — so that a negative market finding is as informative, and as publishable,
as a negative technical one.
