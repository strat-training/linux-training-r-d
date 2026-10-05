# Modules

This directory holds the Build-to-Teach version of *Linux Training for Data Engineers* — a parallel
track to the lesson-based content in `docs/materials/modules/`. The format is different on purpose:
instead of being taught the material, the trainee builds toward each stage's deliverable from a brief,
and writes up how they did it as they go. The write-up becomes the actual teaching content for the next
cohort.

## The cycle

1. **Trainer hands over the brief.** Each stage folder's `brief.md` states the objective, scope, stack
   constraints, the deliverable (named, not explained step-by-step), and an observable definition of
   done. The Objective and Scope summarize the core idea, why it matters, and how it works, and each
   Scope bullet ends with a "Research:" pointer — the terms and questions the trainee should look up.
   No lab steps and no solutions — the trainee is expected to reason out how to get there.
2. **Trainee builds and writes up the stage together, not sequentially.** `write-up-template.md` is
   filled in *as the trainee works*, not after the deliverable already exists — decisions, concepts, and
   obstacles are easiest to capture accurately in the moment, not reconstructed afterward.
3. **Both get reviewed together.** The trainer reviews the working deliverable and the write-up side by
   side at each stage, not just at the end of the course — judging whether the work is teachable, not
   only whether it runs.
4. **Trainee revises** based on that feedback.
5. **The finished pair goes into the content library.** The trainer decides what carries into the next
   cohort's plan — a strong write-up may itself become the next cohort's reference material.

## Why review as you go, not at the end

A write-up finished after the deliverable already works tends to be thin — there's no real pressure to
make it good once the thing runs. Reviewing both pieces together, stage by stage, means the trainer is
judging whether the work is teachable, not just whether it works, and catches problems early instead of
in a finished draft nobody wants to redo.

## Stages

Mirrors the architecture in `docs/arch-docs/linux-training.md` — one folder per module, in dependency
order:

| Stage | Module | Builds on | Week |
|---|---|---|---|
| `m01-linux-fundamentals` | M1: Linux Fundamentals | — | 1 |
| `m02-cli-basics` | M2: CLI Basics | M1 | 1 |
| `m03-filesystem-navigation` | M3: Filesystem Navigation | M2 | 2 |
| `m04-viewing-editing-files` | M4: Viewing & Editing Files | M3 | 2 |
| `m05-permissions-ownership` | M5: Users, Permissions, Ownership | M4 | 3 |
| `m06-package-management` | M6: Package Management | M5 | 3 |

## What's in each stage folder

- `brief.md` — the trainer's curriculum handoff: objective, scope (with "Research:" pointers), stack
  constraints, the deliverable, and definition of done. No answers.
- `write-up-template.md` — blank, for the trainee to fill in while building.
- `tasks.md` — a bridging checklist (setup, build, verify, write-up) that mirrors the brief's Scope and
  definition of done, present in every stage folder (see `.claude/commands/trainee-task-planner`).
