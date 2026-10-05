# Capstone Tasks

**Objective:** Build a small, tested, documented REST API end to end, on your own, and be able to
defend every decision in it, regardless of how the code was typed.

> There is no solutions file for this stage. This checklist guides the work — it doesn't contain it.
> See `brief.md` for the full requirements, AI-assisted development policy, rubric and definition of
> done, and fill in `write-up-template.md` as you go, not after.

## Setup

- [ ] Confirm with your trainer: the demo format for your cohort.
- [ ] Confirm with your trainer: the per-criterion rubric weighting across the six criteria.
- [ ] Confirm with your trainer: whether the two-resource relationship requirement flexes if your
      cohort is running behind schedule.
- [ ] Propose your topic — two related resources, the relationship between them, and a candidate
      aggregate endpoint — and get it approved by your trainer **before** you start building.
- [ ] Set up a new Git repository for the capstone, separate from your stage practice repo.
- [ ] Read the AI-assisted development policy in `brief.md` and decide how you'll log AI interactions
      as you build, not retroactively at the end.

## Build

- [ ] Scaffold the Express app (routes/controllers/services) for both resources, mirroring your
      Stage 7 Tasks API layout.
- [ ] Design the relationship between your two resources — decide how the child references the parent
      in your data shape.
- [ ] Implement full CRUD for both resources.
- [ ] Enforce the relationship: creating a child under a nonexistent parent returns the right error;
      decide and implement what happens to children when a parent is deleted.
- [ ] Add validation wired to the shared error shape, extended to cover relationship errors.
- [ ] Reason through concurrency and ID generation now that two resources are involved — decide
      whether they share a data file or use separate ones, and why.
- [ ] Implement JSON persistence for both resources, applying your concurrency decision.
- [ ] Add search/filter via query parameters on at least one list endpoint.
- [ ] Add pagination on at least one list endpoint.
- [ ] Add request logging middleware.
- [ ] Design and implement your aggregate/computed endpoint — something that reads across your data
      rather than returning a single record or list unmodified.
- [ ] (Optional, not scored) Implement one stretch goal — sorting, soft deletes, or a second aggregate
      endpoint — only once everything required above is solid.
- [ ] Make `PORT` and your data file path(s) configurable via environment variables.
- [ ] Write a Bruno collection covering every endpoint, including failure and relationship-error
      cases.
- [ ] Write 10+ meaningful tests covering CRUD on both resources, relationship enforcement,
      search/filter, pagination, and the aggregate endpoint — not just the easy paths.
- [ ] Write `README.md`: setup, scripts, endpoint docs, and a pointer to your AI collaboration log.
- [ ] Commit in stages as you go — not as one commit at the end.

## Verify

- [ ] From a clean clone, confirm `npm install` and `npm start` run and every CRUD endpoint (both
      resources) responds.
- [ ] From a clean clone, confirm `npm test` exits successfully.
- [ ] Confirm the relationship is enforced and the aggregate endpoint returns correct, non-trivial
      output.
- [ ] Confirm search/filter, pagination, and request logging all behave as described.
- [ ] Before your demo: pick a few of your own functions at random and practice explaining and
      modifying them unassisted — this is what your trainer will do live.

## Write-up

- [ ] Under "What I built," describe your topic, two resources, relationship, and aggregate endpoint.
- [ ] Under "Why it's built this way," answer all four questions in the template (topic/relationship
      choice; which Stage 7 decisions carried over vs. changed; your aggregate endpoint's design
      rationale; where you spent the most time).
- [ ] Under "AI collaboration log," document which tools you used, 2–3 concrete prompts, at least one
      corrected bad suggestion, and what you accepted vs. rewrote — capture this as you go, not
      retroactively.
- [ ] Under "How to build it," write a from-scratch guide for the next trainee.
- [ ] Under "Concepts worth explaining," pick 2–3 ideas and explain them in your own words.
- [ ] Under "What tripped me up," capture the real obstacles.
- [ ] Under "Checkpoint evidence," show your clean-clone run, tests passing, relationship/aggregate
      working, and search/filter/pagination/logging behaving.
- [ ] Under "Rubric self-assessment," score yourself against all six criteria including Understanding
      & defensibility; name any code you'd struggle to explain cold.
- [ ] Under "What I'd do differently," reflect on what you'd change.
- [ ] Final self-review: re-read your capstone and write-up against the Definition of done and the
      full six-criteria rubric — confirm you're ready for the live code-walkthrough, not just that it
      runs.