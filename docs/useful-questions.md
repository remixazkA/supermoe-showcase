# Questions that proved useful

A SuperMoE round is one question about a short excerpt of a pinned version of a real codebase. Several open-weight models, most of them local and one in the cloud, answer it blind, without seeing each other. Every claim they make has to carry evidence that a program can check. The shape of the question decides most of what comes out: some shapes find real problems at low cost, others mostly produce confident noise.

This page describes the question types by their **shape**, not their wording. The exact prompts, the code under review, the defects found in it and the decision rules stay private (see the [public disclosure policy](public-disclosure-policy.md)). Every result comes from a finished measurement and gives its date and sample size. Some come from re-reading the panel's own run records rather than from a new experiment, and say so.

- **Verification battery, 18 November 2026:** 15 questions with a mechanical oracle, three repetitions, n = 45 per arm.
- **Open audits with planted defects, 19 November 2026:** 14 one-line defects planted in a copy of the codebase plus 4 untouched controls, two repetitions, n = 28 planted rounds and 8 control rounds.

These are small samples on one codebase. They point the way, but they are not proof. A second battery with new planted defects is pending. Method and limits are in [Validation and results](validation.md).

## 1. Check one claim against an oracle

**Form.** A claim about what some code does is put to the reviewer, who must say whether it holds. The answer opens with a fixed, machine-readable verdict.

**Why it works.** The truth is fixed before any model runs. A script checks it by searching the code, counting or running a check. No model grades another model. The question is narrow, so a reviewer cannot drift into unrelated topics.

**What it exposes.** Plausible but false statements about what code does, and reviewers who agree with whatever they are told.

**Measured (18 Nov 2026, n = 45).** Under the registered scoring rule, a single reviewer gave 43 correct answers and 2 wrong (45 and 0 after a manual check of the scoring), at 23 GPU-seconds per question. The full panel of up to twelve voices gave 32 correct, 8 wrong and 5 abstentions, at 420 GPU-seconds. Voting on the opening verdicts of three voices gave 45 correct. **To check a claim, one reviewer is enough.**

## 2. Ask for an exact figure

**Form.** "What is the exact limit (or count, or constant) that applies here?" The answer opens with the figure itself.

**Why it works.** A number is either right or wrong, with nothing to interpret. The oracle reads it straight from the code.

**What it exposes.** Figures that are remembered instead of read: values from another branch, from documentation, or from a similar-looking constant.

**Measured (18 Nov 2026, 9 answers: 3 questions × 3 repetitions).** A single reviewer got 9 of 9 right, three voices 9 of 9, and the full panel 8 of 9.

## 3. Visible false-premise traps

**Form.** The question rests on an assumption that the excerpt shown plainly contradicts, for example "Why does this function retry three times?" when it does not retry. The correct answer rejects the premise.

**Why it works.** The question tests whether a reviewer reads the code or answers the question as asked. Agreeable models fall for it; careful ones push back.

**What it exposes.** Confirmation bias, and reviewers who follow the framing of the question.

**Measured (18 Nov 2026, 12 trap answers per arm).** A single reviewer rejected the premise in 12 of 12 rounds counting verdict labels only (11 of 12 under the registered scoring rule, whose one miss was a scoring artefact). The full panel, scored by its own agreement rule, got 10 right and 2 wrong counting verdict labels only (7 and 5 under the registered rule, which also flagged three correct answers). In both wrong rounds, grouping merged one dissenting verdict into a large group with the opposite verdict.

## 4. Hidden traps: "cannot be decided" is a valid answer

**Form.** The question cannot be answered with certainty from what is shown, because the deciding code is elsewhere. Either "the premise is wrong" or "cannot be decided" is correct; confidently confirming it is not.

**Why it works.** It rewards a reviewer for knowing what it does not know, instead of guessing with confidence.

**What it exposes.** Guessing, and answers that sound confident but have no evidence behind them.

**Measured (18 Nov 2026, 12 hidden-trap answers per arm).** A single reviewer answered correctly in 12 of 12 (11 under the registered scoring rule, which wrongly flagged one correct "cannot be decided"). Voting on opening verdicts got 12 of 12. The full panel, scored by agreement among its grouped claims, got 6 right, 1 wrong and abstained 5 times (5 right under the registered rule). The reason is structural: an agreement rule that needs cited evidence can never accept "cannot be decided". **The opening verdict has to allow abstention explicitly.**

## 5. Open audit with a forced first-line verdict

**Form.** An open question about a short excerpt, with no hint that a defect exists. The answer opens with a fixed, machine-readable verdict, so every answer can be counted.

**Why it works.** Saying nothing about whether a defect exists removes the hint that every question hides a bug. The fixed first line makes every answer countable, so verdicts can be voted on without any model grouping the answers. Different voices notice different things, and the vote adds them up.

**What it exposes.** Subtle one-line defects that only one or two voices notice: minimum and maximum exchanged, a sanitising step removed, two arguments swapped. A removed `await` was never found by any arm.

**Measured (19 Nov 2026, n = 28 planted rounds).**

| Arm | Planted defects found |
|---|---|
| One reviewer | 20 of 28 |
| Three voices voting on the verdict | 24 of 28 |
| Every voice voting on the verdict | 25 of 28 |
| Three voices, scored by agreement among grouped claims | 13 of 28 |

Compared round by round, three voices voting beat one reviewer in 4 rounds and lost none. This has not been adopted yet: it is waiting for the second battery.

## 6. Control rounds: the same question on untouched code

**Form.** The open audit of type 5, asked about code that was not modified, or a verification question whose premise is true. The right answer is "nothing to find" or "the claim is right".

**Why it works.** A method that finds defects is worth little if it also invents them. Control rounds put a number on that. Every defect claimed in a control is judged blind and then confirmed by hand. A claim only counts as a false alarm if it turns out not to be real.

**What it exposes.** Invented problems, and the noise that grows as voices are added.

**Measured.**
- **Open-audit controls (19 Nov 2026, n = 8):** rounds with a false alarm: 0 for one reviewer, 1 (plus 1 judged dubious) for three voices voting, 5 for every voice voting. Going from three voices to all of them found 1 more planted defect in 28 and cost about seven times the GPU time.
- **Verification controls (18 Nov 2026, n = 12):** no false alarms in any arm.
- **Side effect:** the audit rounds, controls included, turned up four real defects that nobody had planted. Their details are private.

## 7. Every claim carries a citation that a program checks

**Form.** Every claim must name a file, a line and a short quoted fragment. Before any vote, a program checks that the file exists, that the line exists and that the quote is really there. A claim without a valid citation counts as no evidence.

**Why it works.** It removes made-up evidence at no model cost, before any voice is weighed. It also turns "found the defect" into something a script can score: a verified citation at the planted line.

**What it exposes.** Quotes that are not in the file, the right quote on the wrong line, and claims that name a nearby line instead of the faulty one.

**Measured (19 Nov 2026, 956 citations in 36 rounds).** 92.1% of the citations checked out. The other 76 would have passed as evidence without the check:

| Problem | Citations |
|---|---|
| Quote not in the file | 43 |
| Wrong line | 8 |
| Quote missing or too short to check | 25 |

## 8. Ask the same question twice

**Form.** Each question is asked again in a later repetition, blind, in a different order. Nothing in the question changes.

**Why it works.** One run cannot tell "this reviewer cannot see the defect" apart from "it did not see it this time". Two runs can.

**What it exposes.** Findings that come down to luck, and arms that look better than they are on a single pass.

**Measured (19 Nov 2026, 14 planted defects × 2 repetitions).**

| Arm | Found in both runs | Found in one run | Found in neither |
|---|---|---|---|
| One reviewer | 9 | 2 | 3 |
| Three voices voting | 11 | 2 | 1 |

A single pass of one reviewer would have missed or overstated about one defect in seven.

## Not measured yet

- **Behaviour and cost questions** ("what does this do when…", "how many calls does this make…"). The panel asks many of them, but nobody has measured how many of their findings were real.
- **Disputed rounds.** As of 19 November 2026, 219 of 532 summarised rounds (41%, all question types) ended with at least one group still in open dispute. Whether a follow-up, such as running the tests, settled them has not been measured.

## Questions that looked good and did not work

These three were measured by re-reading the panel's run records, with no new model calls.

### Absence claims judged from an excerpt

**Form.** "This value is never checked", "nothing calls this", "this case is not handled", judged only from the lines shown.

**Why it failed.** The truth sits outside what the models were shown, so the voices share the same blind spot and agree on the same wrong answer. On 18 November 2026, 18 of 43 absence claims the panel had accepted turned out to be false (42%). A mechanical check against the whole codebase raised the accepted share that was true from 58% to 73%. It also rejected 14 of 25 true claims, and it was tuned on that same sample. Absence questions now need the whole file or a mechanical search.

### "Review this change and list improvements", scored by agreement

**Form.** A change or module with an open request for problems or improvements, scored by how many claims enough voices agree on.

**Why it failed.** Agreement is not truth. On 18 November 2026, 82 of 88 claims from such rounds were false or could not be decided when checked against the code. Several accepted claims rested on a single voice, and in one round the grouping step merged nearly every claim into one group and made the result look unanimous. These rounds were replaced by types 5 and 6.

### Follow-up questions that the panel writes for itself

**Form.** After each round, a voice proposes the next questions, aimed at what is disputed or unverified.

**Why it failed.** Almost half are unusable. On 18 November 2026, the panel's own validator rejected 252 of 550 proposed questions (46%), for reasons such as citing files or quotes that do not exist. Nobody has measured whether the rest are worth their cost. The validation experiments therefore use fixed, hand-built questions.
