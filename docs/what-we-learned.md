# What we learned

All figures were measured on 18 November 2026 by reading existing records of the panel's runs. No new model calls were made for these measurements. The validation experiment is separate and still running (see [Validation](validation.md)).

## 1. The panel was fooled by absence claims

A claim of absence ("this function does not exist", "this is never called") is easy to believe when the models only see a narrow excerpt of code. The truth can sit outside what they were shown.

- Of **43** absence claims the panel accepted that could be decided from the code, **18 (42%) were false**. Each was labelled by checking the code.
- This is a shared bias of the panel, not a quirk of one model: several voices agree on the same tempting wrong answer.

**Fix:** search the whole codebase for the identifiers an absence claim depends on. If they appear outside the excerpt, the claim is no longer accepted.

- Before the check, 25 of the 43 accepted claims were true (**58%**). After it, 15 remained accepted, and 11 of them were true (**73%**).
- The check demoted 14 of the 18 false claims, but also 14 of the 25 true ones. That is its cost: it trades recall for precision.
- The fix was tuned on this sample, so the 73% has to be confirmed with new questions.

## 2. Grouping is mostly mechanical

Grouping similar claims was done by a 27B model, at more than a minute of A40 time per round. We compared it with a simple rule-based grouping on **83 rounds**:

- The rule reproduces **83%** of the model's accepted groups (672 of 805).
- 90% of the rule's accepted groups are also accepted by the model.
- The rule is near-instant; the model costs GPU time on every round.

What the rule cannot do is notice that two groups **contradict** each other. The model flagged disputes that the rule would have let through as accepted, so the rule cannot replace the model on its own.

**Reading:** merging is nearly mechanical; spotting contradiction is judgement. A hybrid (rule for grouping, model only for contradictions) is a candidate, adopted only if it finds as many seeded errors as the current method.

This does not say which grouping is *better*. Accepted is not true, so that question goes to the validation experiment.

## 3. The question generator wastes a lot

Almost half of the questions the panel proposes for itself are rejected by its own validator (files that do not exist, quotes that cannot be found). Whether a mechanical template could replace some of the rest is still open.

## What we do not know yet

- Whether 13 voices beat 1 or 3 on real errors found and false alarms.
- Whether adaptive scaling (1, then 3, then 13 only on disagreement) is enough.
- Any validation result. Validation in progress; results will be published when the three repetitions finish.
