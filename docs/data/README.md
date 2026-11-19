# Data behind the charts

Plain CSV, one row per arm or per mutation. The charts in [../charts](../charts) are drawn from these figures, and the full tables are in [Validation and results](../validation.md).

| File | What it holds |
|---|---|
| `validation-2026-11-18.csv` | Verification battery, 18 November 2026. One row per arm with n = 45: correct, wrong and abstained under the registered scoring rule, then the same counting verdict labels only, and GPU-seconds per question and per correct answer. `gpu_s_approximate` marks the exploratory vote arms, whose GPU-s exclude the grouping turn. |
| `seeded-battery-2026-11-19-arms.csv` | Open-audit battery with planted defects, 19 November 2026. One row per arm: seeded errors found out of 28 rounds, and false alarms on the 8 control rounds. Also adjudicated as dubious, control rounds where the arm also flagged a real defect nobody had planted, and mean GPU-seconds per round. |
| `seeded-battery-2026-11-19-by-mutation.csv` | Same battery. One row per mutation (one changed line each), with its category and, per arm, the repetitions (of 2) in which that arm found it. |

GPU-seconds are the local models' compute time as reported by the inference server: prompt processing plus generation. They exclude model loading and waiting for a turn. Cloud voices are not counted in GPU-seconds, and their spend is not published.

Mutations are identified by category only. The files, functions and lines of the reviewed code are not published.
