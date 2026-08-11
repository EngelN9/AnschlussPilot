# A5a Benchmark Checkpoint — 2026-08-11

```text
checkpoint_status       IN_PROGRESS
a5a_verdict             NOT_ISSUED
complete_case_denominator 0 / 10
complete_reroute_early    0 / 2
```

This is an evidence artifact, not a new specification. The governing method and
thresholds remain in
[`docs/market-and-validation.md`](../../../docs/market-and-validation.md) §2.

## Scope used

- Surfaces: DB Navigator mobile app, bahn.de web and Trainline web.
- Population: five frozen archetypes, two fresh live cases each.
- A case counts only when the same qualifying disruption is observed on all
  three surfaces in the same decision window.
- A tool closes the gap only at `6/10` all-four-criteria cases and `2/2`
  `REROUTE_EARLY` cases.

## Evidence collected so far

| Surface | Completed paired cases | Partial observations | Limitation |
| --- | ---: | ---: | --- |
| DB Navigator mobile | 0 | 0 | Awaiting user-supplied, de-identified screenshots and interaction steps |
| bahn.de web | 0 | 1 | Anonymous search result, not a saved disrupted journey |
| Trainline web | 0 | 1 attempted | Search inputs were accepted, but no result page was produced in the controlled session |

The partial case `a5a-20260811-cancel-01` was observed before departure. bahn.de
marked the Berlin–Konstanz itinerary as cancelled, showed the planned 22:16
destination arrival and exposed other search results. It did **not** show a
single-view comparison of the final outcome of continuing versus changing. The
same case was not completed on DB Navigator or Trainline, so it contributes to
neither the 10-case denominator nor any competitor numerator.

## Verdict

No A5a verdict is issued. The complete denominator is zero, and the required
mobile surface has not been observed. The current evidence cannot support any
claim that a competitor capability exists or is absent.

If the required surfaces cannot be completed during the capped live session, or
fewer than ten qualifying cases are completed, this checkpoint becomes
`inconclusive`; it does not become a negative competitive finding.

## Evidence needed to resume

For each fresh qualifying case, provide DB Navigator screenshots and a short
interaction log captured in the same decision window as the web observations.
Before sharing:

- remove names, email addresses, booking codes, ticket numbers, loyalty IDs and
  precise personal location;
- never include passwords, one-time codes or login screens;
- retain the journey times, disruption state, available action and interaction
  sequence needed to score the four criteria.

No screenshots are committed at this checkpoint because no complete paired case
exists.
