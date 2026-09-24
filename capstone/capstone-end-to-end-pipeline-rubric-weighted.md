HANDS-ON RUBRIC (Weighted) — Capstone: End-to-End Automation Pipeline

Weight table used: Tier 1 excluded (45 / 35 / 20) — the capstone already
defines its own "Requirements / acceptance criteria" and "Project
architecture" sections
(`capstone/capstone-end-to-end-pipeline-cohort.md`), so planning is not
part of what's being graded here.

| Tier (Weight) | Criterion | 1 — Below Expectations | 2 — Developing | 3 — Proficient | 4 — Exemplary | Score (1-4) |
|---|---|---|---|---|---|---|
| **Technical Execution (45%)** | Application of Trained Skills | Pipeline does not use Bash scripting, cron, or `.env`-based credential handling as taught in M9/M8/M13. | Uses the correct tools (Bash, cron, `.env`) but implementation is shallow — e.g. the credential check or cron entry is missing or wrong. | Successfully implements CSV validation, `processed/`/`rejected/` routing, structured logging, cron scheduling, and `.env`-based credentials as taught in M8/M9/M13. | Goes beyond the baseline — e.g. validation beyond column count, or a richer log format than required. | — |
| **Technical Execution (45%)** | Technical Best Practices | Script is unstructured and/or hardcodes `PIPELINE_API_KEY` directly in the script. | Functional, but ignores standards — e.g. credential is in `.env` but `.gitignore` is incomplete, or the `mv`-draining idempotency pattern isn't used. | Follows the idempotent-via-`mv`-draining and cron-compatible `.env`-sourcing patterns from the capstone; `.gitignore` correctly excludes all runtime artifacts (`pipeline.log`, `incoming/`, `processed/`, `rejected/`), not just `.env`. | Production-ready — e.g. a fail-closed credential check with a clear log line, `shopt -s nullglob` handled cleanly, no edge-case gaps. | — |
| **Functional Demonstration (35%)** | Functionality & Stability | Pipeline does not run, or crashes on the happy-path test (`batch1.csv`). | Happy path partially works, but `processed/`/`rejected/` routing or logging is broken. | `batch1.csv` → `processed/`, `batch2.csv` → `rejected/`, an immediate re-run produces zero new log lines (idempotent), and the cron-triggered run (`batch3.csv`) is confirmed via a `pipeline.log` line timestamped by cron, not a manual invocation. | All of the above, plus graceful handling of an edge case beyond the required tests (e.g. an empty `incoming/`, or a zero-byte CSV). | — |
| **Presentation & Defense (20%)** | Troubleshooting & Defense | Cannot explain why the script is idempotent or how `.env` sourcing works under cron. | Struggles to explain the M8 "cron runs with a minimal environment" reasoning behind sourcing `.env` inside the script. | Confidently explains why draining `incoming/` via `mv` makes the script idempotent, and why `.env` must be sourced in-script rather than relying on an interactively-exported variable — matching the capstone's own Checkpoint self-explanation requirement. | Can discuss trade-offs beyond the capstone's current scope — e.g. what would change if this were containerized (M10) or run as a Kubernetes CronJob (M11), per the architecture doc's fuller vision. | — |

Weighted score: — (not graded — see note below)
Technical Documentation Gate: — (source: `## Documentation` section in
`capstone/capstone-end-to-end-pipeline-cohort.md` /
`-instructor.md` — grades whether real `pipeline.log` output from both
test runs, the real crontab entry and its cron-triggered log line, real
`git status` output, and a real idempotency explanation were actually
produced, not whether the section exists with placeholder text)
Result: Not graded — this is a blank scoring template with no real
trainee submission behind it. Fill in each "Score (1-4)" cell and the
Technical Documentation Gate against an actual submission to compute a
real weighted score and PASS/FAIL result. Pass requires the Technical
Documentation Gate to be Met regardless of the weighted score.
