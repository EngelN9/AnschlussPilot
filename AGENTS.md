# AGENTS.md

Operating rules for AI agents and human contributors working on **AnschlussPilot**.

Read this file fully before any non-trivial change. It is intentionally short so
that it is actually followed. Detail lives in [`docs/`](docs/) — read the
specific document that covers the layer you are touching.

---

## 0. Current Reality

**Check the repository before assuming anything about it** (I1). This section
goes stale the moment code lands; the date below tells you how far to trust it.

```text
Last verified:  2026-08-12
Observed state: documentation only — no application code, no provider
                integration, no dataset, no tests; D045 archived inconclusive
                at 1/30 observations; D046 Phase 0A pre-registered
```

If the date is old or the state does not match what you see, trust the
repository and update this block as part of your change.

The project is in **Phase 0** (see [`README.md`](README.md) §4): determine
whether the product thesis holds, using one corridor, offline replay, and no user
interface. Phase 0 blockers, in order:

1. D046 Phase 0A `REROUTE_EARLY` live kill-check — target three, at most five
   Bavarian cases across DB Navigator, MoBY and Wohin·Du·Willst. D045 remains
   archived `inconclusive` evidence and is not imported into the new denominator.
2. `docs/provider-evaluation.md` — public matrix partially filled; rights gate
   remains blocked and stops all data-dependent work, but not the live-surface
   kill-check. A product enquiry about Anschlussvormeldung was sent on
   2026-08-12 and is awaiting response; it does not open the rights gate.
3. Only if the competitor, early-reroute opportunity and minimum-data gates are
   complete and pass: A7 identity spike, carrier / A3 checks, corridor and
   observation-schema freeze; then the collector as soon as licensing permits.
4. Replay harness, deterministic baseline, full protocol freeze and opportunity
   measurement remain out of order while Phase 0A or provider rights are
   incomplete.

Work that does not serve Phase 0 requires an explicit reason. Building the UI,
adding ML, or widening geographic scope now is out of order, not merely early.

---

## 1. Hard Invariants

Numbered so they can be cited in review. Violating one is a defect regardless of
how well the code works. Numbers are stable — new rules get appended, never
renumbered.

Sixteen is more than anyone recalls under pressure, so they cluster into five
ideas. None is optional; the grouping is a memory aid, not a ranking.

| Cluster | Invariants | The idea |
| --- | --- | --- |
| Truth | I1, I2 | Say only what the repository can back up |
| Uncertainty | I4, I5, I8, I9 | Unknown stays unknown, all the way to the screen |
| Layering | I6, I7 | Provider data and time both flow one way |
| Decision discipline | I3, I10, I11, I12 | Compare actions; never react to a risk label |
| Boundaries | I13, I14, I15, I16 | Know what this system is not allowed to claim |

**I1 — The repository is the source of truth.**
Never infer implementation status from this file, the README, plans, issues, or
prior conversations. Inspect the actual code, tests, schemas, and configuration.

**I2 — Never claim an unverified capability.**
No invented providers, integrations, datasets, models, benchmarks, coverage,
deployment status, compliance, or licence permission. Planned work is labelled
planned. Unverifiable claims are labelled unverifiable. **Never report tests as
passing unless they were executed and passed.**

**I3 — Risk is not the decision.**
Never implement `HIGH_RISK ⇒ REROUTE` as a product rule. Risk assessment and
action recommendation are separate concepts, computed and tested separately.

**I4 — `UNKNOWN` is a valid result.**
Never map missing, stale, unsupported, or conflicting data to `SAFE` or
`CONTINUE`. Provider failure must degrade toward uncertainty, never toward
reassurance.

**I5 — Never fabricate confidence.**
No invented probabilities, no unjustified precision (`4–6 min`, not
`5 min 14 sec`). If calibration is not established, use categorical states.

**I6 — Provider payloads never become the domain model.**
`provider → adapter → normalization → canonical model → journey state →
risk / alternatives / decision`. Decision logic must not read raw provider JSON.

**I7 — Temporal correctness.**
Distinguish scheduled time, provider observation time, event effective time,
ingestion time, and decision time. **Historical evaluation may only use
information available at the evaluated decision time.**

**I8 — Freshness is part of correctness.**
The system must be able to express `LIVE` / `STALE` / `UNAVAILABLE`, and a
recommendation must know the freshness of its evidence. Never present stored data
as live because it is merely the newest record.

**I9 — Provenance is preserved.**
It must remain possible to tell what was provider-reported, what was inferred,
what was estimated, when it was observed, and when it was received. Never
silently promote an inference to a reported fact.

**I10 — `CONTINUE_CURRENT_PLAN` is always a candidate.**
Alternatives are never compared only against each other. The question is
*"is changing better than doing nothing?"*

**I11 — A false intervention is a real failure.**
Unnecessary rerouting, extra transfers, and unstable advice are costs. Do not
optimize solely for avoiding missed connections.

**I12 — Baseline before model.**
Deterministic, explainable rules first. ML must beat a stated baseline on
decision-quality metrics — not on AUC alone — before it ships.

**I13 — LLMs are never authoritative.**
Not for timetable facts, realtime state, transfer arithmetic, service identity,
normalization, fare validity, or legal entitlement. Generated text explains
already-structured conclusions and must stay consistent with `UNKNOWN`.

**I14 — Recommendations are not instructions or entitlements.**
Output is *"based on currently available information"*. Never *"you must take
this train"*, never *"you are entitled to board"*.
Ticket binding is an input the passenger supplies, never an inference. While
binding status is `UNKNOWN`, a candidate's executability stays `unknown` and must
not be presented as executable. See `README.md` §7.1.

**I15 — Data rights are established before collection.**
API availability implies nothing about storage, redistribution, training, or
commercial use. Verify current terms; document them next to the provider code.

**I16 — Privacy and security by default.**
Collect the minimum personal data. Never commit secrets. Treat all provider
responses as untrusted input and validate at the boundary. Enforce authorization
server-side if authentication is ever introduced.

---

## 2. Where Things Belong

Fix problems at the layer that owns them:

```text
provider adapter → normalization → service identity → state reconciliation
→ journey state → risk engine → alternative generation → outcome estimation
→ decision engine → API → frontend
                                        (persistence cuts across)
```

Patching a normalization bug in the decision engine, or a domain bug in the UI,
is a rejected change even if the symptom disappears.

Localized German strings belong in the presentation layer, never in domain logic
(`Anschluss`, `Umstieg`, `Verspätung`, `Zugausfall`, `Gleisänderung`).

---

## 3. Workflow

For every non-trivial change:

1. **Inspect** the relevant code, tests, schemas, configuration, and docs. Do not
   assume the architecture.
2. **Locate the owning layer** (§2).
3. **Check scope** — does this serve Phase 0 and stay inside the product boundary
   (§5)?
4. **Check temporal validity** — would this information actually be available at
   decision time? (I7)
5. **Implement the smallest coherent change.** No opportunistic refactors, no
   unrelated formatting, no dependency additions bundled with feature work.
6. **Test the domain cases**, not only the happy path. See
   [`docs/testing-catalogue.md`](docs/testing-catalogue.md).
7. **Review failure modes**: missing, stale, duplicate, out-of-order, conflicting
   data; provider failure; cross-midnight; recommendation instability.
8. **Review decision effects**: could this cause unnecessary rerouting? suppress a
   useful intervention? destabilize recommendations? couple risk to action?
9. **Update documentation** if behaviour or capability changed.
10. **Report truthfully**: what changed, why, what was tested, **what was not
    tested**, known limitations. Never fabricate validation.

**Progress is uncertainty removed, not output produced.** Every PR states the
uncertainty it targets, the evidence added, and which decision that evidence can
change. Files, features, and models completed are activity measures, not progress.

---

## 4. Definition of Done

Two checklists, applied at different moments. This project is small and mostly
single-author; a fourteen-item gate on every commit does not get trimmed when it
becomes burdensome, it gets **abandoned entirely**. Splitting it is what keeps
the important half alive.

### Every commit

- [ ] No invariant in §1 violated
- [ ] Change sits in the correct layer (§2)
- [ ] All applicable checks actually executed and pass; if none apply, that is
      reported explicitly
- [ ] No secrets; no unverified capability claim introduced

Four items. If a commit cannot clear these, nothing else matters.

### Deliverable complete

Run at the end of each deliverable in `README.md` §4 — not per commit.

- [ ] Requested behaviour implemented; nothing silently widened or narrowed
- [ ] Stale / missing / unknown paths considered
- [ ] Risk and action remain separate
- [ ] Relevant tests added or updated; type / lint checks pass where they exist
- [ ] Documentation matches behaviour
- [ ] Any `docs/` file whose subject matter changed has been updated (see the
      update trigger at the top of each one)
- [ ] Any decision a reasonable contributor could have made differently is
      recorded in [`docs/decisions.md`](docs/decisions.md), with its reversal
      condition
- [ ] The Active Decision Index in that file lists every new entry — an index
      that lags the entries makes the whole log unusable as a lookup
- [ ] Any new file under `docs/` carries an update trigger, or it is invisible
      to this checklist forever
- [ ] Any pre-report work satisfies the property gate in `README.md` §4, or has
      a recorded exception and rollback condition
- [ ] No new licensing assumptions
- [ ] Anything unverified is stated as unverified

It runs once per deliverable in `README.md` §4, not once per commit. The count is
deliberately not repeated here — a duplicated number is a drift defect waiting to
happen.

---

## 5. Scope Boundaries

German rail only. Do not expand into European rail, aviation, coaches, taxis, or
general multimodal routing because the abstractions would allow it.

AnschlussPilot is not a DB Navigator replacement, a delay tracker, a reliability
score site, a ticket shop, a payment processor, a reservation system, a carrier
account client, a compensation-claim system, an authoritative passenger-rights
engine, a guaranteed connection predictor, an indoor navigator, a European
planner, a multimodal super-app, or a marketing chatbot.

Introducing any of these indirectly requires updating `README.md` §10 first.

Do not introduce microservices, Kafka, Kubernetes, GraphQL, vector databases, LLM
orchestration, event sourcing, or workflow engines speculatively. Choose the
simplest architecture that preserves current correctness requirements.

Before adding a dependency: what does it solve, does the existing stack solve it,
what maintenance and security surface does it add, is its licence compatible?

---

## 6. When Priorities Conflict

```text
Railway-domain correctness
  → Temporal correctness
    → Data correctness
      → Decision usefulness
        → Decision stability
          → Reliability
            → Explainability
              → Prediction sophistication
                → Architectural cleverness
```

A sophisticated system that confidently recommends the wrong action is a failure.
A simple system that correctly says *"no reliable recommendation — realtime
information is insufficient"* is working.

When requirements are ambiguous, prefer the reading that preserves existing
behaviour, keeps scope narrow, protects domain and temporal correctness,
preserves uncertainty, avoids unnecessary intervention, and avoids irreversible
architecture. Document the assumption; do not build a subsystem to resolve a
small ambiguity.

---

## 7. Detail Documents

| Document | Covers |
| --- | --- |
| [`docs/railway-domain.md`](docs/railway-domain.md) | Railway reality, service identity, observations as events, state reconciliation, temporal handling, freshness, provenance |
| [`docs/decision-model.md`](docs/decision-model.md) | Domain concepts, connection feasibility, risk vocabulary, candidate actions, outcome estimation, stability, explainability |
| [`docs/modelling-and-evaluation.md`](docs/modelling-and-evaluation.md) | Historical reconstruction, backtesting, baselines, ML policy, leakage, calibration, decision-policy evaluation |
| [`docs/phase0-protocol.md`](docs/phase0-protocol.md) | Single versioned freeze record for the Phase 0 corridor, data contracts, population, episode rules, policy and measurement window |
| [`docs/testing-catalogue.md`](docs/testing-catalogue.md) | Railway edge cases, decision cases, provider contract tests |
| [`docs/market-and-validation.md`](docs/market-and-validation.md) | Customer hypotheses, competitive benchmark, distribution risk, commercial tracks, Phase 0.5 experiments |
| [`docs/binding-scenarios.md`](docs/binding-scenarios.md) | Versioned ticket-binding ruleset behind the A6 scenario band. A sensitivity assumption, never a legal determination |
| [`docs/decisions.md`](docs/decisions.md) | Why things are the way they are, and what would reverse each choice |
| [`docs/provider-evaluation.md`](docs/provider-evaluation.md) | Feed capabilities and data rights. Public matrix partially filled; rights gate still blocks Phase 0 data-dependent work |
| [`README.md`](README.md) | Product definition, unvalidated assumptions, known constraints, success criteria |

`docs/provider-evaluation.md` contains a partially filled public-source matrix,
but no provider has sufficient verified rights. The closed rights gate blocks
Phase 0 data-dependent work. It does not block the A5a live competitive
benchmark.

---

## 8. Repository Conventions

**Naming.** Use domain terms: `Connection`, `ServiceRun`, `RealtimeObservation`,
`RiskAssessment`, `DecisionCandidate`, `OutcomeEstimate`,
`DecisionRecommendation`, `TransferBuffer`. Not `Manager`, `Helper2`,
`DataProcessor`, `GenericService`.

**Types.** Validate at boundaries. Represent stable domain states as enums or
discriminated unions, never as free-form strings.

**Persistence.** Inspect existing migrations before changing schema; preserve
migration history. Historical observation schemas need extra care — a migration
mistake can invalidate later backtesting.

**Errors.** Distinguish provider unavailable, provider rejected, malformed data,
unsupported journey, ordinary missing information, ordinary `UNKNOWN`, internal
error, and database failure. Do not collapse them where the distinction matters.

**Accessibility.** Never communicate risk or action by colour alone. Use explicit
labels, semantic HTML, keyboard access. Accessibility regressions are defects.

**Markdown math.** Keep display formulas on a single line between `$$`
delimiters. Multi-line LaTeX in this repository was previously corrupted by a
formatter reading `=` and `-` continuation lines as setext headings.

**Commits.** Keep changes focused. Do not combine feature work, refactoring,
dependency upgrades, formatting, and schema redesign in one commit.

**Branches and pull requests.** Non-trivial work uses one `codex/<deliverable>`
branch and one PR per coherent deliverable. Direct `main` changes require an
explicit user instruction. A PR body states: uncertainty targeted, evidence
added, decisions affected, checks executed, and anything still unverified.
Merge only after the applicable §4 checks pass. Use squash merge normally; use
a merge commit when a frozen protocol cites a branch commit SHA.

**Synchronization.** Start from a clean, up-to-date `main` using fetch/prune and
fast-forward-only pull. Push the working branch before ending a work session.
After merge, update local `main`, confirm `main...origin/main` is `0/0` with a
clean worktree, then delete the merged branch. GitHub Desktop must point to this
same local repository; CLI upstream state is the synchronization authority.

---

## 9. The Question That Decides Everything

> **Does this make AnschlussPilot better at deciding what a passenger should do
> while there is still time to improve the journey outcome?**

If no, reconsider whether the change belongs in the product.
