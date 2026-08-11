# A5a Benchmark Checkpoint — 2026-08-11

```text
checkpoint_status                    IN_PROGRESS
a5a_verdict                          NOT_ISSUED
db_navigator_complete_cases          0 / 10
bahn_de_complete_cases               0 / 10
trainline_complete_cases             0 / 10
db_navigator_reroute_early           0 / 2
bahn_de_reroute_early                0 / 2
trainline_reroute_early              0 / 2
```

This is an evidence artifact, not a new specification. The governing method and
thresholds remain in
[`docs/market-and-validation.md`](../../../docs/market-and-validation.md) §2.

## Scope used

- Surfaces: DB Navigator mobile app, bahn.de web and Trainline web.
- Population per surface: five frozen archetypes, two fresh live cases each.
- Samples are compared across tools by archetype, not by requiring identical
  disruptions. Observing the same case in the same decision window is preferred
  when feasible because it reduces context differences, but it is not an
  eligibility condition.
- A tool closes the gap only at `6/10` all-four-criteria cases and `2/2`
  `REROUTE_EARLY` cases.

## Evidence collected so far

| Surface | Completed cases | Partial observations | Limitation |
| --- | ---: | ---: | --- |
| DB Navigator mobile | 0 | 0 | Awaiting user-supplied, de-identified screenshots and interaction steps |
| bahn.de web | 0 | 2 | Anonymous search results with no retained screenshots; not independently auditable |
| Trainline web | 0 | 2 attempted | Search inputs were accepted, but no result page was produced in either controlled session |

The partial case `a5a-20260811-cancel-01` was observed before departure. bahn.de
marked the Berlin–Konstanz itinerary as cancelled, showed the planned 22:16
destination arrival and exposed other search results. It did **not** show a
single-view comparison of the final outcome of continuing versus changing. The
observation has no retained screenshot (`evidence_path=NOT_CAPTURED`), so it
remains partial and contributes to neither bahn.de's denominator nor numerator.
The failed Trainline attempt and missing DB Navigator observation are recorded
separately; cross-tool pairing is not required for future qualifying cases.

The second partial case, `a5a-20260811-cancel-02`, completed the two planned
examples for the cancellation archetype **on bahn.de only**. Before its 14:20
departure, ICE 224 was shown as starting instead at Frankfurt Airport; its
München and Mannheim stops were cancelled. The cancelled itinerary retained a
planned 17:34 destination arrival, while a separate alternative search result
arrived at 17:48. The surface did not label these as continue/change branches or
compare them. Trainline accepted the matching route inputs, but its enabled
search action produced no result, navigation or new controlled tab. Neither
attempt is evidence that Trainline lacks the feature. The bahn.de observation
also remains partial because no screenshot was retained; it is not excluded
merely because DB Navigator was not observed.

## Verdict

No A5a verdict is issued. Every surface has a complete denominator of `0/10`,
and the mobile surface has not been observed. The current evidence cannot
support any claim that a competitor capability exists or is absent.

If the required surfaces cannot be completed during the capped live session, or
fewer than ten qualifying cases are completed, this checkpoint becomes
`inconclusive`; it does not become a negative competitive finding.

## Evidence needed to resume

For each fresh qualifying DB Navigator case, provide screenshots and a short
interaction log while the action remains available. Matching web observations
should be captured in the same decision window when feasible, but an otherwise
qualifying mobile case does not become invalid when an identical web observation
cannot be completed. Before sharing:

- remove names, email addresses, booking codes, ticket numbers, loyalty IDs and
  precise personal location;
- never include passwords, one-time codes or login screens;
- retain the journey times, disruption state, available action and interaction
  sequence needed to score the four criteria.

No screenshots are committed at this checkpoint because no independently
auditable complete case exists.
