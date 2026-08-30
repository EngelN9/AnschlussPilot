# Synthetic UX Preflight (Phase 0.5-S)

Optional, deferred contract for using simulated users to find communication
risks in frozen AnschlussPilot recommendation presentations.

> **Update trigger:** revise when Phase 0.5-S eligibility changes; when a
> MatrAIx version, licence, provider, budget or data-handling decision is made;
> when the scenario, cohort, response or manifest contract changes; or when a
> synthetic run is pre-registered or completed. Checked at deliverable
> completion (`AGENTS.md` §4).

> [!IMPORTANT]
> **Status: `DEFERRED / NOT_IMPLEMENTED`.** No harness, persona dataset, model
> integration, scenario cohort or synthetic result exists in this repository.

---

## 1. Purpose and timing

Phase 0.5-S may use simulated users to expose likely failures in recommendation
wording, information hierarchy, uncertainty communication and structured task
comprehension before revised materials are shown to real Phase 0.5 participants.
It is not railway evaluation and it is not passenger research.

Implementation is prohibited until all of these are true:

1. the Phase 0 report exists and its technical gates permit product validation;
2. no applicable S-condition requires stopping or repivoting away from this UX;
3. the author approves an exact MatrAIx commit, applicable code and data terms,
   model provider, cost ceiling and data-handling plan;
4. the frozen recommendation-presentation contract exists without requiring
   MatrAIx to call the decision engine; and
5. a separate implementation deliverable records its tests and rollback path.

The current public MatrAIx repository describes Python 3.12, `uv`, Docker and
model API keys for evaluation runs. Those are observations about that repository,
not approved AnschlussPilot dependencies. No package, dataset or service is
selected by this document:
[MatrAIx official repository](https://github.com/MatrAIx-ai/MatrAIx-Persona-8B),
accessed 2026-08-13.

---

## 2. Hard evidence boundary

> **Synthetic-user evidence may identify communication and usability risks, but
> cannot satisfy technical, behavioral, commercial, safety, rights, legal, or
> promotion gates requiring railway evidence, real people, provider evidence, or
> human review.**

Every synthetic report carries this exact banner:

```text
SYNTHETIC PERSONA EVIDENCE — NOT REAL PASSENGER BEHAVIOR
```

Synthetic output cannot establish:

- A1–A7, S1–S12, B1–B4 or SB1–SB4;
- recommendation correctness, transfer feasibility or connection protection;
- ticket validity, binding, entitlement or legal executability;
- real comprehension, trust, willingness to act, friction or willingness to pay;
- market size, German-passenger representativeness or production safety.

A favourable synthetic result does not promote a variant. An adverse result is a
triage signal for human review, not a project stop. The real participant protocol
in [`market-and-validation.md`](market-and-validation.md) §5 remains mandatory.

---

## 3. Isolation contract

The only permitted future flow is:

```text
human-authored frozen recommendation scenario
  → isolated MatrAIx task
  → structured synthetic response
  → deterministic scorer
  → human review
```

The following flows are forbidden:

```text
MatrAIx → journey recommendation
MatrAIx → route or alternative selection
MatrAIx → transfer, timetable or realtime fact
MatrAIx → ticket, entitlement or executability decision
MatrAIx → production confidence score
MatrAIx → A-, B-, S- or SB-gate verdict
```

A future harness belongs under `evaluation/matraix/`, has its own evaluation-only
environment and lock data, and is absent from every production import or runtime
dependency graph. The decision engine may later export an approved serialized
fixture; the harness may never import or invoke decision logic.

---

## 4. Scenario semantics

Risk and action remain separate (`AGENTS.md` I3):

- `ATTENTION` and `UNKNOWN` are risk states, never actions;
- actions use only the canonical vocabulary in
  [`decision-model.md`](decision-model.md) §4;
- `CONTINUE_CURRENT_PLAN` remains an explicit candidate;
- no low/medium/high recommendation-confidence field is invented;
- uncertainty is represented through categorical evidence freshness
  (`LIVE`, `STALE`, `UNAVAILABLE`), explicit missing information and, when
  warranted, `NO_RELIABLE_RECOMMENDATION`;
- `UNKNOWN` ticket binding preserves `unknown` executability.

The first frozen set must include counterexamples such as
`HIGH_RISK + CONTINUE_CURRENT_PLAN` and `ATTENTION + REROUTE_EARLY`. This prevents
the presentation or scorer from learning the forbidden shortcut
`HIGH_RISK ⇒ REROUTE`.

A future versioned scenario record contains at least:

| Field | Contract |
| --- | --- |
| `scenario_id`, `scenario_version` | Stable identity and immutable version |
| `risk_state`, `recommended_action` | Separate canonical categorical values |
| `reason_codes` | Human-authored facts the explanation must convey |
| `freshness`, `missing_information_codes` | Categorical evidence limitations |
| `ticket_constraint`, `executability` | Preserve `UNKNOWN`; never infer entitlement |
| `decision_window` | Presentation context, not a fabricated precise forecast |
| `displayed_outcomes` | Human-approved scenario values, labelled estimates |
| `variant_id`, `presentation` | Frozen wording or static presentation under test |
| `source_class` | `fully_synthetic` or an explicitly approved retained fixture |

Only fully synthetic scenarios or scenario artifacts whose retention and use are
explicitly permitted may enter the harness. Raw provider payloads are forbidden.

---

## 5. Persona and response contracts

The initial cohort is locally authored and purposefully sampled: 20 versioned
persona fixtures, not Persona 1M and not a population sample. Permitted dimensions
include German proficiency, rail experience, station familiarity, technical
confidence, risk tolerance, time sensitivity, luggage burden, travelling party,
mobility constraint, disruption stress and decision-window context.

These fields are scenario lenses, not claims about German passengers. Persona IDs
and cohort versions are stable; no real person's profile or contact data enters
the cohort.

For every persona × scenario × variant cell, the structured response asks what
the presentation recommends, why, how uncertainty is understood, what action the
simulated user says they would take, what is missing, whether `UNKNOWN` predicts
failure, and whether an estimated arrival is a guarantee.

The deterministic scorer, not the simulated user or another unconstrained model,
derives:

- `recommended_action_understood`;
- `reason_understood`;
- `uncertainty_understood`;
- `wrong_action_selected`;
- `unknown_misinterpreted`;
- `false_certainty_expressed`;
- `requested_more_information`;
- `abandoned_or_indecisive`;
- `interaction_count`, or `NOT_APPLICABLE` for a static Survey task.

`wrong_action_selected` is a UX-risk flag inside a synthetic task, not proof that
a real passenger would choose incorrectly. Reports show raw numerators and
denominators plus subgroup patterns; they never collapse the fields into one UX
score or attach population confidence intervals.

---

## 6. Experiment ladder and human gate

No paid or external model run occurs without explicit author approval.

| Stage | Frozen size | Purpose | Exit |
| --- | ---: | --- | --- |
| Calibration | 4 scenarios × 6 sentinel personas × 2 paired variants = **48 cells** | Check persona behaviour, prompt meaning and scorer validity | Human reviews every response; stop this lane if outputs are implausible or scorer errors are material |
| Initial pilot | 10 scenarios × 20 personas × 2 paired variants = **400 cells** | Generate descriptive UX-risk hypotheses and compare paired wording failures | Only after calibration and cost approval; still no product or evidence-gate verdict |

A third variant, more personas, more scenarios or repeated stochastic trials
requires a new pre-registration before results are observed. Passing calibration
only permits the pilot; it does not validate MatrAIx against real passengers.

Human review checks at least:

- whether the persona response is internally coherent rather than a caricature;
- whether scoring follows the human-authored expected action and reason codes;
- whether a mismatch reflects communication, willingness, prompt artefact or
  scorer failure;
- whether subgroup language is descriptive and avoids population claims;
- whether any proposed material revision must be tested by the real pilot.

---

## 7. Reproducibility, cost and data handling

A future run manifest records:

- exact MatrAIx repository URL and commit;
- scenario, cohort, prompt, task, scorer and report versions;
- persona IDs and any supported deterministic seeds;
- model provider, model identifier and relevant generation settings;
- input and output SHA-256 values;
- attempted, completed, failed and retried cell counts;
- run timestamps, environment metadata and approved cost ceiling;
- actual cost when available, without committing credentials or billing data.

Artifact reproducibility and model determinism are different. A manifest can
prove which inputs and outputs were used even when a provider cannot reproduce
the same text. Reports disclose that limitation.

Persona dataset terms, model-provider retention and training terms, and any
external telemetry are separate rights gates. The public availability or code
licence of one component does not approve any other component (`AGENTS.md` I15).

---

## 8. Future implementation acceptance

The eventual harness is incomplete unless fresh checks demonstrate:

- scenario schema and canonical enum validation;
- rejection of `ATTENTION`/`UNKNOWN` as actions and unsupported scenarios;
- cohort identity and version stability;
- deterministic scoring and fixture handling where possible;
- manifest and input/output hash generation;
- mandatory report banner and raw numerator/denominator output;
- no production decision-engine dependency, import or mutation;
- no provider payload, secret, personal data or unapproved dataset retention;
- report generation for failure, partial and completed runs.

Until a later deliverable satisfies those checks, this document records intent
only and must not be cited as a MatrAIx capability.
