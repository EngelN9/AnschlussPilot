# Provider Evaluation

Phase 0 data-dependent-work blocker (`README.md` §4). Until the matrix below is
filled from primary sources, no collector may retain data and no capability may
be described as supported (`AGENTS.md` I2, I15). This does not block A5a.

This matrix evaluates **data providers only**. It does not establish carrier,
ticket-issuer, fare-validity, binding-relief, or passenger-rights conditions.
Those require the independent carrier-conditions evidence described in
[`binding-scenarios.md`](binding-scenarios.md) §3.

> **Status: UNFILLED SCAFFOLD.** Every cell below is `?`. A `?` means *not
> verified*, never *probably fine*. Do not copy answers from documentation
> summaries, blog posts, community wikis, or model output — only from the
> provider's own current terms page, with the URL and date recorded.

> **Update trigger:** re-verify whenever a provider changes terms, when adding a
> provider, and at minimum before any change to what the collector retains.
> Checked in `AGENTS.md` §4.

---

## 1. Column Definitions

| Column | Question being answered |
| --- | --- |
| Realtime | Does it expose live operational state, and at what update granularity and latency? |
| Platform | Are platform (`Gleis`) values exposed, and are changes exposed? |
| Hold signal | Is dispatcher connection protection (*Anschlusssicherung* — whether the outgoing service is being held) exposed in any form? **This bounds A2.** |
| Identity | Is there a stable journey identifier, or only train number + date? **This bounds service identity work (A7).** |
| Transfer times | Are station minimum transfer times (*Mindestumsteigezeit*) available? **This is the only identified candidate source for A3.** |
| Topology | Any platform-pair, level-change, or walking-distance data? Determines whether `T_transfer` can be narrowed below station-level constants. |
| Storage | May responses be persisted beyond immediate display, and for how long? |
| Redistribution | May derived or raw data be shown to third parties / published? |
| Training | May retained data be used to train statistical or ML models? |
| Commercial | May it be used in a commercial or monetized product? |
| Attribution | Is attribution required, and in what exact form? |
| Rate limits | Requests per interval; is a corridor-wide poll at the intended cadence feasible? |
| Access | Registration, key, approval process, cost |
| Source | URL of the terms consulted + date consulted |

---

## 2. Matrix

Add one row per candidate. Names left blank deliberately — fill in what you
actually evaluated.

```text
Provider snapshot ID     UNSET
Verdict version          UNSET
Terms effective date     UNSET
Terms checked date       UNSET
```

Once verified, these values and the chosen retention / payload policy are copied
into [`phase0-protocol.md`](phase0-protocol.md). Until then the collector gate is
closed.

| Provider | Realtime | Platform | Hold signal | Identity | Transfer times | Topology | Storage | Redistribution | Training | Commercial | Attribution | Rate limits | Access | Source (URL + date) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| ? | ? | ? | ? | ? | ? | ? | ? | ? | ? | ? | ? | ? | ? | ? |
| ? | ? | ? | ? | ? | ? | ? | ? | ? | ? | ? | ? | ? | ? | ? |
| ? | ? | ? | ? | ? | ? | ? | ? | ? | ? | ? | ? | ? | ? | ? |

---

### Verified finding — the decisive signals exist, behind a partner gate

Consulted 2026-08-10:
[DB API Marketplace — RIS::Connections (DB Transporteure)](https://developers.deutschebahn.com/db-api-marketplace/apis/product/ris-connections-transporteure)

| Field | What the product page states |
| --- | --- |
| Hold signal | *"Informationen ob Anschlüsse `warten` oder `nicht warten`"* — **A2b true** |
| Transfer times | *"gleisscharfe Umsteigezeiten"*, with a personalized reachability assessment for occasional travellers, commuters and mobility-impaired travellers — **A3b true** |
| Access | *"Zugang erfolgt nach positiver Prüfung **ausschließlich für Vertriebspartner der Deutschen Bahn AG**"* |
| Pricing | *"Kostenpflichtig (Preis auf Anfrage)"* |
| Terms | *"Nutzungsbedingungen werden vertraglich vereinbart"* — no published standard terms |

**What this settles and what it does not.** The two assumptions most likely to
cap this project — hold signals (A2) and platform-level transfer times (A3) —
are not physically unobservable. They exist, in a documented feed. The open
question is **A2c / A3c: whether this project may use them.** Access is limited
to DB sales partners after review, priced individually, and governed by a
negotiated contract rather than public terms.

Three consequences:

1. **The access question outranks the modelling questions.** It is investigated
   in parallel with A5a (`README.md` §4), not deep inside Phase 0.
2. **Any `UNKNOWN` rate this project measures is tier-relative.** It reflects the
   feeds obtainable here, not a limit of German rail — see `README.md` A2 and
   **S4**.
3. **Revocability becomes a first-order risk** (**S11**). A moat built on access
   the incumbent grants and can withdraw is not a moat, and it points the
   strategy toward B2B2C or research (**S12**).

The matrix above must still be filled for whichever feeds are actually
obtainable. This finding narrows the search; it does not complete it.

---

## 3. What Each Answer Decides

This is the part that makes the matrix worth filling. Write the verdict, not just
the facts.

| Finding | Consequence |
| --- | --- |
| **Storage not permitted** | Phase 0 cannot be built as designed. The historical store, backtesting, and the entire moat argument (`README.md` §3 A4) collapse. Fallback is ephemeral live evaluation with no reconstruction — a different, weaker product. **Decide this before writing the collector.** |
| **Storage permitted, retention capped** | Collector needs enforced retention and deletion from day 1. A cap shorter than the frozen measurement window in [`phase0-protocol.md`](phase0-protocol.md) may make A1 unmeasurable. |
| **Training use not permitted** | The ML roadmap in [`modelling-and-evaluation.md`](modelling-and-evaluation.md) §5 is dead for this provider. Deterministic policy only. This is survivable — say so explicitly rather than leaving it ambiguous. |
| **Redistribution not permitted** | Constrains what the UI may show and rules out publishing datasets or examples. Affects §9 of the README. |
| **Hold signal absent** | A2 fails or is severely bounded. Expect a high `UNKNOWN` rate and set the §12 target accordingly. **This is the finding most likely to cap the product's ceiling.** |
| **No stable journey identity** | Service identity work becomes heuristic. Budget for it and test it explicitly (`testing-catalogue.md` §4). The identity spike (A7) measures how bad it actually is; **S9** is the exit. |
| **No transfer-time or topology data** | A3 has no source. `T_transfer` stays a wide interval, pushing cases toward `ATTENTION`/`UNKNOWN`. **S5** applies if it cannot be bounded within ±5 min. |
| **Rate limits below corridor-wide polling** | Forces a narrower corridor or a slower cadence, which directly reduces achievable warning lead time. Recompute the Phase 0 scope. |
| **Attribution required** | Becomes a UI requirement, not a footnote. Record the exact required wording. |
| **Commercial use prohibited** | Fine for Phase 0. Record it so it is not discovered later after building on it. |

---

## 4. Verdict

_Not written yet — requires §2._

The project pursues a research result (**R**) and a product prototype (**P**) in
sequence (`README.md` §4). **Their permission requirements differ, so the verdict
answers both separately.** The same terms check produces both answers at no extra
cost, and finding that only R is permitted is a useful result, not a failure.

### 4.1 Is R permitted?

Requires: retention long enough to measure, and use for private analysis.

```text
Retention permitted        ?
Retention window           ?
Analysis / research use    ?
Model training use         ?   (affects modelling-and-evaluation.md §5, not R itself)
Verdict for R              ?
```

If retention is **not** permitted → `README.md` §12 **S1** applies: hard stop on
Phase 0 as designed. Redesign as ephemeral live evaluation, or change provider.

### 4.2 Is P permitted?

Requires everything R requires, plus showing derived data to other people.

```text
Redistribution / display to third parties   ?
Attribution required, exact wording         ?
Commercial use                              ?
Verdict for P                               ?
```

If R passes and P fails → drop P, continue R unchanged. Record it as a decision
in [`decisions.md`](decisions.md), superseding **D005**.

### 4.3 Then state plainly

1. Which provider Phase 0 will use, and why.
2. Exactly what the collector may retain, and for how long.
3. Which of `README.md` A2 / A3 / A4 / A7 are now answered, bounded, or open.
4. What each of R and P must give up as a result.
5. Which stop conditions in `README.md` §12 are now triggered, if any.
