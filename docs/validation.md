# Validation method

**Validation in progress; results will be published when the three repetitions finish.** This page describes the design, fixed before any validation round was run. It reports no results.

## The question

Does the full panel find more real errors, with fewer false ones, than one or three reviewers, at a cost per confirmed error that justifies it? The count of accepted conclusions is not a measure, because accepted does not mean true.

## A battery with a mechanical oracle

The truth is never decided by a model. A battery of questions is built over a pinned version of a real codebase. Each question has an answer that a script can check (by searching the code, counting lines, or running an existing test), and the build aborts if any planted truth does not hold.

| Kind | Count | Purpose |
|---|---|---|
| Visible trap | 4 | False premise; the line that disproves it is shown to the models |
| Hidden trap | 4 | False premise; the disproof lies outside what is shown. The right answer is "no" or "cannot tell" |
| Control | 4 | True premise; nothing to find. The correct behaviour is not inventing a problem |
| Exact figure | 3 | A precise limit or constant that can be counted |
| Tuning | 3 | Used for setup; not scored |

Every answer opens with a fixed-format verdict or figure, so the oracle can read it.

The oracle is fixed before the first validation round, and a result counts as false if anything accepted is wrong.

## Four arms

| Arm | Voices | Grouping |
|---|---|---|
| A | one reviewer | none |
| B | three complementary voices | model |
| C | the full panel | model |
| D | three voices with rule-based grouping | simple rule-based, with the model only on contradictions |

Arm D separates "less coordination" from "fewer agents".

## Paired design

Voices answer blind, so each question is answered once by the full panel and arms A, B and D are derived from the same answers. Arms are compared on identical responses, which reduces noise and uses about 10 GPU-hours instead of about 16.

## Repetitions and order

Three repetitions, giving n = 45 per arm (15 questions x 3), shuffled in blocks so that if time runs out, complete repetitions remain.

## Columns reported, always together

- Seeded errors found
- False alarms on controls
- Precision of accepted results
- Valid citations that support the argument
- GPU-seconds, tokens and latency
- Cloud usage

Quality and cost are always shown side by side, with n.

## Adoption gate

- Nothing is adopted that finds fewer seeded errors than the full panel, or raises false alarms on controls.
- At equal quality, the cheapest in GPU-seconds per error found wins.
- If one or three voices match the full panel, scaling 1 to 3 to 13 only on disagreement is proposed.
- If arm D matches B, simple rule-based grouping enters the panel.

## Limits

One codebase, 15 scored questions, three repetitions: a small battery cannot stand in for measurement on real work. Findings reported by the panel are confirmed with an independent test before being treated as defects.
