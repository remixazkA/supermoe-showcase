# Validation and results

Two experiments have finished:

1. **Verification battery (18 November 2026).** Fifteen questions with a mechanical oracle, three repetitions. The design below was fixed before any validation round was run. [Results](#results-of-the-verification-battery-18-november-2026).
2. **Open-audit battery with planted defects (19 November 2026).** The next step, which the first battery could not measure. [Design and results](#open-audits-with-planted-defects-19-november-2026).

The data behind every chart is in [data](data).

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

## Results of the verification battery (18 November 2026)

n = 45 per arm (15 scored questions × 3 repetitions). The four tuning rounds are not counted. The oracle and the scoring script were frozen, with their hashes recorded, before the first validation round.

![Verifying claims: one reviewer vs. panels](charts/validation-2026-11-18.svg)

| Arm | Correct | Wrong | Abstained | GPU-s per question | GPU-s per correct answer | Tokens per question | Latency (s) |
|---|---|---|---|---|---|---|---|
| **A**: one reviewer | **43** | 2 | 0 | **23** | **24** | 2,954 | 23 |
| B: three voices, model grouping | 31 | 4 | 10 | 67 | 97 | 5,465 | 137 |
| C: full panel (up to 12 voices), model grouping | 32 | 8 | 5 | 420 | 590 | 41,359 | 610 |
| D: three voices, rule-based grouping | 32 | 3 | 10 | 51 | 71 | 5,465 | 122 |
| B-vote: three voices, vote on verdicts | 45 | 0 | 0 | ≈54 | n/a | n/a | n/a |
| C-vote: full panel, vote on verdicts | 45 | 0 | 0 | ≈360 | n/a | n/a | n/a |

- **Scoring.** The table uses the registered scoring rule.
  - That rule also marked an answer wrong when a "trap phrase" appeared among the accepted claims. The phrase was matched by a pattern, which fired in 6 rounds.
  - Checked by hand, 5 of the 6 were true statements the pattern caught by mistake.
  - Counting verdict labels only: A 45 correct, B 35, C 36 with 4 wrong, D 35, and the vote arms unchanged.
- **Vote arms.** B-vote and C-vote were added as exploratory arms and were not pre-registered. Their GPU-s exclude the grouping turn.
- **Controls.** No arm raised a false alarm on the 12 control answers.
- **Cloud usage** was tracked but is not published: spend figures stay private under the [disclosure policy](public-disclosure-policy.md).
- **Missing voice.** The GPT voice had no quota during the run, so it took part in no round.

**What the adoption gate says**

- **One reviewer beats the full panel** (43 vs. 32 correct), with no more false alarms on controls. It costs about 25 times less per correct answer.
- **Rule-based grouping matches or beats model grouping** (D vs. B: 32 vs. 31 correct, 3 vs. 4 wrong) and is cheaper (51 vs. 67 GPU-s).

**Why the panel lost (checked round by round)**

- Model grouping sometimes merged a dissenting verdict into a large group with the opposite verdict.
- Several weaker voices agreeing could outvote a correct minority.
- The accepted-group rule needs a located citation, so it can never accept "cannot tell", which is the right answer to a hidden trap.

Voting directly on each voice's verdict, without grouping, avoided all three problems.

**Adopted on 19 November 2026, for verification questions**

- One reviewer, which escalates to a vote of three voices only when it is in doubt.
- A vote on each voice's verdict in place of the accepted-group rule.
- Rule-based grouping by default, with groups never mixing opposite verdicts.

**What this battery could not measure**

- **Open audits,** where nobody knows what to look for and the diversity of voices might help.
- **Differences between the best arms.** One reviewer was already at the ceiling, so the battery could not separate it from the votes.

## Open audits with planted defects (19 November 2026)

### Design

- **Tasks.** There are 18 tasks over a pinned version of the same real codebase. Each task shows 40 to 70 lines of code and asks, with no hint, whether they contain any defect: logic, boundaries, conditions, defaults, error handling or security. Nobody tells the voices whether there is one.
  - **Seeded:** 14 tasks have exactly one line changed on purpose (a mutation), each in a different function, across 13 categories.
  - **Controls:** 4 tasks show untouched code.
- **Repetitions.** Two, in a shuffled order with a recorded seed: 36 rounds in all, 28 seeded and 8 control.
- **How mutations are kept apart.** The mutations live as commits in a throwaway repository, so the reviewed codebase is never modified. Before use, each one is checked mechanically: the original line matches, the file still parses, and the change is exactly one line.
- **What counts as "found".** A defect claim whose cited line is verified against the mutated file, falls within one line of the planted change, and names the mutation, using keywords registered before the battery.
  - A two-round tracer run fixed this criterion before the battery started.
  - The tracer showed that a wider window credited defects on the neighbouring line.
- **Controls are adjudicated, not assumed clean.** Untouched code can contain real defects.
  - A separate model, given no context and no arm labels, reviewed every defect claim that did not match a planted change.
  - Those it found real were confirmed by hand.
  - A control round counts as a false alarm only if none of the arm's claims in that round turned out real.
- **Paired arms.** All arms are derived from the same blind answers, so they are compared on identical responses. Each round had 8 to 11 local and cloud voices; the GPT voice again had no quota.

| Arm | Voices | Decision |
|---|---|---|
| A | one reviewer | its own claims |
| 1→3 policy | one reviewer, three on doubt | the policy adopted for verification |
| B | three voices | grouping plus the accepted-group rule |
| B-vote | the same three | majority vote on verdicts, equal weights |
| C | the full panel | grouping plus the accepted-group rule |
| C-vote | the full panel | majority vote on verdicts, equal weights |

### Results

![Seeded errors found vs. GPU time per round](charts/seeded-found-vs-gpu.svg)

| Arm | Seeded errors found (of 28) | False alarms on controls (of 8) | Controls where it also flagged a real, unseeded defect | GPU-s per round |
|---|---|---|---|---|
| A: one reviewer | 20 | **0** | 1 | **56** |
| 1→3 policy | 20 | 1 | 0 | 56 |
| B: three voices, accepted-group rule | 13 | 1 | 0 | 71 |
| **B-vote: three voices, vote** | **24** | 1 (+1 dubious) | 1 | 71 |
| C: full panel, accepted-group rule | 19 | 3 | 0 | 502 |
| C-vote: full panel, vote | 25 | 5 | 2 | 502 |

GPU-s are the local models' compute time as reported by the inference server, summed per round. They exclude loading and waiting for a turn. Open audits produce longer answers than verification questions, so one reviewer costs 56 GPU-s per round here, against 23 per question in verification.

The 1→3 policy differs from A only in what it scores (the reviewer's verdict rather than all of its claims), since it never escalated.

![False alarms on untouched code](charts/seeded-false-alarms.svg)

![Which planted defects each arm finds](charts/seeded-by-mutation.svg)

### Reading

1. **In open audits, diversity helps, but only with a vote.**
   - Three voting voices find 4 more planted defects than one reviewer (24 vs. 20), for 15 more GPU-s per round.
   - Paired round by round, the vote wins 4 rounds and loses none.
   - With the accepted-group rule, the same three voices find 7 fewer (13).
   - This agrees with the verification battery: coordination costs quality, not the number of voices.
2. **More voices, more noise.** The full panel voting finds one more planted defect than three voices (25 vs. 24). It raises 5 false alarms in 8 controls, against 1, and uses about seven times the GPU time.
3. **The verification policy does not discover.** In open audits the single reviewer never declared doubt, so the 1→3 policy never escalated and found what one reviewer found. It is built to check a claim, not to search for defects.
4. **Hard and easy defects.**
   - Defects readable in one line next to their own comment were found by every arm.
   - A removed `await` was never found, by any arm, in either repetition.
   - Swapped arguments were found only by the votes, once each.
   - Swapped min/max and a removed sanitization step were found mostly by the votes: only one or two voices saw them.
5. **Real defects nobody had planted.** The voices also found 4 previously unknown real defects in the untouched code, of low to medium severity. They have been logged for the codebase's maintainers; their details stay private.

### Proposal (not adopted yet)

- **Open audits:** replace the accepted-group rule with a vote of three voices on each voice's verdict. When the vote says "defect", the claims of the voices that voted for it go to the single-reviewer verification adopted on 19 November. That step filters the false alarms the vote lets through.
- **The full panel:** only when someone asks for maximum coverage. It gains one planted defect in 28 at seven times the GPU time, with more false alarms.

### Limits

- **Small n.** There are 28 seeded rounds (14 mutations × 2) and 8 controls. A difference of 4 planted defects (24 vs. 20) is not conclusive at this size.
  - A second battery, with new mutations not used for any tuning, is needed before adoption.
- **Wording.** The "found" criterion can miss a finding worded differently.
  - In a partial check, every claim the adjudication judged real at a planted line came from a round already counted as found.
  - The non-accepted claims of the other voices were not adjudicated.
- **Not tested on these rounds:**
  - model grouping vs. rule-based grouping;
  - a weighted vote instead of equal weights;
  - the GPT voice.
- **Sampling.** One version of each model, at its production temperature, with no fixed seed.
