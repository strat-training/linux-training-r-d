# Capstone: End-to-End Automation Pipeline — Instructor Version

> **INSTRUCTOR VERSION** — contains the full solution script, real log
> output, and filled Checkpoint answers. Do not hand this to a trainee.
> The trainee-facing spec is
> [`capstone-end-to-end-pipeline-cohort.md`](./capstone-end-to-end-pipeline-cohort.md)
> in this same folder.

The capstone is the course's systems-integration deliverable — it wires
together shell scripting (M9), scheduling (M8), and secrets hygiene
(M13) into one running, idempotent pipeline that processes a batch of
CSV files automatically and on a schedule, exactly as it would run in
production.

## Getting started

### Prerequisites

- A Linux environment (WSL2, a VM, or macOS-in-a-Linux-VM per M0).
- Bash and cron available (both ship with the standard course
  environment set up in M0).

### Local setup

```bash
mkdir -p ~/linux-course/capstone/incoming
mkdir -p ~/linux-course/capstone/processed
mkdir -p ~/linux-course/capstone/rejected
cd ~/linux-course/capstone
```

```bash
nano ~/linux-course/capstone/pipeline.sh
```

Script contents:

```bash
#!/bin/bash
set -e

BASE_DIR="$(cd "$(dirname "$0")" && pwd)"
INCOMING_DIR="$BASE_DIR/incoming"
PROCESSED_DIR="$BASE_DIR/processed"
REJECTED_DIR="$BASE_DIR/rejected"
LOG_FILE="$BASE_DIR/pipeline.log"
EXPECTED_COLUMNS=3

if [ -f "$BASE_DIR/.env" ]; then
  set -a
  source "$BASE_DIR/.env"
  set +a
fi

if [ -z "$PIPELINE_API_KEY" ]; then
  echo "$(date '+%Y-%m-%d %H:%M:%S') ERROR PIPELINE_API_KEY is not set" >> "$LOG_FILE"
  exit 1
fi

shopt -s nullglob
for file in "$INCOMING_DIR"/*.csv; do
  filename=$(basename "$file")
  actual_columns=$(head -n 1 "$file" | awk -F, '{print NF}')

  if [ "$actual_columns" -eq "$EXPECTED_COLUMNS" ]; then
    mv "$file" "$PROCESSED_DIR/"
    echo "$(date '+%Y-%m-%d %H:%M:%S') VALID $filename moved to processed/" >> "$LOG_FILE"
  else
    mv "$file" "$REJECTED_DIR/"
    echo "$(date '+%Y-%m-%d %H:%M:%S') INVALID $filename expected $EXPECTED_COLUMNS columns, got $actual_columns, moved to rejected/" >> "$LOG_FILE"
  fi
done
```

```bash
chmod +x ~/linux-course/capstone/pipeline.sh
```

```bash
echo "PIPELINE_API_KEY=example-value" > ~/linux-course/capstone/.env
echo "PIPELINE_API_KEY=your-key-here" > ~/linux-course/capstone/.env.example
printf ".env\npipeline.log\nprocessed/\nrejected/\nincoming/\n" > ~/linux-course/capstone/.gitignore
```

Schedule it:

```bash
crontab -e
```

```cron
*/5 * * * * /home/USERNAME/linux-course/capstone/pipeline.sh
```

**Verification** — run all three checks in "Validation & testing" below
(happy path, failure path, idempotency), then confirm git hygiene:

```bash
git init
git status
```

`.env`, `pipeline.log`, `incoming/`, `processed/`, and `rejected/` should
not appear as trackable files; `pipeline.sh`, `.env.example`, and
`.gitignore` should.

### Data setup

Copy the fixtures from [`mock-data/`](./mock-data/) (see "Data sources"
below) into `~/linux-course/capstone/incoming/` as each test step calls
for them — don't copy all three in at once, since the cron-trigger proof
specifically depends on `batch3.csv` being dropped in *after* the
pipeline is already scheduled and running.

## Business problem

> How do you build a single automated system that ingests batches of
> data files, validates them, routes them correctly, and proves — under
> both success and failure — that it ran unattended and securely?

- Gives the cohort a systems-integration checkpoint: proves shell
  scripting, scheduling, and credential hygiene work together, not just
  individually.
- Mirrors a real production batch pipeline: files arrive, get validated,
  get routed, and the run is logged and provably scheduled rather than
  manually triggered.
- Forces proof that the failure path works, not just the happy path.

## Requirements / acceptance criteria

1. **Idempotent automation** — the script must handle repeated runs
   without manual cleanup. Re-running it should never duplicate work,
   error out on already-processed files, or require manual state reset.
   *Solution: `mv` removes each file from `incoming/` as it's handled,
   so a second run with nothing new simply finds nothing to process.*
2. **Reliable scheduling** — the pipeline runs automatically via cron
   (M8), not by the trainee remembering to run it by hand.
   *Solution: a `*/5 * * * *` crontab entry runs `pipeline.sh`
   unattended.*
3. **Structured logging** — every run appends a clear, parseable log
   line recording what happened (filename, valid/invalid, timestamp) to
   a single pipeline log file.
   *Solution: every branch of the script appends one
   `YYYY-MM-DD HH:MM:SS STATUS filename detail` line to `pipeline.log`.*
4. **Security baked in** — the credential the pipeline depends on is
   read from an environment variable, never hardcoded, and `.gitignore`
   prevents any credential file from being committed. Applied to the
   *same* script built here, not a separate exercise.
   *Solution: `PIPELINE_API_KEY` is sourced from `.env` at script start;
   `.gitignore` excludes `.env`.*
5. **Validation criterion** — the pipeline must be run at least once
   with a deliberately malformed file to prove the failure path
   (validation → `rejected/` → logged) actually works, not just the
   happy path.
   *Solution: `batch2.csv` (2 columns, expected 3) proves this — see
   "Validation & testing" below.*

## Deliverable

This is a headless automation pipeline — there is no screen. Its
observable output is:

- **`pipeline.log`** — one structured line per file processed (see
  "Output & usage notes" for the exact format).
- **`processed/`** — where valid files land.
- **`rejected/`** — where invalid files land.
- **A crontab entry** — `*/5 * * * * /home/USERNAME/linux-course/capstone/pipeline.sh`
  — and a `pipeline.log` line timestamped by that scheduled run (not a
  hand-triggered one) confirming it actually fired on schedule.

## Data sources

Three small, static CSV fixtures live in [`mock-data/`](./mock-data/) in
this folder — just enough to exercise every path the pipeline must
prove, not a large or realistic dataset:

| File | Shape | Purpose |
|---|---|---|
| [`mock-data/batch1.csv`](./mock-data/batch1.csv) | `a,b,c` / `1,2,3` (3 columns) | Happy path — valid file, routes to `processed/` |
| [`mock-data/batch2.csv`](./mock-data/batch2.csv) | `a,b` / `1,2` (2 columns) | Failure path — malformed, routes to `rejected/`; this is the capstone's required validation criterion |
| [`mock-data/batch3.csv`](./mock-data/batch3.csv) | `a,b,c` / `4,5,6` (3 columns) | Cron-trigger proof — dropped into `incoming/` and left for the *scheduled* run to pick up, not run by hand |

These are static, bounded fixtures copied into the pipeline's own
`incoming/` directory at test time — not a live or queried source.

## Project architecture

```text
Scheduled trigger (cron, M8)
        |
        v
Bash script (M9) reads new files from incoming/
        |
        v
Validate column count
   |             |
 valid         invalid
   |             |
   v             v
processed/    rejected/
   |             |
   +------+------+
          |
          v
   Append result to pipeline.log
```

## Data model

Not applicable — this pipeline routes flat files by validation result;
there is no dimensional or relational data model to describe.

## Technology stack

| Area | Technology | Module |
|---|---|---|
| Scripting | Bash | M9 |
| Scheduling | cron | M8 |
| Credential handling | Environment variables via a sourced `.env` file, excluded from git by `.gitignore` | M13 |

_Scope note: the course currently covers M0–M9 and M13. This capstone
does not use Docker, Kubernetes, or a separate observability tool — see
"Project scope" below._

## Key engineering features

- **Idempotency via draining `incoming/`** — `mv` removes each processed
  file from `incoming/` as it's handled, so a second run with no new
  files simply finds nothing left to process (`shopt -s nullglob` keeps
  the `for` loop from erroring on an empty directory).
- **Cron-compatible credential loading** — the script sources `.env`
  itself (`set -a` / `source` / `set +a`) rather than relying on a
  variable exported in the trainee's interactive shell. This directly
  addresses M8's "cron runs with a minimal environment" lesson: an
  interactively-exported variable would not be visible to the same
  script running under cron.
- **Fail-closed credential check** — if `PIPELINE_API_KEY` is unset, the
  script logs an error and exits before touching any files, rather than
  running with a missing credential.
- **Git hygiene applied to runtime artifacts, not just secrets** —
  `.gitignore` excludes `pipeline.log`, `processed/`, `rejected/`, and
  `incoming/` alongside `.env`, since they're all runtime output/data,
  not source, even though only `.env` holds an actual secret.

## Validation & testing

**Happy path** (`batch1.csv`):
```bash
printf "a,b,c\n1,2,3\n" > ~/linux-course/capstone/incoming/batch1.csv
~/linux-course/capstone/pipeline.sh
cat ~/linux-course/capstone/pipeline.log
```
Expected log line: `... VALID batch1.csv moved to processed/`

**Failure path** (`batch2.csv`) — the required validation criterion:
```bash
printf "a,b\n1,2\n" > ~/linux-course/capstone/incoming/batch2.csv
~/linux-course/capstone/pipeline.sh
cat ~/linux-course/capstone/pipeline.log
```
Expected log line: `... INVALID batch2.csv expected 3 columns, got 2, moved to rejected/`

**Idempotency check** — re-run immediately with no new files:
```bash
~/linux-course/capstone/pipeline.sh
cat ~/linux-course/capstone/pipeline.log
```
Expected: no new log lines, no error.

**Cron-trigger proof** (`batch3.csv`) — dropped into `incoming/` and
left for the *scheduled* run, not triggered by hand; after 5+ minutes,
`pipeline.log` gains a `VALID batch3.csv` line timestamped by cron, not
by the trainee's terminal.

## Output & usage notes

`pipeline.log` lives at `$BASE_DIR/pipeline.log` (append-only). Each
line: `YYYY-MM-DD HH:MM:SS STATUS filename detail`. A missing or unset
`PIPELINE_API_KEY` also logs an `ERROR` line and exits non-zero before
any file is touched. Caveat: this capstone's scope has no log rotation
— `pipeline.log` grows unbounded, which is an intentional out-of-scope
simplification (see "Project scope"), not an oversight.

## Platform notes

| Behavior | WSL2 | macOS |
|---|---|---|
| Cron-triggered script environment | Minimal, same as any Linux cron job — this is exactly why the pipeline sources `.env` itself rather than relying on an interactively-exported variable | Same consideration applies once running inside a real Linux VM/container (per M0); `cron` on Darwin itself is deprecated, so this capstone should be scheduled inside the Linux environment, not the macOS host |

## Repository structure

```text
capstone/
├── capstone-end-to-end-pipeline-instructor.md   this file (solution)
├── capstone-end-to-end-pipeline-cohort.md       trainee-facing spec (no solution)
└── mock-data/
    ├── batch1.csv   valid — happy path
    ├── batch2.csv   malformed — failure path
    └── batch3.csv   valid — cron-trigger proof
```

`pipeline.sh` itself, `incoming/`, `processed/`, `rejected/`, `.env`, and
`pipeline.log` are created by the trainee at build time under their own
`~/linux-course/capstone/` working directory — they are not part of this
repo.

## Documentation

- [`capstone-end-to-end-pipeline-cohort.md`](./capstone-end-to-end-pipeline-cohort.md) — the trainee-facing version of this same document.
- `knowledge/patterns/capstone-documentation-template.md` — the pattern this document was filled in from.
- `docs/arch-docs/linux-training.md` §5 — the architecture doc's capstone section (its fuller vision includes Docker/Kubernetes/observability, which this capstone intentionally does not implement yet — see "Project scope").

## Checkpoint (self-assessed)

- [x] My pipeline script validates incoming CSVs and routes them to
      `processed/` or `rejected/` based on column count.
      *Expected: `batch1.csv` → `processed/`, `batch2.csv` → `rejected/`.*
- [x] Running the script twice in a row with no new files produces no
      errors and no duplicate log entries (idempotent).
      *Expected: second run's `pipeline.log` has zero new lines.*
- [x] The pipeline reads its credential from an environment variable
      (via a sourced `.env`), never hardcoded.
      *Expected: `grep PIPELINE_API_KEY pipeline.sh` finds no literal
      key value, only the variable name.*
- [x] `.gitignore` excludes `.env`, `pipeline.log`, `incoming/`,
      `processed/`, and `rejected/`, confirmed with `git status`.
      *Expected: `git status` after `git init` shows only `pipeline.sh`,
      `.env.example`, and `.gitignore` as trackable.*
- [x] I scheduled the pipeline with cron and confirmed, from
      `pipeline.log`, that it ran automatically on schedule — not only
      when triggered by hand.
      *Expected: a `VALID batch3.csv` line timestamped by the cron run,
      not by a manual invocation.*
- [x] I ran the pipeline against a deliberately malformed file and
      confirmed the failure path (validation → `rejected/` → logged)
      works as intended.
      *Expected: `batch2.csv` in `rejected/`, matching `INVALID` log
      line.*

## Project scope

This capstone builds directly on the automation and security skills
already covered in M8, M9, and M13 — a scheduled Bash pipeline with
proper credential handling. It does **not** use Docker, Kubernetes, or a
separate log-analysis tool; observability here means the pipeline's own
log output is complete and correct, not a separate dashboard. This is a
deliberate scope boundary, not an omission: the architecture doc's
fuller vision (`docs/arch-docs/linux-training.md` §5) additionally
describes containerizing the pipeline (M10), scheduling it as a
Kubernetes CronJob (M11), and feeding its logs into a grep/awk
observability report (M12) — none of which `modules/` currently teaches.
If the course's module scope grows to include M10–M12, this capstone
should be revisited rather than assumed to already cover them.
