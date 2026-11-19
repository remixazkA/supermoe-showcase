# What we learned

Sections 1 to 3 were measured on 18 November 2026 by reading existing records of the panel's runs, with no new model calls. Sections 4 to 6 come from the two validation experiments of 18 and 19 November 2026 (see [Validation and results](validation.md)).

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

This does not say which grouping is *better*. Accepted is not true, so that question went to the validation experiment.

**Answered on 18 November:** rule-based grouping matched or beat model grouping on verification questions: 32 vs. 31 correct, 3 vs. 4 wrong, 51 vs. 67 GPU-s, n = 45. It has been the default since 19 November, and groups no longer mix opposite verdicts.

## 3. The question generator wastes a lot

Almost half of the questions the panel proposes for itself are rejected by its own validator (files that do not exist, quotes that cannot be found). Whether a mechanical template could replace some of the rest is still open.

## 4. To check a claim, one reviewer is enough

On 45 verification questions with a mechanical oracle, one reviewer (a Qwen 27B model on the A40) got 43 right and 2 wrong, at 23 GPU-s per question. The full panel got 32 right and 8 wrong, at 420 GPU-s.

What cost the panel quality was the coordination layer, not the voices:

- grouping merged opposite verdicts;
- weak voices that agreed outvoted a correct minority;
- the accepted-group rule cannot accept "cannot tell".

The same voices voting directly on their verdicts got 45 of 45.

**Adopted:** for verification, one reviewer that escalates to a three-voice vote only when it is in doubt.

## 5. To search for defects, three voices that vote find more

In open audits with planted defects, the reviewer does not know what it is looking for.

| Arm | Planted defects found (of 28) | False alarms (of 8 controls) | GPU-s per round |
|---|---|---|---|
| One reviewer | 20 | 0 | 56 |
| Three voices, vote | 24 | 1 (+1 dubious) | 71 |
| Three voices, accepted-group rule | 13 | 1 | 71 |

So diversity does help here, but only when the voices vote. One reviewer is not enough to search, and the escalation policy built for verification never triggered: the reviewer was never in doubt.

## 6. More voices, more noise

The full panel voting found 25 of 28: one more than three voices. It raised 5 false alarms in 8 controls, against 1, and used about seven times the GPU time.

On this battery, going from three voices to the full panel added more noise than findings. The full panel stays available for maximum coverage on request, not as the default.

Along the way the voices also found 4 previously unknown real defects in code nobody had seeded. Their details stay private.

## What we do not know yet

- **Whether the three-voice vote holds** on a second battery with new planted defects that were not used for any tuning: n = 28 is small.
- **Whether the candidate models** now in testing lower the panel's false alarms without losing planted defects. See [Models in testing](../README.md#models-in-testing).
- **Whether a weighted vote beats equal weights** in open audits.
- **Defects the panel misses entirely.** A removed `await` was never found by any arm.
