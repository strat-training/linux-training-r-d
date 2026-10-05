# Capstone Brief

**Hours:** 4 · **Builds on:** everything from Stages 1–8, directly extending the Stage 7/8 Tasks API
pattern onto resources of your own.

## Objective

Build a small, tested, documented REST API end to end, on your own, and write the module that teaches
someone else how you did it — and be able to defend every decision in it, regardless of how the code
got typed.

## A note on AI-assisted development

You're expected to use AI coding tools for this capstone — that's realistic, not cheating. The bar this
sets is understanding, not output:

1. **You must be able to explain any part of your code on the spot**, without looking it up or asking
   an AI tool again. During your demo, your trainer will pick a function or endpoint at random, ask you
   to walk through what it does and why, and ask you to make a small change to it live, unassisted.
2. **The design decisions are yours, not the tool's.** An AI tool can write an implementation; you decide
   the resource shape, the relationship between resources, the concurrency approach, the endpoint
   contract, the aggregate logic. If you can't explain *why* a decision was made, it isn't actually your
   decision yet — go back and make it one.
3. **Keep a short log of at least one moment an AI tool's first suggestion was wrong, inefficient, or
   didn't fit your design**, and what you did instead. This is part of your write-up, not optional
   color — catching and correcting a bad suggestion is the actual skill being assessed here, more than
   the code that resulted.

None of this means avoiding AI tools or second-guessing every line out of caution — it means treating
suggested code the way you'd treat a teammate's pull request: read it, understand it, and be ready to
defend keeping it.

## Scope / topic rule (raised from a single resource)

Default topic: extend your Stage 7 Tasks API into **two related resources** (e.g. Tasks + Projects, or
Notes + Tags) with a real relationship between them — a one-to-many relationship is enough (e.g. a
project has many tasks). A single resource with one bolt-on feature is no longer sufficient — AI tools
make that too fast to be a meaningful test of design judgment. Propose your own topic if you prefer,
under the same constraint: two related resources, approved by your trainer before you start building.

## Stack constraints

Same as the rest of the course: Node.js 24 LTS, Express 5.x, Jest 30 with Supertest, ES Modules, JSON
file persistence, Bruno. No databases, no Docker.

## Requirements (all required — the former stretch goals are now part of the bar)

- Express server with full CRUD for **both** resources, structured the same way as Stage 7
  (routes/controllers/services).
- The relationship between your two resources is **enforced, not decorative** — e.g. creating a child
  resource under a parent that doesn't exist returns the right error, and you've made (and can defend)
  an explicit decision about what happens to children when a parent is deleted.
- **Search/filter via query parameters** on at least one list endpoint.
- **Pagination** on at least one list endpoint.
- **Request logging middleware.**
- **One aggregate/computed endpoint that isn't plain CRUD** — something that reads across your data
  rather than returning a single record or list unmodified (e.g. counts, groupings, a summary). This is
  the one endpoint that can't be lifted wholesale from Stage 7 — it has to be designed.
- Input validation and the shared error shape used since Stage 7, extended to cover relationship errors
  (e.g. referencing a nonexistent parent).
- JSON file persistence, carrying forward your Stage 7 thinking on concurrent writes and ID generation —
  applied now across two resources. Decide explicitly whether they share a data file or use separate
  ones, and be able to justify it.
- Configuration via environment variables (`PORT`, data file path(s)).
- **At least 10 meaningful automated tests** (Jest and Supertest, per Stage 8's definition of
  "meaningful") — covering CRUD on both resources, the relationship's enforcement, the search/filter and
  pagination behavior, and the aggregate endpoint. Tests that only cover the easy CRUD paths don't meet
  this bar even if there are 10 of them.
- A Bruno collection covering every endpoint.
- A `README.md` with setup steps, available scripts, endpoint documentation, and your AI-collaboration
  log (see `write-up-template.md`).
- A clean Git history, in its own repository separate from your stage practice repo, committed in stages
  as you build rather than as one giant commit at the end.

### Stretch goals (optional, not scored)

- Sorting via a query parameter.
- Soft deletes instead of hard deletes.
- A second aggregate endpoint.

## Demo (raised bar)

In addition to the existing demo components (app running, code structure, tests passing): your trainer
will pick one or two places in your code at random and ask you to (a) explain what it does and why, and
(b) make a small modification to it live, without AI assistance. This isn't a gotcha — it's the actual
test of whether the capstone is yours.

## Rubric (fixed — do not deviate from this when self-assessing)

| Criteria | Excellent | Satisfactory | Needs work |
| --- | --- | --- | --- |
| Functionality | All CRUD + relationship + aggregate endpoints work correctly | Most endpoints work, relationship or aggregate has gaps | Major endpoints broken |
| Code organization | Clear separation, readable code, relationship handled cleanly | Some structure | Everything in one file |
| Error handling | Consistent, meaningful errors including relationship errors | Partial handling | Crashes on bad input |
| Testing | 10+ meaningful tests covering relationship/search/pagination/aggregate | A few tests, easy paths only | No tests or failing |
| Documentation | Clear README, Bruno collection, AI-collaboration log included | Basic README | Missing |
| Understanding & defensibility | Explains any code confidently, modifies it live without issue | Explains most code, needs a hint to modify it | Can't explain code they didn't personally reason through |

> **Note (Proposed, supersedes decision D8):** this replaces the original 5-criteria, equal-8%-each
> split with 6 criteria. Confirm the per-criterion weighting with your program owner before scoring a
> cohort against it — equal weighting (~6.67% each) is the default absent other direction.

## Definition of done

- From a clean clone: `npm install` and `npm start` run, every CRUD endpoint responds (both resources),
  and `npm test` exits successfully. A capstone that doesn't meet this bar doesn't pass, regardless of
  rubric score elsewhere.
- The relationship between your two resources is enforced, and the aggregate endpoint returns correct,
  non-trivial output.
- Search/filter, pagination, and request logging all work as described above.
- You pass the live code-walkthrough component of the demo — if you can't explain or modify code you
  submitted, that part of the capstone doesn't count as done regardless of whether it runs.

## What goes into the content library

Both your working repository and your `write-up-template.md` (filled in) are the deliverable — the
write-up is what the next cohort's capstone brief and training content get built from. Write it as
teaching material, not a retrospective.

## Still open / ask your trainer

- The demo format for your cohort (format depends on cohort size — ask rather than assume).
- The confirmed per-criterion rubric weighting (see note above).
- Whether the two-resource relationship requirement should flex for a cohort running behind schedule —
  ask before assuming a single-resource capstone is acceptable.