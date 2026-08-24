# Provider Evaluation

Phase 0 data-dependent-work blocker (`README.md` §4). Until the capability and
rights evidence below identifies an obtainable provider with sufficient rights,
no collector may retain data and no capability may be described as supported
(`AGENTS.md` I2, I15). This does not block A5a.

This matrix evaluates **data providers only**. It does not establish carrier,
ticket-issuer, fare-validity, binding-relief, or passenger-rights conditions.
Those require the independent carrier-conditions evidence described in
[`binding-scenarios.md`](binding-scenarios.md) §3.

> **Status: PARTIAL EVIDENCE v4 — RIGHTS GATE BLOCKED.** Four DB products, two
> DB data streams and relevant DELFI public leads have been checked against
> current primary sources. Written replies now close the individual zero-budget
> path for RIS::Connections, RIS::Stations and DB GTFS data, but neither reply
> grants usable retention or downstream-use rights. The DB data-stream reply
> does not expressly answer RiFahrt, and the DELFI-Realtime enquiry remains
> unanswered. `UNKNOWN` means *not answered for this project*, never *probably
> fine*. No account, paid plan, API call, credential request or partner
> application was used.

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
| Cost | Published price or `UNKNOWN` / price on request |
| Revocability | Whether continued access is contractually protected or can be withdrawn |
| Source | URL of the terms consulted + date consulted |

---

## 2. Matrix

```text
Provider snapshot ID          provider-primary-evidence-2026-08-17-v4
Verdict version               v4-partial
Marketplace general terms     Stand 05/2022
Product-contract dates        UNKNOWN — the public product pages state no effective date
Terms checked date            2026-08-11
Provider response dates        2026-08-11 — DB RIS-API Team; 2026-08-17 — DB GTFS product function
```

Once verified, these values and the chosen retention / payload policy are copied
into [`phase0-protocol.md`](phase0-protocol.md). Until then the collector gate is
closed.

### 2.1 Capability and access evidence

| Product and version | Realtime | Platform | Hold signal | Service identity | Transfer / topology | Rate limits | Access and cost | Source, checked or received by 2026-08-17 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **RIS::Connections** product `1.0.208`, API `1.18.0` | Current forecasts, disruption state and connection status are inputs; public page gives no latency guarantee | Yes — platform-precise connection assessment | **Yes** — waiting / not-waiting disposition status | Request uses `journeyID` + `arrivalID`; cross-observation stability remains unverified pending A7 | Platform-precise transfer times, indoor-routing inputs where available, fallback corporate transfer rules and station-area transfers | 100 req/s plus 5k–500k requests/day by plan | **Only DB sales partners**; DB's written response says free access is not generally provided; paid, price on request | [Marketplace product](https://developers.deutschebahn.com/db-api-marketplace/apis/product/ris-connections-transporteure); [official API guide](https://developer-docs.deutschebahn.com/doku/apis/ris-connections-10686902); redacted response mapping in §6.1 |
| **RIS::Journeys** product `1.0.273`, API `2.12.0` | Yes — scheduled and forecast arrivals/departures, cancellations and journey changes; no public latency guarantee | Yes — platforms / bus bays | Not documented | `journeyID` and match/find endpoints exist; stability remains unverified pending A7 | No transfer-time or station-topology claim | 100 req/s plus 5k–500k requests/day by plan | Positive eligibility review; paid, price on request | [Marketplace product](https://developers.deutschebahn.com/db-api-marketplace/apis/product/ris-journeys-transporteure); [official API guide](https://developer-docs.deutschebahn.com/doku/apis/ris-journeys-10582266) |
| **Timetables** product/API `1.0.274` | Yes — planned timetable plus current changes at a station; no public latency guarantee | `UNKNOWN` from the consulted public product page | Not documented | Station-scoped plan/change records; stable cross-station journey identity not established | No transfer-time or topology claim | 60 requests/minute | Marketplace registration/subscription; free plan | [Marketplace product](https://developers.deutschebahn.com/db-api-marketplace/apis/product/timetables) |
| **RIS::Stations** product `1.29.3448`, API `1.29.1.1` | Primarily versioned station master data, not journey realtime | Yes — platform structures and sectors | Not applicable / not documented | Station, stop-place and platform keys; not a journey-identity source | Transfer times by traveller type, transfer areas, platform structure; official guide permits initial storage and incremental refresh of station master data | Test: 10 req/s and 10k/month; paid plans: 100 req/s and 150k–15m/month | DB's written response offers subscription only, with monthly cost based on use. The public page advertises a two-month free test; no eligible free offer was made to this enquiry, so the zero-budget path stays closed without registration | [Marketplace product](https://developers.deutschebahn.com/db-api-marketplace/apis/product/ris-stations); [official API guide](https://developer-docs.deutschebahn.com/doku/apis/ris-stations-10686906); redacted response mapping in §6.1 |
| **DB GTFS / GTFS-RT data streams**, public documentation has no product/API version | Yes — planned and realtime trips for DB Fernverkehr and DB Regio; GTFS-RT covers delays, stop cancellations, added trips, platform changes and disruptions; realtime horizon 24 hours | Yes — planned platforms and realtime platform changes | Not documented | **Documented stable trip IDs across GTFS and GTFS-RT** | GTFS includes transfer times and accessible-boarding information; no platform-pair topology claim | No quota published; DB recommends GTFS every 5 minutes and GTFS-RT every 20 seconds with `ETag` | DB's written response says the data cannot currently be supplied as Open Data. Paid access may be considered only after internal review and support from DB Regio / DB Fernverkehr according to scope; price and terms remain `UNKNOWN` | [Official data-stream guide](https://developer-docs.deutschebahn.com/doku/datenstroeme/stroeme-gtfs-10582270); redacted response mapping in §6.2 |
| **RiFahrt data stream**, public documentation has no product/API version | Yes — planned and realtime journey events for DB Fernverkehr and DB Regio; subscriptions may cover up to 14 days ahead | Yes — actual and planned platforms, including changes | Not documented | **Documented stable journey IDs**, linkable to DB APIs and GTFS / GTFS-RT | No transfer-time or topology claim | Event stream; no public quota or throughput commitment | Registration, a provider-supplied protobuf and RabbitMQ credentials required. The written response answered an enquiry that named RiFahrt but did not name or separately address RiFahrt; eligibility and cost remain `UNKNOWN` | [Official data-stream guide](https://developer-docs.deutschebahn.com/doku/datenstroeme/RiFahrt-13745610); redacted response mapping in §6.2 |

`Not documented` means the consulted primary source makes no claim for that
field. It is not evidence of absence. Version numbers are snapshots, not a
compatibility commitment.

### 2.2 Rights evidence

| Product | Storage | Retention | Redistribution | Training | Commercial use | Attribution | Revocability |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **RIS::Connections** | `UNKNOWN` — contract required | `UNKNOWN` — contract required | `UNKNOWN` — contract required | `UNKNOWN` — contract required | `UNKNOWN` — contract required | `UNKNOWN` — contract required | `UNKNOWN` — approval and contract required; no public continuity promise |
| **RIS::Journeys** | `UNKNOWN` — contract required | `UNKNOWN` — contract required | `UNKNOWN` — contract required | `UNKNOWN` — contract required | `UNKNOWN` — contract required | `UNKNOWN` — contract required | `UNKNOWN` — approval and contract required; no public continuity promise |
| **Timetables** | **Permitted for the CC BY 4.0 licensed dataset**; reproduction is licensed | No published retention cap found | **Permitted with CC BY 4.0 conditions** | `UNKNOWN` — model training is not expressly addressed; no legal interpretation is assumed | **Permitted by CC BY 4.0**, subject to attribution and other applicable rights | Preserve supplied creator/copyright/licence/disclaimer/source information, indicate modifications and link the licence when sharing | CC BY 4.0 grant is irrevocable while its conditions are followed; API availability itself has no public continuity commitment |
| **RIS::Stations** | Official guide expressly supports storing and incrementally updating station master data; rights for the transfer/topology subset remain `UNKNOWN` pending contract/scope confirmation | `UNKNOWN` for the contract-governed API and transfer/topology subset | CC BY 4.0 applies to stated *Stationswissen*; exact inclusion of transfer/topology fields is `UNKNOWN` | `UNKNOWN` — not expressly addressed and licence scope is unresolved | `UNKNOWN` for the transfer/topology subset; contract required | CC BY 4.0 attribution for covered *Stationswissen*; OpenStreetMap attribution also applies to state-boundary data | `UNKNOWN` for API/contract access; the CC BY grant for material actually covered remains irrevocable while compliant |
| **DB GTFS / GTFS-RT** | `UNKNOWN` — public stream documentation gives technical access instructions but no applicable content licence | `UNKNOWN` | `UNKNOWN` | `UNKNOWN` | `UNKNOWN` | `UNKNOWN` | `UNKNOWN` — credentials are required and no public continuity or termination terms for the streams were found |
| **RiFahrt** | `UNKNOWN` — public documentation describes consumption, not content-use rights | `UNKNOWN` | `UNKNOWN` | `UNKNOWN` | `UNKNOWN` | `UNKNOWN` | `UNKNOWN` — registration and provider-issued credentials are required; applicable agreement not public |

The CC BY conclusions above use the
[CC BY 4.0 legal code](https://creativecommons.org/licenses/by/4.0/legalcode.en)
linked by DB: it permits reproduction, sharing and adapted material, including
relevant database-right uses, subject to attribution. It grants only rights the
licensor is authorised to grant; it does not settle privacy, trademark,
third-party or contract scope. That is why training and the boundary of
RIS::Stations *Stationswissen* remain `UNKNOWN` rather than inferred.

### 2.3 Marketplace general terms do not grant data rights

The [DB API Marketplace general terms](https://developers.deutschebahn.com/db-api-marketplace/apis/nutzungsbedingungen),
version `Stand 05/2022`, were checked on 2026-08-11. They establish a general
portal layer, not the product licence AnschlussPilot needs:

- the Marketplace is described as available to commercial and private
  developers, but there is no entitlement to registration or activation;
- access to DB content remains subject to the applicable separate licence, and
  registration or activation itself grants no content-use rights;
- a separate licence or contract takes precedence over the general terms;
- either side may terminate the general Marketplace agreement without notice,
  and DB may limit calls, change or delete content, or stop the Marketplace.

These facts strengthen the access-continuity risk. They do **not** prove that a
future product contract would be terminable on the same terms, because the
general terms expressly defer to the separate agreement. Product-level storage,
retention, commercial use and revocability therefore remain `UNKNOWN`.

### 2.4 Open auxiliary sources and unresolved leads

The [DELFI nationwide schedule dataset](https://www.opendata-oepnv.de/ht/de/organisation/delfi/startseite?amp=&cHash=136faae755c82cae8604462f8c8b4f10&tx_vrrkit_view%5Baction%5D=details)
is a useful auxiliary planning source: its public metadata says the GTFS / NeTEx
dataset includes German local public transport and rail long-distance service,
is licensed under Creative Commons Attribution, and requires registration for
download. The page does not state the Creative Commons version. This source can
support timetable population and enumeration, but it is not decision-time
realtime evidence and exposes no hold signal.

DELFI states that nationwide public-transport realtime data (`DELFI-Realtime`)
has been made available through Mobilithek and that the streams use GTFS
Realtime Trip Updates and SIRI Estimated Timetable. This pass did **not** locate
an exact current public metadata record establishing endpoint, licence,
retention, commercial use, stable identity or long-distance coverage. It remains
an unresolved lead, not a provider capability or rights finding. Sources checked
2026-08-11: [DELFI overview](https://www.delfi.de/) and
[DELFI current information](https://www.delfi.de/de/aktuelles/).

---

### Verified finding — the decisive signals exist, behind a partner gate

Re-checked 2026-08-11:
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

The matrix above now records the public evidence for four products and two data
streams. GTFS / GTFS-RT and RiFahrt materially improve the public capability
picture for realtime and stable service identity, but neither documents a hold
signal or sufficient use rights. No obtainable decisive-signal feed with
sufficient rights has been identified; the gate remains closed.

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

> **Verdict v4-partial: BLOCKED.** Public primary sources establish useful
> capabilities, including stable IDs in DB GTFS / GTFS-RT and RiFahrt. Written
> DB replies close the current individual zero-budget paths for RIS products and
> DB GTFS data: RIS::Connections is sales-partner-only, RIS::Stations is paid,
> and DB GTFS data cannot currently be supplied as Open Data. The DB stream
> reply leaves RiFahrt and every downstream-use right unanswered, while the
> DELFI-Realtime enquiry remains pending. No Phase 0 provider is selected. A7,
> polling and collector work remain prohibited.

The project pursues a research result (**R**) and a product prototype (**P**) in
sequence (`README.md` §4). **Their permission requirements differ, so the verdict
answers both separately.** The same terms check produces both answers at no extra
cost, and finding that only R is permitted is a useful result, not a failure.

### 4.1 Is R permitted?

Requires: retention long enough to measure, and use for private analysis.

```text
Retention permitted        UNKNOWN for decisive realtime data
Retention window           UNKNOWN for decisive realtime data
Analysis / research use    UNKNOWN for decisive realtime data
Model training use         UNKNOWN for decisive realtime data
Verdict for R              BLOCKED pending eligibility and contractual rights
```

If retention is **not** permitted → `README.md` §12 **S1** applies: hard stop on
Phase 0 as designed. Redesign as ephemeral live evaluation, or change provider.

### 4.2 Is P permitted?

Requires everything R requires, plus showing derived data to other people.

```text
Redistribution / display to third parties   UNKNOWN for decisive realtime data
Attribution required, exact wording         UNKNOWN for decisive realtime data
Commercial use                              UNKNOWN for decisive realtime data
Verdict for P                               BLOCKED; no product route authorised
```

If R passes and P fails → drop P, continue R unchanged. Record it as a decision
in [`decisions.md`](decisions.md), superseding **D005**.

### 4.3 Consequences

1. **Provider selection:** none. Timetables is CC BY 4.0; DELFI's public
   schedule metadata identifies a Creative Commons Attribution licence without
   stating its version. Both are useful planning sources, but neither documents
   the decisive hold signal. DB GTFS / GTFS-RT and RiFahrt document realtime
   data and stable IDs, but the written DB response closes the GTFS Open Data
   path without granting the required rights and does not expressly answer
   RiFahrt. RIS::Connections documents the hold and transfer signals but is
   restricted to DB sales partners under individually agreed terms.
2. **Collector policy:** none can be frozen. No decisive-signal payload may be
   polled or retained until eligibility, storage and retention are expressly
   established.
3. **Assumptions:** A2b and A3b remain verified; A2c, A3c and A4 remain
   `UNKNOWN`; A7 remains untested despite stable identity being documented in
   RIS::Journeys, DB GTFS / GTFS-RT and RiFahrt.
4. **Research and product:** both R and P remain blocked for the designed
   longitudinal decision experiment. This result does not authorise an
   ephemeral redesign or a B2B2C implementation.
5. **Stop conditions:** S1 is not triggered because storage was not answered.
   S11 and S12 are not triggered because DELFI-Realtime and the RiFahrt-specific
   access position remain unresolved; the DB replies narrow two zero-budget
   paths but do not settle every possible decisive-signal source. The rights
   gate remains closed under I15. Await the DELFI reply; do not register, apply,
   request credentials, pay or call an API.

### 4.4 Evidence still required to change the verdict

- Explicit RiFahrt eligibility and cost, because the DB data-stream response did
  not separately address it.
- Written eligibility for any remaining candidate source, including
  DELFI-Realtime, for AnschlussPilot's intended research and possible product
  use.
- Contract terms covering payload storage, retention, research analysis,
  third-party display, commercial use, attribution and termination.
- Confirmation that the licensed scope of RIS::Stations includes the specific
  transfer/topology fields Phase 0 would retain.
- Exact DELFI-Realtime metadata covering endpoint, current access procedure,
  licence, long-distance coverage, stable identity and downstream-use rights.
- Only after those rights pass: a bounded A7 identity spike and cadence/coverage
  feasibility check using the authorised products.

---

## 5. Zero-Budget External Confirmation Package — Sent

These messages are evidence-gathering tools, not evidence. The author has chosen
to write as an individual independent researcher and to spend nothing before
Phase 0 evidence justifies reconsideration (**D044**). No account is created, no
terms are accepted, no API is called and no payment information is supplied
before a written eligibility and rights response is reviewed.

All three provider messages were sent separately on 2026-08-11 so that each
recipient could answer for the products it owns. Public routing addresses
checked 2026-08-11 are
`ris-api@deutschebahn.com` with `api-marketplace@deutschebahn.com` copied for
RIS / Marketplace questions, `ris-gtfs@deutschebahn.com` for DB data streams,
and `info@delfi.de` for DELFI. The original drafts remain below as provenance.

| Enquiry | Sent | Current state |
| --- | --- | --- |
| DB API Marketplace / RIS | 2026-08-11 | Responded 2026-08-11; redacted evidence in §6.1 |
| DB GTFS / GTFS-RT / RiFahrt | 2026-08-11 | Responded 2026-08-17; RiFahrt and rights fields were not expressly answered; redacted evidence in §6.2 |
| DELFI-Realtime / Mobilithek | 2026-08-11 | `follow-up sent 2026-08-22; awaiting response`; only unanswered provider enquiry |

### 5.1 DB API Marketplace and RIS

```text
An: ris-api@deutschebahn.com
Cc: api-marketplace@deutschebahn.com
Betreff: Kostenfreier Forschungszugang zu RIS::Connections und RIS::Stations

Guten Tag,

ich prüfe als Privatperson für das unabhängige, nicht mit der Deutschen Bahn
verbundene Forschungsprojekt „AnschlussPilot“, ob sich Entscheidungen bei
gefährdeten Bahnanschlüssen in einer begrenzten Phase untersuchen lassen. Das
Projekt verfügt in Phase 0 über kein Budget. Untersucht werden soll ein einzelner
deutscher Fernverkehrskorridor; der genaue Korridor wird erst nach Klärung des
Datenzugangs festgelegt.

Kann eine Privatperson ohne Status als DB-Vertriebspartner einen vollständig
kostenfreien Forschungs- oder Testzugang zu RIS::Connections und RIS::Stations
erhalten? Bitte bestätigen Sie auch, ob ein Testzugang automatisch endet, ohne
in einen kostenpflichtigen Vertrag überzugehen.

Für jedes grundsätzlich verfügbare Produkt benötige ich bitte eine schriftliche
Klärung:

1. Dürfen rohe Antworten und daraus normalisierte Beobachtungen gespeichert
   werden, und wie lange?
2. Sind private Forschungsanalyse und die Veröffentlichung aggregierter,
   nicht personenbezogener Ergebnisse zulässig?
3. Sind die Anzeige abgeleiteter Informationen, spätere kommerzielle Nutzung
   und Modelltraining zulässig oder ausgeschlossen?
4. Welche Quellenangaben sowie Kündigungs- und Löschpflichten gelten?
5. Umfasst die CC-BY-Lizenz für RIS::Stations auch Umsteigezeiten,
   Umsteigebereiche und Gleisstrukturen?
6. Welche Abrufgrenzen und Zusagen zur fortlaufenden Verfügbarkeit gelten?

Falls dieser Zugang für Privatpersonen nicht möglich ist, welche offizielle
kostenfreie Datenquelle empfehlen Sie für diese Forschungsfrage? Vor einer
schriftlichen Klärung werde ich weder ein Marketplace-Konto anlegen noch Daten
abrufen oder speichern.

Mit freundlichen Grüßen
[Name]
Privatperson / unabhängiges Forschungsprojekt
[Land / Rechtsordnung]
[E-Mail]
```

### 5.2 DB GTFS / GTFS-RT and RiFahrt

```text
An: ris-gtfs@deutschebahn.com
Betreff: Kostenfreier Forschungszugang zu DB GTFS / GTFS-RT und RiFahrt

Guten Tag,

ich prüfe als Privatperson für das unabhängige Forschungsprojekt
„AnschlussPilot“, ob sich Entscheidungen bei gefährdeten Bahnanschlüssen in
einer begrenzten Phase auf einem einzelnen deutschen Fernverkehrskorridor
untersuchen lassen. Das Projekt verfügt in Phase 0 über kein Budget und ist
nicht mit der Deutschen Bahn verbunden.

Können DB GTFS / GTFS-RT und RiFahrt einer Privatperson für diese Untersuchung
vollständig kostenfrei bereitgestellt werden? Falls ja, wie können die benötigten
Zugangsdaten beantragt werden, und endet ein Testzugang ohne automatische
Umstellung auf einen kostenpflichtigen Vertrag?

Bitte bestätigen Sie für jeden verfügbaren Datenstrom:

1. welche Verkehre und Betreiber enthalten sind und ob stabile Fahrt-IDs die
   Verknüpfung von Plan- und Echtzeitdaten erlauben;
2. ob rohe Feeds und normalisierte Beobachtungen gespeichert werden dürfen und
   welche maximale Aufbewahrungsfrist gilt;
3. ob private Forschungsanalyse, aggregierte Veröffentlichung, Anzeige
   abgeleiteter Informationen, spätere kommerzielle Nutzung und Modelltraining
   jeweils erlaubt sind;
4. welche Quellenangaben, Abrufgrenzen, Verfügbarkeitszusagen sowie Kündigungs-
   und Löschpflichten gelten.

Falls kein Zugang für Privatpersonen besteht, welche offizielle kostenfreie
Alternative empfehlen Sie? Vor Ihrer schriftlichen Antwort werde ich keine
Zugangsdaten beantragen und keine Daten abrufen oder speichern.

Mit freundlichen Grüßen
[Name]
Privatperson / unabhängiges Forschungsprojekt
[Land / Rechtsordnung]
[E-Mail]
```

### 5.3 DELFI-Realtime

```text
An: info@delfi.de
Betreff: Anfrage zu DELFI-Realtime auf der Mobilithek

Guten Tag,

ich prüfe als Privatperson für das unabhängige, derzeit vollständig
unfinanzierte Forschungsprojekt „AnschlussPilot“ Datenquellen für eine begrenzte
Untersuchung gefährdeter Bahnanschlüsse auf einem einzelnen deutschen
Fernverkehrskorridor.

Bitte nennen Sie mir den aktuellen Mobilithek-Datensatz beziehungsweise die
Angebots-ID für DELFI-Realtime und bestätigen Sie:

1. Welche Verkehre und Betreiber sind enthalten, insbesondere SPNV und SPFV?
2. Welche stabilen Fahrt-IDs ermöglichen die Verknüpfung mit den DELFI-Solldaten?
3. Ist der Zugang für eine Privatperson vollständig kostenfrei, und sind
   Registrierung, Freischaltung oder ein Vertrag erforderlich?
4. Dürfen Rohdaten und normalisierte Beobachtungen gespeichert werden, und wie
   lange?
5. Sind private Forschungsanalyse, aggregierte Veröffentlichung, Anzeige
   abgeleiteter Informationen, spätere kommerzielle Nutzung und Modelltraining
   jeweils erlaubt?
6. Welche Quellenangaben, Abrufgrenzen, Verfügbarkeitszusagen sowie Kündigungs-
   und Löschpflichten gelten?

Falls DELFI-Realtime hierfür nicht verfügbar ist, welche offizielle kostenfreie
Alternative empfehlen Sie? Vor einer schriftlichen Klärung werde ich mich nicht
registrieren und keine Daten abrufen oder speichern.

Mit freundlichen Grüßen
[Name]
Privatperson / unabhängiges Forschungsprojekt
[Land / Rechtsordnung]
[E-Mail]
```

### 5.4 Follow-up and evidence handling

Before sending, the author reviewed the German text, replaced only the identity
placeholders and sent from the matching personal address. No company name,
invented affiliation, API credential, payment information or personal travel
example was added.

For each message:

1. record the sent date and recipient privately;
2. after seven business days without a response, send one reply in the same
   thread: `Guten Tag, ich möchte höflich an meine unten stehende Anfrage
   erinnern. Können Sie mir bitte mitteilen, ob und an welche zuständige Stelle
   ich sie gegebenenfalls weiterleiten soll? Vielen Dank.`;
3. after another seven business days without a response, record
   `inconclusive / no response`; never convert silence into refusal;
4. keep the raw email and identity data in the private mailbox, outside Git;
5. commit only a redacted evidence mapping: organisation / department, response
   date, products addressed, each rights field answered or `UNKNOWN`, and the
   resulting gate effect.

A free account, trial or subscription is considered only after the written
response and applicable terms have both been reviewed. It must require no
payment method, must not auto-convert to a paid plan and must grant the retention
needed for the bounded research period. Otherwise it does not open the rights
gate. Provider-response evidence gets its own
`codex/provider-outreach-evidence-v1` branch and never enters the A5a PR.

As of 2026-08-22 only DELFI-Realtime remains unanswered. The frozen same-thread
reminder was sent that day. If no reply arrives through 2026-09-01 (seven
further German business days), record `inconclusive / no response` on
2026-09-02. Do not send another reminder. Silence is never a refusal.

---

## 6. Redacted Provider Response Evidence

The raw messages, sender and recipient identities, and mailbox screenshots
remain in the author's private mailbox outside Git. These mappings record only
facts needed to update the gate.

### 6.1 DB RIS / Marketplace response

| Field | Redacted evidence |
| --- | --- |
| Organisation / department | Deutsche Bahn — RIS-API Team; API Marketplace copied on the response |
| Response date | 2026-08-11 |
| Products addressed | RIS::Connections; RIS::Stations |
| Free access | **No** — the response states that free access is not generally provided |
| Eligibility | RIS::Connections is available exclusively to DB sales partners |
| Cost | RIS::Stations may be subscribed to; monthly cost depends on use |
| Storage / retention | `UNKNOWN` — not answered |
| Research / aggregated publication | `UNKNOWN` — not answered |
| Redistribution / derived display | `UNKNOWN` — not answered |
| Training | `UNKNOWN` — not answered |
| Commercial use | `UNKNOWN` — not answered |
| Attribution | `UNKNOWN` — not answered |
| Termination / deletion / continuity | `UNKNOWN` — not answered |
| Resulting gate effect | Individual zero-budget RIS path closed. Project-level A2c, A3c and A4 remain `UNKNOWN`; rights gate remains `BLOCKED`; no provider, registration, A7, polling or collector authorised |

### 6.2 DB GTFS / GTFS-RT / RiFahrt enquiry response

| Field | Redacted evidence |
| --- | --- |
| Organisation / department | Deutsche Bahn — GTFS product function |
| Response date | 2026-08-17 |
| Enquiry scope | DB GTFS; GTFS-RT; RiFahrt |
| Open Data / zero-budget access | **No for the DB GTFS data addressed** — the response says the data cannot currently be supplied as Open Data |
| Paid access | May be considered after review by the DB data-governance body and, depending on requested data, support from DB Regio and DB Fernverkehr; price and terms are `UNKNOWN` |
| Recommended alternative | DELFI provides a GTFS dataset. This does not establish the scope or rights of DELFI-Realtime |
| RiFahrt | `UNKNOWN` — the response did not name or separately answer RiFahrt |
| Included operators / stable IDs | `UNKNOWN` — not answered in the response; public-source findings remain separate |
| Storage / retention | `UNKNOWN` — not answered |
| Research / aggregated publication | `UNKNOWN` — not answered |
| Redistribution / derived display | `UNKNOWN` — not answered |
| Training | `UNKNOWN` — not answered |
| Commercial use | `UNKNOWN` — not answered |
| Attribution / rate limits | `UNKNOWN` — not answered |
| Termination / deletion / continuity | `UNKNOWN` — not answered |
| Resulting gate effect | Current individual zero-budget DB GTFS path closed; RiFahrt and all downstream-use rights remain `UNKNOWN`. DELFI-Realtime is the only unanswered provider enquiry. A2c, A3c and A4 remain `UNKNOWN`; rights gate remains `BLOCKED`; no provider, registration, payment, A7, polling or collector authorised |
