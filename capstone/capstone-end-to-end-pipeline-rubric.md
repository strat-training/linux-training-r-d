HANDS-ON RUBRIC — Capstone: End-to-End Automation Pipeline

| Criterion | Test type | Met? | How to verify |
|---|---|---|---|
| The pipeline script validates incoming CSVs and routes them to `processed/` or `rejected/` based on column count. | Positive | — | `batch1.csv` → `processed/`, `batch2.csv` → `rejected/`. |
| Running the script twice in a row with no new files produces no errors and no duplicate log entries (idempotent). | Positive | — | The second run's `pipeline.log` has zero new lines. |
| The pipeline reads its credential from an environment variable (via a sourced `.env`), never hardcoded. | Positive | — | `grep PIPELINE_API_KEY pipeline.sh` finds no literal key value, only the variable name. |
| `.gitignore` excludes `.env`, `pipeline.log`, `incoming/`, `processed/`, and `rejected/`, confirmed with `git status`. | Positive | — | `git status` after `git init` shows only `pipeline.sh`, `.env.example`, and `.gitignore` as trackable. |
| The pipeline was scheduled with cron and, per `pipeline.log`, ran automatically on schedule — not only when triggered by hand. | Positive | — | A `VALID batch3.csv` line timestamped by the cron run, not by a manual invocation. |
| The pipeline was run against a deliberately malformed file, confirming the failure path (validation → `rejected/` → logged) works as intended. | Negative | — | `batch2.csv` in `rejected/`, matching an `INVALID` log line. |

Negative-path coverage: covered (1 negative-path criterion present, sourced from the capstone's own Checkpoint).
Result: Not graded — this is a blank scoring template generated as a test run of `/create-rubric`, with no real trainee submission behind it. Fill in "Met?" per criterion against an actual submission to produce a real PASS/FAIL result.
Pass rule (for when it is graded): every Positive-path criterion Met, and the one Negative-path criterion Met.
