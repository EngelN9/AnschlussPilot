# Decision Log

AnschlussPilot's product thesis is that reconstructing *what was known at the
time* matters more than storing the final outcome. This file applies that
principle to the project itself.

Without it, a superseded decision leaves no trace but a diff — and a diff records
what changed, not why it was believed in the first place.

> **Update trigger:** append an entry whenever a decision is made that a
> reasonable contributor could have made differently, or whenever evidence
> reverses an existing one. Never edit a past entry's reasoning — supersede it.
> Checked at **deliverable completion**, not per commit (`AGENTS.md` §4).

> **Volume note:** the original estimate of 8–12 Phase 0 entries was exceeded
> during specification convergence. The active-decision index below is now the
> reading path; historical entries remain intact and are never silently rewritten.

---

## Format

```markdown
### D0NN — Title

- **Date:**
- **Status:** active | superseded by D0NN | reversed
- **Evidence available at the time:**
- **Decision:**
- **Alternatives rejected:**
- **What would reverse this:**
```

The last field is the point of the exercise. A decision with no stated
reversal condition is a preference, not a decision.

---

## Active Decision Index

Use this index to find the current rule; read the entry for reasoning and
reversal conditions.

| Area | Active decisions |
| --- | --- |
| Phase 0 scope, order and delivery | D001, D002, D005, D007, D008, D009, D010, D011, D027 |
| Population, episodes and evaluation | D003, D006, D013, D020, D021, D024, D027 |
| Binding and observation evidence | D004, D012, D015, D022 (retention principle only), D023, D029 |
| Competitive, market and validation | D017, D018, D019, D025, D026, D028 |

Superseded entries remain below for provenance: D014 is superseded by D021 and
D027; D016 is superseded by D027; D022's combined status vocabulary is
superseded by D029 while its missing-data and retain-when-permitted principles
remain active.

---

## Entries

> **Provenance note.** D001–D007 were recorded retroactively on 2026-08-10, when
> this log was created. They describe decisions made during the preceding
> documentation work. Reasoning is reconstructed, not contemporaneous — treat it
> as slightly less reliable than entries written at the time. Every entry from
> D008 onward is contemporaneous.

### D001 — Phase 0 is offline replay with no user interface

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** No provider integration, no data, no
  validated assumptions. The product claim is about decision quality, which is
  measurable offline.
- **Decision:** Build collector → replay → baseline → measurement → report
  before any interface work.
- **Alternatives rejected:** UI-first (would display a policy not yet known to
  work); parallel UI and measurement (splits a single person's effort).
- **What would reverse this:** Evidence that the measurement cannot be performed
  offline — e.g. if the decisive signal only exists in a live session and cannot
  be captured for replay.

### D002 — Collection scope is corridor-wide

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** Labelling an opportunity requires the
  counterfactual — the alternative service's *actual* run. A journey-scoped
  collector cannot produce it, and the gap is unrecoverable.
- **Decision:** Capture all relevant services on the corridor, not only those
  used by monitored itineraries.
- **Alternatives rejected:** Journey-scoped collection (cheaper, permanently
  incapable of answering A1).
- **What would reverse this:** Provider rate limits that make corridor-wide
  polling infeasible — in which case the corridor shrinks, not the scope.

### D003 — Sample size is reached with synthetic itineraries

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** Real journeys on one corridor yield far
  too few transfers to characterize the magnitude distribution (see
  `modelling-and-evaluation.md` §2).
- **Decision:** Enumerate plausible itineraries from the timetable and evaluate
  all of them. Phase 0 needs no real users.
- **Alternatives rejected:** Waiting for real usage (months, and requires a
  product that does not exist yet).
- **What would reverse this:** Nothing likely. The known limitation — synthetic
  itineraries are not distributed like real demand — is disclosed rather than
  fixed.

### D004 — Ticket binding is a user input, never an inference

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** Recommending an unusable route produces a
  wrong outcome estimate. Adjudicating entitlement requires legal validation the
  project does not have.
- **Decision:** `TicketConstraint` is supplied by the passenger; candidates carry
  an executability annotation; the system never asserts a legal conclusion.
- **Alternatives rejected:** Ignoring ticket constraints (invalidates the
  headline measurement); inferring entitlement (out of scope and unsafe).
- **What would reverse this:** A validated, versioned, jurisdiction-aware rules
  engine — explicitly outside the current scope.

### D005 — The project targets a research result and a product prototype, in that order

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** The research deliverable is a strict subset
  of the product's foundation. One measurement report serves as both the research
  output and the product go/no-go.
- **Decision:** Sequence as R → P-minimal → P (`README.md` §4). Tag every
  deliverable `R` / `P` / `RP`. Any `P`-only work before the report exists is
  scope creep.
- **Alternatives rejected:** Research only (discards a cheap product signal);
  product only (builds a surface for an unvalidated policy); both simultaneously
  (removes the ability to refuse any work).
- **What would reverse this:** Provider terms that permit research use but
  prohibit the product — in which case P is dropped and R proceeds unchanged.

### D006 — Success targets stay `TBD`; stop conditions are set in advance

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** No measurements exist. An invented target
  becomes an anchor that outlives the guess behind it.
- **Decision:** `README.md` §12 targets remain `TBD` until data sets them. Stop
  conditions, by contrast, are fixed *before* collection begins.
- **Alternatives rejected:** Placeholder targets (previously present, removed);
  deciding stop conditions after seeing results (indistinguishable from not
  having any).
- **What would reverse this:** Nothing. Pre-registering the exit and deferring
  the target are complementary, not contradictory.

### D007 — Documentation is split: rules, product, detail

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** A 1825-line `AGENTS.md` exceeds what any
  contributor or agent reliably follows.
- **Decision:** `AGENTS.md` holds invariants and workflow; `README.md` holds the
  product; `docs/` holds detail, each file carrying an update trigger.
- **Alternatives rejected:** Single large file (unfollowable); deleting the
  detail (loses hard-won domain knowledge).
- **What would reverse this:** Evidence of drift between the layers — which the
  update triggers and `AGENTS.md` §4 exist to prevent.
- **2026-08-10 — reversal condition triggered once.** An external review found
  six concrete drift defects (stale file count, a self-contradiction inside
  `AGENTS.md`, five dead section references, a missing A7, a wrong deliverable
  count, and a rule contradicting its own exception). Repaired rather than
  reversed; cross-references switched to stable invariant IDs to reduce
  recurrence. **The split stands, but its cost is now measured rather than
  assumed.**

### D008 — Service identity is promoted to an assumption and tested first

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** Identity resolution was documented as an
  implementation concern (`railway-domain.md` §3) rather than an assumption.
  Its failure mode is silent: mis-linked runs produce data that looks entirely
  normal and corrupts every downstream statistic.
- **Decision:** Promote to `README.md` §3 **A7**; run a one-day linkage spike
  before the collector is built; add stop condition **S9** at a 5% linkage
  failure rate.
- **Alternatives rejected:** Leaving it as an implementation detail (failure
  would surface weeks later, after the store is already contaminated);
  discovering it during collector development (same problem, less deliberate).
- **What would reverse this:** A provider with journey identifiers stable enough
  that linkage is trivially reliable — which the spike itself would show.

### D009 — One corridor is measured to completion before a second is considered

- **Date:** 2026-08-10
- **Status:** supersedes the two-corridor position previously stated in
  `README.md` §4
- **Evidence available at the time:** Two contrasting corridors are
  methodologically better, but roughly double the time to a first result. For a
  solo project, elapsed time is the dominant risk — abandonment, not error, is
  now the largest threat to completion.
- **Decision:** Measure one corridor. Add a second only after the first has
  produced a report. The report names the corridor and its alternative density
  and does not generalize beyond it.
- **Alternatives rejected:** Two corridors from the start (methodological
  completeness bought with completion probability); one corridor with silent
  generalization (a false claim).
- **What would reverse this:** The first corridor finishing quickly and easily,
  which makes the second cheap.

### D010 — Definition of Done is split into per-commit and per-deliverable

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** A fourteen-item checklist on every commit,
  for a solo project at limited weekly hours, is not trimmed under pressure — it
  is abandoned wholesale, taking the important items with it.
- **Decision:** Four invariant-level checks per commit; the full checklist at
  deliverable completion (~9 times across Phase 0). Decision-log entries follow
  the same cadence.
- **Alternatives rejected:** One uniform checklist (predictable abandonment);
  dropping items entirely (loses the checks that matter at integration points).
- **What would reverse this:** Evidence that defects are escaping the per-commit
  four — in which case move the specific escaping check up, not the whole list.

### D011 — Advancement to P-minimal requires the author's own willingness to use it

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** Stop conditions covered failure and `TBD`
  targets covered clear success, leaving the most likely outcome — a modest
  improvement with wide uncertainty — with no decision rule at all.
- **Decision:** Proceed to Phase 0.5 only if no stop condition fired **and** the
  author, having read the finished report, would use the system on their own next
  journey. If the answer is *"not yet, but with X fixed"*, X becomes the
  Phase 0.5 scope.
- **Alternatives rejected:** A numeric threshold (would have to be invented now,
  the failure mode D006 exists to prevent); no rule (the result gets argued about
  indefinitely and the project drifts).
- **What would reverse this:** More than one contributor, at which point a single
  person's judgement is no longer an adequate gate.

### D012 — Ticket executability becomes a scenario analysis in Phase 0

- **Date:** 2026-08-10
- **Status:** active; supersedes the *measurement* aspect of **D004**. D004's
  position — the system never adjudicates entitlement — stands unchanged.
- **Evidence available at the time:** External review observed that Phase 0
  enumerates synthetic itineraries and therefore has no passenger, no ticket, and
  no observable binding status — while stop conditions S2 and S3 were defined in
  terms of an "executable opportunity rate". **S2 depended on a number Phase 0
  cannot compute.**
- **Decision:** The headline metric is the **operational opportunity rate**
  (assumes nothing about tickets). Binding is treated as a *filter on the
  admissible candidate set*, computed once under `UNBOUND` and once under
  `BOUND`, and reported as a sensitivity band. S2 keys off the `UNBOUND`
  scenario; S3 becomes the ratio between the two scenarios.
- **Alternatives rejected:** Renaming only and deferring A6 to Phase 0.5 (loses
  the ticket dimension from the stop conditions); sourcing a real fare
  distribution to weight the scenarios (needs data unlikely to be publicly
  available, and is not required to detect a collapse).
- **What would reverse this:** Real users in Phase 0.5 supplying actual binding
  status, at which point the scenario band is replaced by a measured
  distribution.

### D013 — The analysis unit is the disruption episode

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** The stated sample sizes (`n≈450`,
  `n≈1000–2000`) assumed independence, but enumerated itineraries share trains,
  days, disruptions and alternatives. Treating them as independent would produce
  confidence intervals that are too narrow and an overstated effective sample.
- **Decision:** Independence is claimed between disruption episodes, never within
  one. Report enumerated transfers and independent episodes separately;
  confidence intervals by episode-level cluster bootstrap; report effective
  sample size. Enumerated rates are never restated as passenger encounter rates.
- **Alternatives rejected:** Pooling as independent (produces confident wrong
  answers); a mixed-effects model (more machinery than a single-corridor Phase 0
  can justify — revisit if a second corridor is added).
- **What would reverse this:** Nothing likely. The honest consequence — episode
  counts accumulate far more slowly than transfer counts, extending the window
  and pressing on **S8** — is documented rather than avoided.

### D014 — The decision policy is pre-registered and frozen before measurement

- **Date:** 2026-08-10
- **Status:** superseded by **D021** and **D027**
- **Evidence available at the time:** Temporal-validation guidance existed only
  in the ML-leakage section. Deterministic rules have free parameters —
  thresholds, margins, utility weights — and tuning them against the score is
  overfitting whether or not a model is involved.
- **Decision:** Rules, thresholds and utility are recorded here and frozen before
  the measurement window opens. Fallback if tuning proves unavoidable: a
  time-ordered split, development on the first ~30% of collection, final holdout
  scored once on the remainder. Any post-freeze change invalidates that holdout.
- **Alternatives rejected:** Tune-and-score on one dataset (the result would not
  be defensible); cross-validation (inappropriate under temporal dependence).
- **What would reverse this:** Nothing. This is the cheapest possible protection
  and it costs only discipline.

### D015 — A minimal observation contract precedes the collector

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** The project already argues that the
  collector precedes the domain model because elapsed time cannot be recovered.
  The same argument applies one level deeper: a field never collected cannot be
  back-filled either, and schema migration does not help.
- **Decision:** Fix identity fields, four distinct timestamps, poll/gap/heartbeat
  records, provenance and schema version before the first collector run
  (`railway-domain.md` §10). Raw-payload retention stays open until provider
  terms are known.
- **Alternatives rejected:** Starting collection immediately with a loose schema
  (the specific failure this project exists to avoid); a full domain model first
  (contradicts D001's ordering and delays irreplaceable collection).
- **What would reverse this:** Provider terms that forbid retaining several of
  these fields, which narrows the contract rather than removing it.

### D016 — Cheap kill-checks run before documentation repair

- **Date:** 2026-08-10
- **Status:** superseded by **D027**
- **Evidence available at the time:** The external review recommended repairing
  documentation and the measurement protocol first, then running the identity
  spike. Six prior rounds of this project produced documentation improvements and
  zero contact with real data.
- **Decision:** Order is: A5 competitive check (½ d) → provider matrix → identity
  spike (1 d) → documentation and protocol repair → observation contract →
  collector. Documentation consistency gates nothing; the A5 and A7 checks can
  each invalidate large parts of what would be written.
- **Alternatives rejected:** Documentation-first (defensible, and it does produce
  a stable spec before implementation — but it risks another week of writing
  before any observation, which is this project's demonstrated failure mode).
- **What would reverse this:** The measurement protocol and observation contract
  genuinely do gate the collector, and must be finished before it — this decision
  reorders only what precedes the spike, not what precedes collection.

### D017 — A5 becomes a repeated benchmark, not a one-off check

- **Date:** 2026-08-10
- **Status:** active; refines **D005**
- **Evidence available at the time:** A5 was scoped as a half-day existence
  check. A competitive gap can close during a multi-month build, and the
  incumbent app is actively developed.
- **Decision:** Split into **A5a** (gap exists today) and **A5b** (gap is
  defensible long enough). Fixed, versioned scenario set — 10 scenarios initially,
  expanding toward 20–30 — run at three checkpoints: Phase 0 start, Phase 0
  report, Phase 0.5 exit.
- **Alternatives rejected:** Continuous monitoring (unbounded effort for a solo
  project); a single check (cannot detect a closing gap); starting at 20–30
  scenarios (the first run would no longer fit before the collector).
- **What would reverse this:** A5a failing at the first checkpoint, which ends
  the question entirely.

### D018 — Market and distribution get their own validation layer

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** The documentation had a rigorous technical
  and research layer and no equivalent for market, distribution, or commercial
  hypotheses. Phase 0 could pass in full while the product remained unusable for
  reasons never written down.
- **Decision:** Create `market-and-validation.md` holding the ICP hypothesis,
  competitive benchmark, distribution risk, commercial tracks and B1–B4 — held to
  the same standard as the technical documents, every claim with a test and a
  reversal condition. Kept out of `AGENTS.md`, which governs implementation.
- **Alternatives rejected:** Market notes inside `README.md` (would bloat the
  product definition and blur hypothesis with claim); no market layer (the status
  quo, and the gap that prompted this).
- **What would reverse this:** The project settling definitively into
  research-only, at which point the commercial hypotheses become inert but the
  ICP and competitive sections still apply.

### D019 — Distribution is a first-class risk, and the incumbent figure is verified

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** DB Navigator averaged **23.7 million
  monthly active users** in the six months to June 2026 — verified directly
  against `int.bahn.de`'s DSA transparency report on 2026-08-10, not taken from
  the review that cited it. A related claim in that review, that DB has announced
  further 2026 disruption-information improvements, **could not be verified** and
  is recorded as unverified; no conclusion depends on it.
- **Decision:** The product question is restated as *"is the recommendation good
  enough that a passenger opens a second app mid-disruption?"* Phase 0.5 measures
  `time-to-first-useful-decision` including journey entry.
- **Alternatives rejected:** Treating distribution as a launch concern (it can
  invalidate the product while every technical assumption holds); citing the
  unverified claim as support (`AGENTS.md` I2).
- **What would reverse this:** A B2B2C route where the surface already has the
  user, which removes the second-app problem rather than solving it.

### D020 — Actionability enters the domain model

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** Candidates carried *what* and *whether
  permitted*, but not *whether there is still time*. The best alternative is
  useless to a passenger with twenty seconds to reach a door, and filtering that
  in the interface would be patching a domain error.
- **Decision:** Candidates carry `available_from`, `latest_action_time`,
  `decision_margin` and `window_state`. `EXPIRED` candidates are never
  recommended nor counted as available. **A1 is tightened accordingly:** an
  opportunity whose window had closed at decision time is hindsight, not
  opportunity — which will lower the measured rate.
- **Alternatives rejected:** Handling it in the UI (wrong layer, and the decision
  layer would still have preferred an impossible action); ignoring it (overstates
  A1 and would eventually be discovered by a user on a platform).
- **What would reverse this:** Nothing. The correction only ever makes the
  measurement more honest.

### D021 — No utility function until preference evidence exists

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** Outcome estimation lists several
  dimensions. Collapsing them into weighted coefficients now would encode a guess
  about how passengers trade minutes against transfers — the fabricated precision
  `AGENTS.md` **I5** forbids elsewhere.
- **Decision:** Phase 0 uses an explainable ordering applied as successive
  filters: executability → actionability → feasibility → material improvement →
  added cost → uncertainty → intervention bar. The `argmax E[U(Y)]` formulation
  remains the stated direction, explicitly not the Phase 0 policy.
- **Alternatives rejected:** A weighted utility now (looks rigorous, is invented,
  and is not explainable to a passenger step by step).
- **What would reverse this:** Real preference data from Phase 0.5 onward, which
  a utility function could then be fitted to rather than assumed.

### D022 — Missing data is a labelled value, and retain is the default

- **Date:** 2026-08-10
- **Status:** active in principle; combined status vocabulary superseded by
  **D029**; extends **D015**
- **Evidence available at the time:** The observation contract listed required
  fields without missing-value semantics. Providers will not supply journey
  identifiers, observation times or effective times for every observation — so
  the contract as written forced the collector to invent values or discard
  observations. Both are unacceptable, the first under `AGENTS.md` I5/I9, the
  second because discarded data is unrecoverable.
- **Decision:** Every optional slot carries a status — `present`,
  `not_provided`, `unavailable`, `malformed`, `derived`. `not_provided` (a
  property of the feed) stays distinct from `unavailable` (a property of the
  moment). `received_at` is the sole always-required field, because it is the
  only one this system generates. Default behaviour is **retain**; rejection is
  limited to observations that cannot be placed in time at all.
- **Alternatives rejected:** Requiring all fields (forces fabrication or loss);
  nullable-without-status (destroys the feed/moment distinction and with it the
  ability to separate provider limitation from provider outage).
- **What would reverse this:** Nothing. Any provider change narrows which
  statuses occur, not whether they are needed.

### D023 — The BOUND scenario runs off a versioned ruleset

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** S3 is the ratio between the `BOUND` and
  `UNBOUND` scenario rates, but "what a bound ticket would permit" had no data
  structure — no fare class, operator, validity date, relief condition, or
  behaviour under unobservable preconditions. S3 was therefore not reproducible.
- **Decision:** `binding-scenarios.md` holds a versioned ruleset. Every rule
  states its `unknown_behaviour` explicitly. Any change bumps the version and
  invalidates measurements produced under the previous one. Exactly two scenarios
  are computed — more would imply precision the inputs do not support.
- **Alternatives rejected:** Encoding the rules in code only (unversioned in
  practice, and invisible to review); more granular scenarios (false precision).
- **What would reverse this:** A validated legal rules engine, which is
  explicitly out of scope and would replace the sensitivity framing entirely.

### D024 — Episode boundaries are frozen before measurement

- **Date:** 2026-08-10
- **Status:** active; extends **D013**
- **Evidence available at the time:** D013 claimed independence between disruption
  episodes without defining what an episode is. Boundaries left to judgement, and
  drawn after seeing results, would be drawn where they help.
- **Decision:** Freeze the temporal window `W`, the service-relation rule, merge
  and split rules, and the corridor-day boundary before the measurement window
  opens. Also freeze a minimum episode count below which no rate is reported, and
  run a clustering sensitivity analysis over wider and narrower `W`. If the
  conclusion flips under that analysis, it is reported as a conclusion about the
  clustering choice.
- **Alternatives rejected:** Defining episodes during analysis (indistinguishable
  from choosing them); a formal change-point model (more machinery than a
  single-corridor Phase 0 justifies).
- **What would reverse this:** Nothing. The residual risk — national-scale events
  correlating episodes across a whole day — is documented, with corridor-day as
  the fallback unit.

### D025 — The competitive benchmark fixes archetypes, not cases

- **Date:** 2026-08-10
- **Status:** active; refines **D017**
- **Evidence available at the time:** D017 specified a "fixed, versioned scenario
  set", but a live railway disruption cannot be replayed months later — the
  benchmark would have been unrunnable after its first use. S6 was also a binary
  existence test with no threshold for coverage, quality, or friction.
- **Decision:** Fix the archetypes and inclusion criteria; sample fresh
  qualifying cases at each checkpoint, stratified across archetypes. Record
  product version, date, account state, device, OS and locale per run. Pre-define
  "decision-grade counterfactual comparison" as four criteria. S6 becomes
  threshold-based: all four criteria on a majority of cases, or materially rising
  coverage between checkpoints.
- **Alternatives rejected:** Replaying fixed cases (impossible); leaving the
  verdict to the runner's judgement (the finding would depend on who looked).
- **What would reverse this:** Nothing. The cost — comparisons are across samples
  rather than paired — is accepted and reported per archetype.

### D026 — Phase 0.5 is exploratory, then frozen, then confirmatory; live use is author-only

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** B1–B4 thresholds were to be set after the
  first results with no further round — the confirmation bias D006 and D014
  already prevent on the technical side. Separately, "then real journeys" in B2
  conflicted with `README.md`'s single-user description of Phase 0.5.
- **Decision:** Three steps — exploratory pilot (2–3 participants, no verdict),
  freeze thresholds into this log with a date, confirmatory round with
  participants who did not see the pilot. **Live recommendations go to the author
  only**; all other participants evaluate static scenarios. Extending live use is
  a Phase 1 decision.
- **Alternatives rejected:** One round with post-hoc thresholds (unfalsifiable);
  live use by recruited participants (an unvalidated recommendation can cause a
  stranger to miss a train — the only journey the project may risk is its
  author's).
- **What would reverse this:** A validated policy after Phase 0.5, which is
  precisely what would make wider live use defensible.

### D027 — One manifest freezes the Phase 0 population and measurement protocol

- **Date:** 2026-08-10
- **Status:** active; supersedes **D016** and refines **D003**, **D013**, **D024**
- **Evidence available at the time:** Corridor scope, provider rights,
  observation schema, enumeration, binding, episode construction, baseline,
  policy and holdout rules were individually described but had no single version
  record. The eligible denominator was also loose enough for unobserved
  alternatives to be mistaken for negative cases.
- **Decision:** [`phase0-protocol.md`](phase0-protocol.md) is the only Phase 0
  freeze manifest. Detailed documents continue to own their rules; the manifest
  records versions, status and Git commit. The v1 population contains unique,
  equally weighted, exact-one-transfer itineraries inside one frozen corridor.
  A1 reports the evaluable conditional rate plus lower / upper bounds and the
  coverage gap over the full eligible denominator. Collector and measurement
  gates are explicit. Any frozen-field change increments the protocol version;
  after measurement starts it invalidates the prior result.
- **Alternatives rejected:** Duplicating full rules in a manifest (guarantees
  drift); treating unobserved candidates as no opportunity (optimistic false
  precision); letting each analysis record its own informal settings.
- **What would reverse this:** A machine-readable experiment registry that
  enforces the same version and gate semantics without creating a second source
  of truth.

### D028 — A5 uses a complete dual gate and a quantified trajectory threshold

- **Date:** 2026-08-10
- **Status:** active; extends **D025** and **D026**
- **Evidence available at the time:** A majority over all cases could hide the
  exact `REROUTE_EARLY` capability that differentiates the product. "Materially
  rising" had no numeric meaning, and a half-day sample shortfall could be read
  as evidence of absence.
- **Decision:** A5a begins with five fixed archetypes and two fresh cases per
  archetype. It fails only if the same competitor meets all four decision-grade
  criteria on a majority overall and a majority of `REROUTE_EARLY` cases. Fewer
  than ten qualifying cases within half a day is `inconclusive`. A5b fails only
  when overall coverage rises by at least 20 percentage points and
  `REROUTE_EARLY` coverage also rises; raw numerators and denominators are always
  reported. Phase 0.5 uses 2–3 exploratory participants, freezes thresholds,
  then scores 5–8 new confirmatory participants.
- **Alternatives rejected:** Overall-only majority (can pass on incumbent
  features irrelevant to the differentiator); any positive trajectory
  (measurement noise becomes strategy); post-hoc threshold choice.
- **What would reverse this:** A larger pre-registered benchmark with enough
  cases to support a more stable per-archetype statistical gate.

### D029 — Observation availability and provenance are orthogonal

- **Date:** 2026-08-10
- **Status:** active; refines **D015** and supersedes the vocabulary in **D022**
- **Evidence available at the time:** D022 placed `derived` beside missingness
  states, conflating whether a value exists with where it came from. It also left
  malformed-payload retention ambiguous when provider terms prohibit raw storage.
- **Decision:** Every optional observation slot has independent `availability`
  (`present`, `not_provided`, `unavailable`, `malformed`) and `provenance`
  (`reported`, `derived`, `absent`). Derived values name their derivation rule;
  their raw reference is recorded only when provider terms permit retaining that
  value. `received_at` remains the only always-required value. Raw malformed
  payloads are retained only when provider terms permit; otherwise only
  non-reversible diagnostic metadata is kept. Ticket binding separately requires
  carrier / issuer conditions and is not answered by a data-provider matrix.
- **Alternatives rejected:** A combined enum (cannot represent independent
  dimensions); nullable fields (erase why a value is absent); raw retention by
  default regardless of rights (violates I15).
- **What would reverse this:** Nothing likely; a schema may encode the dimensions
  differently, but it must preserve the same distinctions.
