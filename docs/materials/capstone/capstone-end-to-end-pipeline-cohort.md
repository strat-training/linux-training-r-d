# Capstone: End-to-End Automation Pipeline

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

### Data setup

Copy the fixtures from [`mock-data/`](./mock-data/) (see "Data sources"
below) into your `incoming/` directory as each validation step calls for
them — don't copy all three in at once, since the cron-trigger proof
specifically depends on `batch3.csv` being dropped in *after* your
pipeline is already scheduled and running.

## Business problem

> How do you build a single automated system that ingests batches of
> data files, validates them, routes them correctly, and proves — under
> both success and failure — that it ran unattended and securely?

- Gives you a systems-integration checkpoint: prove shell scripting,
  scheduling, and credential hygiene work together, not just
  individually.
- Mirrors a real production batch pipeline: files arrive, get validated,
  get routed, and the run is logged and provably scheduled rather than
  manually triggered.
- Forces you to prove the failure path works, not just the happy path.

## Requirements / acceptance criteria

1. **Idempotent automation** — your script must handle repeated runs
   without manual cleanup. Re-running it should never duplicate work,
   error out on already-processed files, or require you to manually
   reset state.
2. **Reliable scheduling** — the pipeline must run automatically via
   cron (M8), not by you remembering to run it by hand.
3. **Structured logging** — every run must append a clear, parseable log
   line recording what happened (filename, valid/invalid, timestamp) to
   a single pipeline log file.
4. **Security baked in** — the credential the pipeline depends on must
   be read from an environment variable, never hardcoded, and
   `.gitignore` must prevent any credential file from being committed.
   This applies to the *same* script you build here, not a separate
   exercise.
5. **Validation criterion** — you must run the pipeline at least once
   with a deliberately malformed file to prove the failure path
   (validation → `rejected/` → logged) actually works, not just the
   happy path.

## Deliverable

This is a headless automation pipeline — there is no screen. Your
deliverable's observable output is:

- **`pipeline.log`** — one structured line per file processed.
- **`processed/`** — where valid files land.
- **`rejected/`** — where invalid files land.
- **A crontab entry** — and a `pipeline.log` line proving a scheduled
  run actually fired, not just a hand-triggered one.

## Data sources

Three small, static CSV fixtures are provided in
[`mock-data/`](./mock-data/) in this folder — just enough to exercise
every path your pipeline must prove:

| File | Shape | Purpose |
|---|---|---|
| [`mock-data/batch1.csv`](./mock-data/batch1.csv) | 3 columns | Use for the happy-path test |
| [`mock-data/batch2.csv`](./mock-data/batch2.csv) | 2 columns (deliberately malformed) | Use for the required failure-path test |
| [`mock-data/batch3.csv`](./mock-data/batch3.csv) | 3 columns | Use to prove the cron-triggered run — drop this in and let the *schedule* pick it up, don't run the script by hand for this one |

These are static, bounded test fixtures — not a live or queried source.

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
there is no dimensional or relational data model involved.

## Technology stack

| Area | Technology | Module |
|---|---|---|
| Scripting | Bash | M9 |
| Scheduling | cron | M8 |
| Credential handling | Environment variables via a sourced `.env` file, excluded from git by `.gitignore` | M13 |

## Key engineering features

Your implementation must satisfy these properties — how you achieve
each one is your design decision:

- **Idempotent by design** — running your script twice in a row with no
  new files must produce zero new log lines and no errors.
- **Cron-compatible credential loading** — your script must read its
  credential correctly when triggered by cron's minimal environment,
  not only when run from your own interactive shell (recall M8's lesson
  on what cron does and doesn't inherit from your shell).
- **Git hygiene extends past the secret itself** — think about which
  *runtime* artifacts (not just the credential file) shouldn't be
  committed, and why.

## Validation & testing

Prove your pipeline works under both success and failure — this is a
required part of the capstone, not optional polish:

- **Happy path**: run the pipeline against `batch1.csv`. A correct
  result: the file ends up in `processed/`, and `pipeline.log` gains one
  line recording it as valid.
- **Failure path** (the required validation criterion): run the
  pipeline against `batch2.csv`. A correct result: the file ends up in
  `rejected/`, and `pipeline.log` gains one line recording it as
  invalid, naming what was expected vs. what was found.
- **Idempotency check**: run the pipeline again immediately with no new
  files. A correct result: no errors, no new log lines.
- **Cron-trigger proof**: drop `batch3.csv` into your pipeline's
  `incoming/` directory and wait for the *scheduled* run — don't invoke
  the script by hand for this one. A correct result: a new
  `pipeline.log` line timestamped by the cron-triggered run.

## Output & usage notes

Design your log format so each line clearly records, at minimum: a
timestamp, the filename, and whether it was valid or invalid (per
Requirement 3). Think about what a future reader — or an automated
report — would need from each line to reconstruct what happened on a
given run without re-running anything.

## Platform notes

| Behavior | WSL2 | macOS |
|---|---|---|
| Cron-triggered script environment | Minimal, same as any Linux cron job — this is exactly why your script should source `.env` itself rather than relying on an interactively-exported variable | Same consideration applies once running inside a real Linux VM/container (per M0); `cron` on Darwin itself is deprecated, so schedule this capstone inside your Linux environment, not the macOS host |

## Documentation

Submit a short written capstone report, saved alongside your course
notes, containing:

- The full contents of `pipeline.log` after both the happy-path and
  failure-path test runs.
- The crontab entry you used to schedule the pipeline, and the
  cron-triggered log line confirming it ran on schedule — not only when
  triggered by hand.
- The `git status` output confirming `.env`, `pipeline.log`,
  `incoming/`, `processed/`, and `rejected/` are excluded from version
  control.
- Two to three sentences, in your own words, explaining why your script
  is idempotent — what specifically about its design prevents a second
  run from reprocessing or corrupting already-handled files.

## Checkpoint (self-assessed)

- [ ] My pipeline script validates incoming CSVs and routes them to
      `processed/` or `rejected/` based on column count.
- [ ] Running the script twice in a row with no new files produces no
      errors and no duplicate log entries (idempotent).
- [ ] The pipeline reads its credential from an environment variable
      (via a sourced `.env`), never hardcoded.
- [ ] `.gitignore` excludes `.env`, `pipeline.log`, `incoming/`,
      `processed/`, and `rejected/`, confirmed with `git status`.
- [ ] I scheduled the pipeline with cron and confirmed, from
      `pipeline.log`, that it ran automatically on schedule — not only
      when triggered by hand.
- [ ] I ran the pipeline against a deliberately malformed file and
      confirmed the failure path (validation → `rejected/` → logged)
      works as intended.

## Project scope

This capstone builds directly on the automation and security skills
already covered in M8, M9, and M13 — a scheduled Bash pipeline with
proper credential handling. It does **not** use Docker, Kubernetes, or a
separate log-analysis tool; observability here means your pipeline's own
log output is complete and correct, not a separate dashboard.
