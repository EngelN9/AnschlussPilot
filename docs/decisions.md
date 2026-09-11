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
| Phase 0 scope, order and delivery | D001, D002, D007, D008, D009, D010, D011, D027, D033, D037, D039, D044, D048, D050, **D051**, **D052** |
| Population, episodes and evaluation | D003, D006, D013, D020, D021, D024, D027, D030, D031, D032 |
| Binding and observation evidence | D004, D012, D015, D022 (retention principle only), D023, D029, D034, D043, D044, D049, **D051** (A3/A6 scope) |
| Competitive, market and validation | D005, D017, D018, D019, D025, D026, D028, D035 (risk classification only), D036 (exit definitions only), D038, D041, D042, D046, D047, **D053** |
| Open — awaiting a decision from the author | **D040** (code and collected-data layers only; documentation closed 2026-09-02) |

Superseded entries remain below for provenance: D014 is superseded by D021 and
D027; D016 is superseded by D027; D022's combined status vocabulary is
superseded by D029 while its missing-data and retain-when-permitted principles
remain active; **D005's tag-based pre-report rule is superseded by D037**, while
its two-track sequencing remains active; **the single-proposition forms of A2 and
A3 are superseded by D034**; **D035's immediate route selection and D036's B4
schema-coupling rule are superseded by D042**; **D050's open item is resolved by
D052**, while the rest of D050 remains active.

> **This index is load-bearing.** It is the retrieval path into a document too
> long to read linearly; once it drifts, the log stops being consulted and
> becomes an archive. It drifted once already — D030–D033 were added without
> updating it. Keeping it in sync is a checklist item in `AGENTS.md` §4.

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
- **2026-08-10 — triggered a second time.** Adding `S10` to `README.md` §12 left
  `phase0-protocol.md` §4 still routing `u/N` into `S4` — a live contradiction
  between two documents for the duration of one edit. Repaired in the same
  session. **Two triggers in one day is the signal, not the noise:** every
  cross-document invariant added since D027 raises this cost, and the split is
  still worth it only while the detail documents genuinely differ in audience.

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

### D030 — S2 tests the coverage upper bound; coverage adequacy is its own condition

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** `phase0-protocol.md` §4 defines four
  quantities — `o/n`, `o/N`, `(o+u)/N`, `u/N` — while S2 said only "operational
  opportunity rate < 3%". With a coverage gap of any size the choice of
  denominator decides the verdict, so the stop condition was not reproducible.
  Separately, `u/N` was routed into S4, which measures something else entirely.
- **Decision:** S2 tests the **upper bound `(o+u)/N`** — stop only when even the
  most generous coverage assumption falls short, so missing data can never on its
  own end the project. S3 requires both scenarios on the same denominator and the
  same evaluable set. Coverage adequacy becomes **S10** (`u/N > 20%` ⇒ re-scope,
  not stop; above 5% no single-number reporting).
- **Alternatives rejected:** Testing the lower bound (kills the project for
  missing data, which is a scope problem); testing `o/n` (a rate over a
  subpopulation, silently excluding what was never observed); leaving `u/N` in S4
  (an undersized corridor would masquerade as a dead product).
- **What would reverse this:** Nothing. If coverage turns out near-complete the
  bounds converge and the choice stops mattering — which is the good case.

### D031 — Itinerary evaluability requires full candidate coverage

- **Date:** 2026-08-10
- **Status:** active; refines **D027**
- **Evidence available at the time:** Coverage states attach to candidates while
  `N`/`n`/`u` count itineraries, and the mapping was undefined. The default
  reading — compare the evaluable candidates and record the outcome — converts an
  unobserved candidate into evidence that no better option existed, which both
  `phase0-protocol.md` §2 and the testing catalogue explicitly forbid.
- **Decision:** For **measurement**, an itinerary is `evaluable` only when the
  continue baseline and every enumerated candidate are `evaluable`; otherwise it
  counts in `u` with its reason and yields no verdict. For **runtime**, unchanged:
  rank among evaluable candidates, represent the rest as explicit uncertainty.
- **Alternatives rejected:** Applying the runtime rule to measurement (the defect
  itself); a `partially_evaluable` fourth state (needs a rule for when a missing
  candidate could have dominated, which is exactly what is unknowable).
- **What would reverse this:** Nothing. The cost — `u` grows, sometimes sharply —
  is accepted, and S10 exists to detect when it grows too far.

### D032 — Coverage bounds and sampling intervals are reported nested, never merged

- **Date:** 2026-08-10
- **Status:** active; extends **D013**
- **Evidence available at the time:** A1 acquired two independent uncertainties —
  clustered sampling error and coverage gap — with no rule for reporting them
  together. A tight CI on `o/n` beside a 25% coverage gap presents precision about
  a subpopulation as precision about the question.
- **Decision:** Bootstrap within each bound and report nested:
  `o/N [CI] … (o+u)/N [CI]`, with `u/N` always adjacent. No confidence interval
  may be published without the coverage gap beside it, and the two are never
  combined into a single interval.
- **Alternatives rejected:** One combined interval (leaves neither recoverable);
  reporting only the conditional rate with a CI (the misleading form this rule
  exists to prevent).
- **What would reverse this:** Nothing. Near-complete coverage makes the nesting
  trivial rather than wrong.

### D033 — The spike has its own gate; the manifest holds copies, not originals

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** The A7 spike polls a provider for hours —
  collection under I15 and S1 — but the protocol defined only collector and
  measurement gates. Separately, version values live both in their artefact and in
  the manifest, with no stated precedence.
- **Decision:** A third gate precedes the collector gate: the spike runs only
  under a provider verdict permitting polling at the intended cadence, retains no
  more than that verdict allows, and keeps scratch observations out of the
  measurement store. Being exploratory changes what the output is used for, not
  whose terms apply. The manifest records copies; the artefact named in *Canonical
  detail* owns the value, and on mismatch the artefact wins.
- **Alternatives rejected:** Treating the spike as exempt because it is throwaway
  (terms do not have a throwaway clause); making the manifest authoritative
  (guarantees the manifest drifts from the rules it indexes).
- **What would reverse this:** Nothing.

### D034 — A2 and A3 are split into existence, feed, and access layers

- **Date:** 2026-08-10
- **Status:** active; supersedes the single-proposition form of A2 and A3
- **Evidence available at the time:** Each assumption compressed three claims into
  one. Checking DB's `RIS::Connections` product page settled two and left the
  third open: the hold signal and platform-level transfer times **exist in a
  documented feed**, but access is *"ausschließlich für Vertriebspartner der
  Deutschen Bahn AG"*, priced on request, on contractually agreed terms.
- **Decision:** Split into `A2a/A2b/A2c` and `A3a/A3b/A3c` — exists in the world,
  exists in a feed, permitted to this project. A2a/A2b and A3a/A3b are recorded
  verified. **A2c/A3c become the live assumptions**, tested by a half-day access
  investigation running in parallel with A5a. S4 is bounded to *"at the access
  tier this project can obtain"*.
- **Alternatives rejected:** Leaving one assumption (the three fail differently
  and one test cannot separate them); deferring access into the provider
  evaluation (it can invalidate the product track before any collector exists, so
  it cannot sit behind a 0.5-week deliverable).
- **What would reverse this:** Obtaining access on acceptable terms, which
  collapses A2c/A3c back to the original single form.

### D035 — Access revocability and incumbent-only supply get their own exits

- **Date:** 2026-08-10
- **Status:** active for risk classification; immediate route-selection wording
  superseded by **D042**
- **Evidence available at the time:** A4 asks whether data may be retained. It
  cannot see two adjacent failures: permission being *withdrawable*, and the
  decisive signal being available only from the incumbent this product routes
  around.
- **Decision:** Add **S11** (revocable access, no alternative feed ⇒ moat argument
  fails, B2C conditional) and **S12** (signal obtainable only from the incumbent
  on unacceptable terms ⇒ pivot immediately to B2B2C or research-only, without
  completing Phase 0 first).
- **Alternatives rejected:** Folding both into S6 (a competitive condition, not a
  supply condition); relying on A4 (a different failure).
- **What would reverse this:** A self-service tier exposing hold signals, which
  weakens S11, S12 and A5b at once.

### D036 — The market track gets exits, and B4 moves before the schema freeze

- **Date:** 2026-08-10
- **Status:** active for the SB1–SB4 exit definitions; B4 timing and schema
  coupling superseded by **D042**
- **Evidence available at the time:** B1–B4 had reversals but no exits, so no
  market evidence could stop or redirect the project while the technical track had
  ten such conditions. Separately B4 sat last, after the schema freeze, although
  **D016** already establishes that cheap kill-checks run first.
- **Decision:** Add **SB1–SB4** as market-side exits, reported alongside the
  S-conditions. Move B4 ahead of the schema freeze on the feedback-coupling
  argument: if a buyer wants an arrival-reliability report rather than realtime
  rerouting, that changes what the schema must retain, and the freeze cannot be
  undone.
- **Alternatives rejected:** Reversals only (belief changes that cannot change the
  plan); keeping B4 last (learning after the decision it should inform).
- **What would reverse this:** Nothing. Exits can be re-tuned; their absence was
  the defect.

### D037 — The pre-report gate keys on properties, not on tags

- **Date:** 2026-08-10
- **Status:** active; supersedes the rule form introduced with D005
- **Evidence available at the time:** "Any `P`-tagged work before the report is
  scope creep" keys on a self-assigned label. It over-blocks — a zero-cost buyer
  conversation is forbidden — and under-blocks, since expensive work passes once
  labelled `RP`. For a year-long solo project the real failure is not excess
  product work; it is month twelve with a report and no contact with any user or
  buyer, which the old rule guaranteed.
- **Decision:** Work may proceed before the report if it costs ≤ half a day,
  consumes no irreplaceable collection time, and invalidates no frozen artefact
  version. Anything else requires a recorded exception with a rollback condition.
  Tags remain, describing who the work serves, not what is permitted.
- **Alternatives rejected:** Keeping the tag rule (fails both ways); removing the
  gate (restores the resource competition it was built for).
- **What would reverse this:** Exceptions being recorded routinely rather than
  rarely, which would mean the thresholds are set wrong.

### D038 — A5b is event-driven as well as trajectory-based

- **Date:** 2026-08-10
- **Status:** active; extends D025
- **Evidence available at the time:** The trajectory gate models gradual
  improvement. The real threat is discontinuous — an incumbent holding the data,
  the dispatch relationship and the users makes one product decision and the gap
  closes between checkpoints. A trajectory gate cannot detect a step function
  until after the step.
- **Decision:** Keep the trajectory gate and add trigger events forcing an
  immediate A5a re-run: any tool shipping an explicit continue-vs-change
  comparison; expansion of a declare-your-connection feature; a public
  announcement of decision-grade connection guidance; a hold-signal feed becoming
  self-service.
- **Alternatives rejected:** Continuous monitoring (unbounded effort); trajectory
  only (blind to the actual threat).
- **What would reverse this:** Nothing; watching costs nothing until something
  fires.

### D039 — Corridor scope is sized to half the calendar ceiling

- **Date:** 2026-08-10
- **Status:** active; extends D009
- **Evidence available at the time:** §4 estimates ~8 months at 8 h/week while
  **S8** fires at 2× the calendar estimate. At the planning stage, before anything
  has gone wrong, the design already sits one ordinary delay from its own stop
  condition. D009 applied the sizing argument to the *number* of corridors and
  never to the *size* of the one chosen.
- **Decision:** Before corridor scope `v1` is frozen, record hours actually
  available per week and an acceptable calendar ceiling, then shrink the corridor
  — stations, time-of-day window, service classes — until the estimate fits within
  **half** that ceiling. The other half is the slack S8 protects.
- **Alternatives rejected:** Relying on discipline (a plan with no slack is a
  sizing problem); raising the S8 multiplier (hides the problem).
- **What would reverse this:** No corridor small enough still yielding the episode
  counts in `modelling-and-evaluation.md` §2 — in which case that conflict is
  itself a Phase 0 finding, recorded rather than resolved by schedule optimism.

### D040 — Licensing is three decisions; the current silence has a cost

- **Date:** 2026-08-10
- **Status:** active; **documentation layer chosen 2026-09-02 (CC BY 4.0,
  [`LICENSE-docs`](../LICENSE-docs)); code and collected data still open**
- **Evidence available at the time:** With no `LICENSE`, all rights are reserved
  by default. The omission is therefore a silent decision favouring the product
  track over the research track, because all-rights-reserved documentation is
  impractical for academic or media reuse — and the research output is the
  strongest layer and the only early distribution asset.
- **Decision:** Treat documentation, code and collected data as three separate
  licensing decisions. The first two are the author's free choices; the third is
  constrained by provider terms (A4, I15) and cannot be opened by choosing to.
  Record the current state as undecided **with its cost stated**, not left
  implicit.
- **Alternatives rejected:** One deferred decision (hides that two layers are free
  and one is not); selecting licences here (the values are the author's).
- **What would reverse this:** The author choosing values, which closes this entry
  rather than reversing it.
- **Update 2026-09-02 — documentation layer closed.** The author chose
  **CC BY 4.0** for `README.md`, `AGENTS.md`, `ROADMAP.md`, `docs/` and the
  Phase 0 report; `LICENSE-docs` records the scope and the exclusions. CC BY was
  chosen over CC BY-SA because this layer's purpose is citability, and
  share-alike deters the academic and media reuse D041 depends on. The code
  layer remains undecided and all-rights-reserved; the collected-data layer
  remains unavailable to license under **I15**. **Watch item:** candidate
  sources exist under CC BY-**SA** 4.0 (`provider-evaluation.md` §2.4); that
  does not affect this licence, but a future output embedding such data may
  inherit share-alike. Decide that when a source is selected, not before.

### D041 — Publish the rights matrix early; one crude ceiling check

- **Date:** 2026-08-10
- **Status:** active
- **Evidence available at the time:** The only distribution asset appears at the
  end, when it is least useful — B2B2C conversations happen months earlier. A solo
  project producing no external output for a year also has no external error
  correction: pre-registration guards against self-deception during measurement,
  not against a wrong premise.
- **Decision:** Publish the rights-and-access matrix around month two, subject to
  I15 — findings about terms, never redistributed provider data. Separately run
  **one** order-of-magnitude funnel, rounded to powers of ten, every input flagged
  `requires verification`, usable only to stop and never to justify continuing.
  The rule against market sizing otherwise stands.
- **Alternatives rejected:** Publishing nothing until the report (no credibility
  during B4, no error correction); a real market model (the anchor problem D006
  prevents).
- **What would reverse this:** Provider terms forbidding publication of the
  findings themselves — an unusual restriction, and itself worth recording.

### D042 — Early market evidence cannot select or redesign the product route

- **Date:** 2026-08-11
- **Status:** active; refines **D035**, **D036**, and **D037**
- **Evidence available at the time:** The project identified second-app friction
  as a top-three risk, but D036 allowed a half-day exploratory buyer conversation
  to alter an observation schema whose purpose is to test decision quality. D035
  also selected B2B2C before the evidence needed to show that the decision policy
  works or that passengers reject a second app.
- **Decision:** An early B4 round may spend at most half a day collecting
  exploratory evidence. It cannot change the Phase 0 schema, select B2C or
  B2B2C, fire SB4, or authorize product implementation. Phase 0 establishes
  decision quality. Phase 0.5 supplies second-app friction and the formal B4
  evidence; only then is the commercial route selected. Provider access may mark
  a route conditional or non-viable earlier, but does not authorize building the
  alternative route.
- **Alternatives rejected:** Deferring all market contact (needlessly preserves a
  cheap uncertainty); letting one exploratory conversation redesign Phase 0
  (couples technical validation to weak evidence); selecting B2B2C solely because
  B2C data access is difficult (does not prove buyer demand).
- **What would reverse this:** A binding commercial commitment with explicit data
  access and a concrete integration requirement. That would be new evidence, not
  a speculative conversation.

### D043 — Public provider evidence is insufficient to open the rights gate

- **Date:** 2026-08-11
- **Status:** active
- **Evidence available at the time:** A primary-source pass covered
  RIS::Connections, RIS::Journeys, Timetables and RIS::Stations. DB documents
  hold disposition, platform-precise transfers and indoor-routing inputs in
  RIS::Connections, but restricts access to approved DB sales partners under
  individually agreed terms. RIS::Journeys is likewise approval- and
  contract-gated. Timetables is CC BY 4.0 but does not document the decisive
  hold/transfer signals. RIS::Stations exposes relevant master data, but the
  public materials do not prove that the CC BY scope covers every transfer and
  topology field needed by Phase 0.
- **Decision:** Record provider verdict `v1-partial` as `BLOCKED`; select no
  provider and keep A2c, A3c and A4 `UNKNOWN`. Do not poll, run A7, retain
  payloads or build the collector. A missing public answer is not a negative
  contractual finding, so S1, S11 and S12 are not fired. Changing the verdict
  requires written eligibility and applicable contract terms; seeking them is a
  separate externally consequential action requiring explicit authorisation.
- **Alternatives rejected:** Treating documented capability as permission
  (violates I15); treating CC BY Timetables as a substitute for decisive signals
  (answers a different question); declaring the product dead from absent public
  contract terms (confuses `UNKNOWN` with refusal).
- **What would reverse this:** Written provider evidence establishing eligible
  access plus storage, retention, research, redistribution, commercial,
  attribution and termination terms adequate for the frozen Phase 0 use.
- **2026-08-18 — external-response update.** Two of the three provider
  enquiries now have written replies. The DB RIS response closes the individual
  zero-budget RIS path: RIS::Connections is sales-partner-only and
  RIS::Stations is paid. The DB data-stream response says DB GTFS data cannot
  currently be supplied as Open Data and that paid access would require internal
  review, but it does not expressly answer RiFahrt or any storage, retention,
  research, redistribution, training, commercial, attribution or termination
  field. DELFI-Realtime remains unanswered. The evidence therefore narrows two
  zero-budget DB paths without opening the rights gate: A2c, A3c and A4 remain
  `UNKNOWN`; no provider is selected; S1, S11 and S12 do not fire while the
  remaining paths are unresolved. The decision stands.
- **2026-08-24 — DELFI requested project context.** DELFI responded before the
  planned silence deadline, but with a request for more project context
  (research subject, data use, intended results) rather than an answer to the
  six original questions; a phone call was offered as an alternative to a
  written answer. This is neither a rights answer nor silence — none of A2c,
  A3c or A4 changes, the rights gate stays `BLOCKED`, and S1/S11/S12 still do
  not fire. Written-only project context was sent in the same thread on
  2026-08-26 and awaits a substantive answer (`provider-evaluation.md` §5.3);
  the decision stands.

### D044 — Phase 0 uses an individual, zero-spend provider path

- **Date:** 2026-08-11
- **Status:** active; operationalises the next evidence action in **D043**
- **Evidence available at the time:** The author is acting as an individual and
  has no Phase 0 funding. Public evidence v2 identifies free planning sources and
  some free tests, but none currently establishes both the decisive realtime
  signals and the retention rights needed for the longitudinal experiment.
  A5a can run without provider data. Registration itself accepts general terms
  but does not answer product eligibility or downstream rights.
- **Decision:** Spend nothing on provider access before Phase 0 evidence
  justifies reconsideration. Do not provide payment details, form a company,
  register for a platform, accept terms or request credentials before written
  eligibility and rights answers are reviewed. Send separate enquiries as an
  individual to DB RIS / Marketplace, DB data streams and DELFI. A free trial is
  usable only if it has no automatic paid conversion and expressly grants the
  storage and retention needed for the bounded research period. Continue A5a in
  parallel, one auditable mobile case at a time.
- **Alternatives rejected:** Paying before the product thesis is tested
  (converts uncertainty into sunk cost); registering first (accepts terms without
  resolving the rights gate); treating a free schedule feed as a decisive-signal
  provider (answers a different question); declaring that the project must
  remain zero-budget forever (the current constraint is Phase 0, not a permanent
  business model).
- **What would reverse this:** Phase 0 evidence supports continuing and external
  funding becomes available, or a provider supplies written no-cost eligibility
  and sufficient rights. Either event triggers a fresh provider and budget
  decision; neither automatically authorises collection.

### D045 — The first A5a checkpoint pairs three mobile surfaces

- **Date:** 2026-08-12
- **Status:** superseded by **D046**
- **Evidence available at the time:** The unmerged `codex/a5a-20260811` branch
  contains two partial bahn.de cases and two failed Trainline web attempts. No
  screenshot was retained, every evidence path is `NOT_CAPTURED`, DB Navigator
  was not observed, and every complete denominator is `0/10`. That work is
  useful provenance but cannot support a product-level or A5a verdict. Mixing a
  native incumbent app with incomplete web surfaces would preserve the same
  ambiguity.
- **Decision:** The Phase 0-start checkpoint uses the same ten fresh cases on
  DB Navigator, Trainline and Google Maps mobile apps. Each surface is anonymous,
  guest or incognito; no account is created and no web substitute is allowed.
  A case starts at the disrupted journey detail and allows at most three
  purposeful navigation taps and two minutes. The effort cap is four active
  hours; pauses awaiting a mobile observation do not consume it, but an expired
  action window invalidates that case. Across later checkpoints the cases remain
  fresh and therefore unpaired, as D025 requires.
- **Alternatives rejected:** Importing the prior unauditable rows (would turn
  missing evidence into a negative observation); mixed mobile/web surfaces
  (surface differences could masquerade as product differences); unpaired tools
  within one checkpoint (context differences would dominate ten cases); unlimited
  exploration (makes friction and reachability runner-dependent).
- **What would reverse this:** Before a future checkpoint begins, evidence that
  one frozen app is unavailable to the target traveller or that another tool is
  materially more relevant may change the surface set. The replacement and new
  interaction budget must be frozen before sampling and cannot be applied
  retrospectively to this checkpoint.

### D046 — Phase 0A isolates the `REROUTE_EARLY` kill-check

- **Date:** 2026-08-12
- **Status:** active; supersedes **D045** for future sampling and refines
  **D025**, **D028**, **D042**, and **D044**
- **Evidence available at the time:** D045 inspected two candidates, included
  one `REROUTE_EARLY` case and completed one of thirty planned app observations.
  The case's action window expired before Trainline and Google Maps were
  observed, so D045 closed `inconclusive`; its evidence cannot be rescued by a
  later device-timezone change. The product thesis is narrower than five
  disruption archetypes: it depends on useful action before a threatened
  transfer, compared with existing warnings, manual alternative search and
  connection protection. The official DB Regio Bayern FAQ documents
  Anschlussvormeldung in MoBY and Wohin·Du·Willst, including route-position
  validation and late processing, but does not by itself show a simultaneous
  continue-versus-early-reroute destination comparison. A product-support
  enquiry was sent on 2026-08-12 and is awaiting response.
- **Decision:** Run a bounded Phase 0A on `REROUTE_EARLY` only, with DB Navigator,
  MoBY and Wohin·Du·Willst in anonymous or guest mode. Target three unique
  `Fernverkehr → Nahverkehr` Bavarian cases and extend to at most five within
  eight active hours. Each case must have a live disruption, an actionable
  pre-transfer divergence and all three anonymous surfaces. Score the existing
  four decision-grade criteria within three taps and two minutes, and additionally
  record projected arrival gain at `t_early`, option decay by `t_late` and
  decision lead time versus the baseline ladder. Anschlussvormeldung is the
  connection-protection baseline embedded in the latter two apps, not a fourth
  surface. Do not simulate German location or submit a connection request; its
  live lane remains `UNKNOWN` without a genuine in-route observer. The same app
  reaching `2/3` complete cases triggers S6 immediately; if not, extend to five,
  where `3/5` triggers S6. A continue recommendation requires five cases,
  fifteen app observations, complete temporal operands, the frozen opportunity
  thresholds and sufficient acquisition rights. Any incomplete denominator or
  rights answer leaves the overall result `inconclusive / BLOCKED`.
- **Alternatives rejected:** Completing D045 after its only case expired (would
  violate decision-time validity); using Trainline or Google Maps in the new
  denominator (tests broad journey planning rather than the strongest Bavarian
  connection-protection baseline); treating Anschlussvormeldung as a fourth app
  (it is an embedded workflow); simulating location or sending a request (would
  falsify the observation context and create an operational side effect);
  proceeding directly to a collector (rights remain blocked).
- **What would reverse this:** Before the first Phase 0A candidate is inspected,
  current primary evidence may show that one frozen surface cannot be viewed by
  the target traveller or that the official workflow has materially changed.
  After sampling starts, method changes require a new version and denominator.
  A verified competitor stop, a failed early-reroute opportunity gate, or
  insufficient rights stops or repivots the project under the frozen rules.
- **2026-08-13 — BEG response received.** BEG (MoBY's operator) answered the
  2026-08-12 product-support enquiry in writing: connection eligibility has no
  public list and is derived from timetable data; only Nahverkehr and
  Fernverkehr → Nahverkehr transfers within Bavaria qualify; pre-notification
  triggers a server-side geoposition plausibility check; and **BEG confirmed
  MoBY does not simultaneously show the continue-versus-early-reroute
  destination comparison before the wait decision.** BEG could not answer for
  Wohin·Du·Willst (operated by DB Regio Bus Bayern, not BEG) and granted
  screenshot-publication permission with attribution "MoBY/BEG". This is an
  operator-side negative for MoBY, not a completed case — D046's frozen
  all-three-surfaces rule still requires a live observation of all three
  surfaces, and Wohin·Du·Willst remains `surface_unavailable`. The `0/5` /
  `0/15` denominators and `inconclusive / BLOCKED` verdict are unchanged. Full
  evidence recorded in
  [`benchmarks/phase0a-reroute-early-start-2026-08-12.md`](benchmarks/phase0a-reroute-early-start-2026-08-12.md)
  §2 and [`market-and-validation.md`](market-and-validation.md) §2.
- **2026-08-24 — v2 pre-registered.** A physical-device setup preflight reached
  the anonymous journey-search entry in DB Navigator, MoBY and
  Wohin·Du·Willst. DB Navigator had changed from `26.14.0` to `26.15.0`, so the
  closed v1 run is not reopened. The separate v2 artifact freezes the current
  three app versions, the existing D046 sample and gate rules, the station
  discovery order, and the app observation order before any candidate is
  inspected. Setup availability is not scored; v2 starts at zero and imports
  none of v1's `0/5` / `0/15` denominator. See
  [`benchmarks/phase0a-reroute-early-start-2026-08-24-v2.md`](benchmarks/phase0a-reroute-early-start-2026-08-24-v2.md).

### D047 — Synthetic users are an optional Phase 0.5 preflight, never gate evidence

- **Date:** 2026-08-13
- **Status:** active
- **Evidence available at the time:** The repository remains documentation-only;
  Phase 0 has not passed, D046 is `inconclusive / BLOCKED`, provider rights are
  incomplete, and no recommendation output or user interface exists. MatrAIx is
  a newly public simulated-user evaluation framework whose own repository calls
  it useful for exploration, stress testing and hypothesis generation rather
  than a replacement for evidence from real people. Its runtime, persona source,
  model-provider terms and cost have not been approved for this project.
- **Decision:** Document, but do not implement, an optional **Phase 0.5-S**. It
  becomes eligible only after a passing Phase 0 report and separate author
  approval of the pinned MatrAIx commit, applicable licences and terms, provider,
  budget and data handling. The future flow is one-way: a human-authored frozen
  scenario enters an isolated MatrAIx task, a deterministic scorer produces
  structured UX-risk findings, and a human reviews them. Synthetic outputs may
  reject a wording candidate or generate hypotheses, but cannot satisfy A1–A8,
  S1–S13, B1–B4 or SB1–SB4 and never enter a production decision or confidence
  score. Start with a 48-cell calibration; only after human review may a
  400-cell paired-variant pilot run. Real Phase 0.5 participants remain mandatory.
- **Alternatives rejected:** Implementing the harness before Phase 0 (product
  work without a validated decision); importing Persona 1M before a separate
  rights and provenance review; treating `ATTENTION` or `UNKNOWN` as actions;
  inventing low/medium/high recommendation confidence; using synthetic response
  rates as passenger population estimates or promotion evidence.
- **What would reverse this:** If human calibration finds no meaningful signal,
  or licensing, model-provider terms, privacy, cost or maintenance are
  unacceptable, remove Phase 0.5-S without changing the Phase 0 or real Phase 0.5
  protocols. Any proposal to give synthetic evidence stronger status requires a
  new explicit decision supported by independent human validation; no existing
  synthetic result may be reclassified retroactively.

### D048 — Execution order is extracted from README into ROADMAP.md

- **Date:** 2026-08-15
- **Status:** active
- **Evidence available at the time:** `README.md` had grown to mix product
  thesis and specification with project-management content — a deliverables
  table with effort estimates (§4), an engineering-order pipeline (§11), and a
  deferred release sequence (§14). A drafted `ROADMAP.md`, already written in
  this project's own vocabulary (A2c/A3c/A4/A7, S1–S12, B1–B4/SB1–SB4), was
  available to hold that content instead.
- **Decision:** Add `ROADMAP.md` at repo root as a fourth documentation layer,
  extending D007's split (`AGENTS.md` = rules, `README.md` = product,
  `docs/` = detail) with: `ROADMAP.md` = execution order, milestone gates, and
  current phase state. README's deliverables table, engineering-order diagram
  and release-sequence diagram are replaced with pointers into `ROADMAP.md`
  §3/§13/§5.2–5.3. README keeps the Effort and Sizing-gate numbers, because
  stop conditions **S7** and **S8** (`README.md` §12) are defined directly
  against those estimates, and `ROADMAP.md` deliberately states no time
  commitments by design.
- **Alternatives rejected:** Leaving the scheduling content duplicated in both
  files (guarantees drift); moving the Effort/Sizing-gate numbers into
  `ROADMAP.md` too (would strand S7/S8's referent outside the document that
  defines them).
- **What would reverse this:** Evidence that `ROADMAP.md` and README's
  remaining phase/gate definitions have drifted out of sync — the same
  failure mode D007 already tracks for the three-way split.
### D049 — DELFI-Realtime is tested against A2b before it is waited on further

- **Date:** 2026-09-02
- **Status:** active; narrows the scope of the pending DELFI enquiry recorded in
  **D043** and `provider-evaluation.md` §5.4
- **Evidence available at the time:** `README.md` §3 grounds **A2b** in a
  connection-hold flag — `RIS::Connections` states whether connections *warten*
  or *nicht warten* — and that product is restricted to DB sales partners.
  Public sources describe DELFI-Realtime as GTFS Realtime Trip Updates and
  SIRI Estimated Timetable. Both formats carry scheduled times, forecast times,
  delays and cancellations; neither is documented as carrying a
  connection-hold flag. The DELFI thread has therefore been blocking every
  data-dependent Phase 0 task on a source that may be structurally unable to
  supply the assumption it is being waited on for. Separately, `gtfs.de`
  publishes a free long-distance schedule feed and an aggregated GTFS-RT stream
  under published Creative Commons licences, with no counterparty and no
  registration (`provider-evaluation.md` §2.4).
- **Decision:** Stop treating a substantive DELFI answer as the single gate for
  all data-dependent work. Two things proceed in parallel with the pending
  reply, and neither requires provider permission: (a) determine, on paper,
  whether the Phase 0 decisive signal can be defined on **expected arrival at
  the final destination at the decision time** — which the product thesis
  already states — using forecast and cancellation data, with the hold flag as
  one input that degrades to `UNKNOWN` when absent rather than as a
  precondition; and (b) read the `gtfs.de` licence texts and coverage in full
  to establish whether a published-licence path can support a bounded **A7**
  identity spike. Neither activity fetches, polls or stores provider data, and
  neither changes A2c, A3c or A4. The DELFI thread continues on its existing
  schedule, unchanged and unhurried.
- **Alternatives rejected:** Continuing to wait on DELFI as the sole gate
  (single-threads the project on one volunteer association's mailbox and may be
  waiting for something the source cannot supply); declaring A2b dead on the
  basis of format documentation alone (public format descriptions are not a
  provider statement — the question is asked, not assumed); treating `gtfs.de`
  as a decisive-signal provider (it carries no hold flag and is an aggregator,
  which raises **S11** rather than resolving it); starting collection under a
  Creative Commons licence before the licence text has actually been read
  (**I15**).
- **What would reverse this:** DELFI confirming in writing that DELFI-Realtime
  does expose a connection-hold or equivalent wait decision, which would restore
  it as a direct A2b candidate; or the paper analysis showing that a
  forecast-only decisive signal cannot separate `CONTINUE` from `REROUTE_EARLY`
  at a useful lead time, which would make the hold flag genuinely mandatory and
  push the project toward **S12**.
- **2026-09-03 — activity (a) executed.**
  [`decisive-signal-analysis.md`](decisive-signal-analysis.md) reports a
  conditional pass: a forecast-only estimator separates `CONTINUE` from
  `REROUTE_EARLY` outside a bounded hold band, so **the reversal condition above
  did not trigger and S12 does not fire.** The dependency moved rather than
  disappearing — it now rests on forecast informativeness, opened as **A8** with
  stop condition **S13**. Activity (b), the `gtfs.de` licence read, remains
  outstanding and still gates the A7 + A8 spike.
### D050 — The sizing gate is set from measured effort, and the conflict it exposes is a Phase 0 finding

- **Date:** 2026-09-02
- **Status:** active; fills the sizing gate in `README.md` §4 and triages the
  2026-09-02 external expert assessment. **Contains one open item for the
  author.**
- **Evidence available at the time:** `README.md` §4's sizing gate required
  `hours actually available per week` and `acceptable calendar ceiling`, both
  `UNSET`, before corridor scope `v1` could be frozen. An external assessment
  assumed 8 h/week without evidence. The repository's only instrumented effort
  is the Phase 0A active-effort ledgers — 34 min on 2026-08-12 and 14 min on
  2026-08-24, i.e. 48 min across 3.4 weeks (~14 min/week of scored work), with
  activity on 8 of 24 days in two bursts separated by a 9-day gap. Documentation
  and provider correspondence are real but uninstrumented.
- **Decision:** Set `hours actually available per week = 3` on that measured
  basis and `acceptable calendar ceiling = 12 months` as a stated preference;
  S8 therefore fires at 24 months. Record the resulting arithmetic as a Phase 0
  finding rather than resolving it with optimism: ~277 h of work at 3 h/week is
  ~21 months, the ceiling rule allows 78 h inside half of 12 months, and the gap
  is **3.6×**. A corridor shrink cannot close it, because much of the 277 h —
  the A3 evaluation, A6 binding rules, protocol freeze, replay harness,
  deterministic baseline and report — is fixed cost independent of corridor
  size. Separately record that Phase 0 has a **second, independent** constraint:
  episodes accrue on wall-clock time (`modelling-and-evaluation.md` §2), so the
  measurement window has a floor that neither more hours nor a smaller corridor
  can shorten.
- **Open item for the author — RESOLVED 2026-09-03 by D052.** As recorded when
  this entry was written, the choice was between raising the
  available hours; accepting a substantially longer ceiling; formally reducing
  Phase 0's statistical ambition (fewer episodes, wider intervals, weaker
  claims, stated as such in the report); or converting Phase 0 into the
  rights-and-method study it already substantially is. Until this is chosen,
  corridor scope `v1` stays unfrozen.
  **D052 chose a hybrid of the last two** — a directional Phase 0-lite with a
  conditional Phase 0-full, plus standalone publication of the rights study
  (D053) — after **D051** removed two of the fixed costs the 277 h rested on and
  [`decisive-signal-analysis.md`](decisive-signal-analysis.md) removed the
  hold-flag dependency. Corridor scope `v1` may now be frozen under the Phase
  0-lite scope.
- **Assessment triage — adopted:** run **A7 first** (matches `ROADMAP.md` P0-B;
  its failure mode is invisible and poisons every downstream statistic); write
  the Phase 0 report template before data exists (cheap, and it makes the stop
  conditions real rather than post-hoc, consistent with **D041**); choose the
  documentation licence now (**D040**).
- **Assessment triage — adapted:** the assessment's A7 method — *"use any
  available feed (even a degraded one) to poll one corridor for 6 hours"* —
  violates **I15** and **D044** as written. The conclusion survives via the
  lawful route already opened by **D049**: read the `gtfs.de` licence and terms
  in full (summary pages are not terms), and only if fetch and temporary
  retention are permitted, run the bounded spike and score it against the
  existing **S9** threshold of 5 %. Do not invent a new threshold. The
  assessment's four-week deadline on A2c is adopted in spirit but must not
  override the recorded DELFI schedule (reminder 2026-09-07; inconclusive no
  earlier than 2026-09-17).
- **Assessment triage — rejected, with reasons:** *"treat B2B2C as the default
  path"* — **D042** already rejects this exact reasoning by name, and an
  argument is not the binding commercial commitment that would reverse it;
  *"Phase 0 becomes a synthetic-data demonstration"* — **D047** forbids
  synthetic evidence from gating anything, and this would make it the basis of
  the result; the parallel **G1–G5** gate framework — a lossy restatement of
  S1–S12 (G1≈S9, G3≈S6 dropping "same app", the four criteria and the 3/5
  extension, G4≈S8, G5≈S2 dropping that S2 is tested against the *upper* bound
  under `UNBOUND`), and two competing gate vocabularies are worse than one;
  *"verified empty space"* in the competitive gap — A5a/D046 stands at **0/5**
  complete cases and **0/15** scored observations, so calling it verified would
  breach **I2**. The defensible statement is that no competitor has yet been
  observed closing the gap, on zero completed cases.
- **Alternatives rejected:** Adopting 8 h/week because an external reviewer
  assumed it (no evidence supports it; it is ~34× the instrumented rate);
  leaving the gate `UNSET` to avoid recording an uncomfortable number (the gate
  exists precisely to force this); resolving the 3.6× gap by shrinking the
  corridor (arithmetically cannot work, and would disguise a finding as a scope
  tweak); treating the conflict as a stop (it is a finding that forces a choice,
  and **S8** governs stopping).
- **What would reverse this:** A sustained measured rate materially above
  3 h/week, recorded the same way, which would revise the gate and may close the
  gap; or a Phase 0 redesign under the open item above, which would change the
  277 h estimate the arithmetic rests on. Either is a new entry, not a silent
  edit of this one.

### D051 — Phase 0's A3 and A6 prerequisites are reduced to what the measurement actually needs

- **Date:** 2026-09-03
- **Status:** active; reduces the effort estimate D050's arithmetic rests on
- **Evidence available at the time:** D050 recorded a **3.6×** sizing gap and
  listed the A3 transfer-source evaluation and the A6 binding ruleset among the
  fixed costs a corridor shrink cannot remove. Both are fixed costs. Neither is
  a Phase 0 requirement. **A3:** the Phase 0 question is whether `B_eff` is
  positive by a *material* margin — a threshold question, not a precision
  question — and **S5** already exists to catch the case where `T_transfer`
  cannot be bounded within ±5 min. Free GTFS `transfers.txt` minimum transfer
  times and the published *Mindestumsteigezeit* are candidate sources for that
  bound. **A6:** D004 and D012 already established that binding is a user input
  reported as a two-scenario `BOUND` / `UNBOUND` sensitivity band; if both
  scenarios are reported, the carrier-conditions ruleset is not needed to produce
  the Phase 0 result. The effort estimate was never revised to reflect either.
- **Decision:** In Phase 0, A3 is answered by running **S5 as a desk check**
  against free GTFS transfer data plus published minimum transfer times, not by a
  provider evaluation. Platform-pair topology (`RIS::Stations`) becomes a Phase 1
  product requirement, not a Phase 0 blocker. A6's binding ruleset moves to
  **Phase 0.5**, where the recommendation is actually shown to someone;
  [`binding-scenarios.md`](binding-scenarios.md) remaining `UNSET` is acceptable
  for Phase 0 provided the report states both scenarios and **S3** is computed
  from them. The general principle: the project's `R`/`P` discipline was applied
  to deliverables but never to the effort estimate, and the 277 h contains
  product-grade work classified as research prerequisites.
- **Alternatives rejected:** Keeping A3 as a provider evaluation (it makes a
  threshold question depend on a negotiation that has already failed three
  times); dropping `T_transfer` uncertainty entirely (S5 exists precisely because
  the bound can fail, and an unbounded transfer time invalidates `B_eff`);
  writing the A6 ruleset now (it cannot be validated until a recommendation is
  shown to a person, and an unvalidated ruleset would silently shape S3);
  reporting only the `UNBOUND` scenario (S3 becomes uncomputable and the headline
  becomes an upper bound with no floor).
- **What would reverse this:** S5 firing — free transfer data failing to bound
  `T_transfer` within ±5 min at the candidate stations — which restores A3 as a
  genuine Phase 0 prerequisite and forces the narrowing S5 already prescribes; or
  a Phase 0 result whose `BOUND` / `UNBOUND` spread is so wide that the band is
  uninformative, which would make the A6 ruleset load-bearing earlier than
  Phase 0.5.

### D052 — Phase 0 splits into a directional Phase 0-lite and a conditional Phase 0-full

- **Date:** 2026-09-03
- **Status:** active; **resolves the open item in D050**
- **Evidence available at the time:** D050 set the sizing gate at 3 h/week
  measured and a 12-month stated ceiling, computed a **3.6×** gap between ~277 h
  of work and the 78 h available inside half the ceiling, established that a
  corridor shrink cannot close it, and left four options open: raise the hours,
  accept a longer ceiling, reduce Phase 0's statistical ambition, or convert
  Phase 0 into the rights-and-method study it already substantially is. Since
  then, D051 removed two fixed costs, and
  [`decisive-signal-analysis.md`](decisive-signal-analysis.md) established that
  the measurement does not require the partner-gated hold flag. Separately,
  `ROADMAP.md` §2 records that episodes accrue on wall-clock time, so **collection
  time is the only irreplaceable input in the project — and it has been spent at
  zero yield for the whole of Phase 0 so far.**
- **Decision:** Split Phase 0 rather than choosing one of D050's four options
  alone. **Phase 0-lite** is the confirmatory design reduced to one transfer
  station, one daily time-of-day window of about four hours, and 30–60
  consecutive operating days, on a published-licence feed, using the
  forecast-only decisive signal and D051's reduced prerequisites. Its results are
  **directional and explicitly not confirmatory**: `(o+u)/N` is reported with
  wide intervals, a stated non-extrapolation clause, and the coverage nesting
  D030–D032 already require. **Phase 0-full** is the currently specified
  confirmatory study, and it becomes conditional on the lite result justifying
  more hours or an institutional partner. The **rights-and-access matrix
  publishes as a standalone artefact independently of both** (D053). The 3.6×
  gap is therefore closed by **shrinking the claim, not the corridor** — which is
  the one move D050's arithmetic does not forbid.
- **Consequences that must be stated, not buried:** **S2** and **S10** become
  *directional* gates for the lite study. A lite result that clears S2 does not
  establish the opportunity; it justifies funding the confirmatory study. A lite
  result that fires S2 is **not** a stop on its own — the interval will be wide
  enough that it may not exclude the threshold — and the report must say so
  rather than reporting a failed checkbox. Every Phase 0-lite output carries the
  label `DIRECTIONAL — NOT CONFIRMATORY`, in the same spirit as D047's synthetic
  banner. **S8** continues to govern stopping, on the original 24-month clock.
- **Alternatives rejected:** Raising the available hours (not supported by any
  evidence; D050 already rejected assuming 8 h/week, and assuming it of oneself
  is the same error); accepting a longer ceiling alone (does not address the
  wall-clock episode floor, and extends the abandonment risk ROADMAP §12 names as
  dominant); converting Phase 0 entirely into a rights-and-method study
  (abandons the only work that can answer A1, which is the question the project
  exists to answer); reducing statistical ambition *without* splitting (loses the
  confirmatory design permanently rather than deferring it); shrinking the
  corridor further (D050 already showed this cannot work).
- **What would reverse this:** A Phase 0-lite result whose intervals are so wide
  that it distinguishes nothing — in which case the split bought nothing and
  D050's remaining options return; a sustained measured effort rate materially
  above 3 h/week, recorded the same way, which would make Phase 0-full directly
  affordable; or an institutional partner, which would change both the hours and
  the provider access at once.

### D053 — The rights-and-access matrix publishes as a standalone artefact and becomes the provider follow-up mechanism

- **Date:** 2026-09-03
- **Status:** active; executes the publication already scheduled in
  [`market-and-validation.md`](market-and-validation.md) §7
- **Evidence available at the time:** Three zero-budget enquiries were sent
  2026-08-11. All three received a reply; none opened the rights gate; DELFI
  answered 0 of 6 questions and offered a phone call.
  `provider-evaluation.md` §5.4 already diagnosed why: *"a pointer to published
  terms costs minutes; a bespoke rights opinion costs hours, and that asymmetry
  is the most likely reason the thread has stalled."* The diagnosis is right and
  the remedy has not followed from it. Every enquiry so far has requested
  permission while offering nothing, from an individual with no institution.
  Separately, `market-and-validation.md` §7 already schedules matrix publication
  for *"around month two"* — reached 2026-09-11 — on the grounds that *"the
  Phase 0 report is this project's only distribution asset, and it does not exist
  until the end, which is exactly when it is least useful."*
- **Decision:** Publish the rights-and-access matrix as a standalone artefact
  under the CC BY 4.0 documentation licence (D040), and make it the mechanism for
  the outstanding provider threads. Follow-ups become *"this is published, here is
  your organisation's entry, is it accurate?"* rather than *"please answer my six
  questions."* Correcting a public error about one's own organisation is a
  minutes-long task with a reputational incentive; producing a bespoke rights
  opinion is an hours-long task with none. This changes the response function the
  §5.4 diagnosis identified, rather than restating it. The matrix publishes with
  every `UNKNOWN` intact and every organisation's reply represented as recorded —
  redacted per §6.1–6.3, no raw correspondence, no identities.
- **Constraints:** This does **not** alter the recorded DELFI schedule (reminder
  2026-09-07; `inconclusive` no earlier than 2026-09-17) and does not substitute
  for it. It asserts nothing an organisation has not stated: an unanswered field
  publishes as `UNKNOWN`, never as a characterisation of the organisation
  (**I2**, **I4**). It creates no obligation on any provider and claims no
  endorsement.
- **Alternatives rejected:** Waiting for the Phase 0 report (§7's stated failure
  mode — the only distribution asset arrives when it is least useful, and D052
  has just made the report later still); continuing to send permission requests
  unchanged (three replies, zero gate movement — the evidence is in); accepting
  the offered phone call (the written-only choice stands: a spoken rights answer
  is not a citable one); publishing the raw correspondence (violates §6.1–6.3 and
  would end the threads).
- **What would reverse this:** A provider stating that publication of its
  recorded position is unwelcome or inaccurate — in which case the entry is
  corrected or withdrawn on request, which is the mechanism working rather than
  failing; or evidence that publication measurably hardened a thread rather than
  opening it.
