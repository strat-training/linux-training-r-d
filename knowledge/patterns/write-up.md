# Capstone Write-up

> This is the most important write-up of the course — it's meant to become part of the content
> library. Write it so the next cohort could use it as a model for their own capstone, not just as a
> record of what you did.

## What I built

Your topic, your two related resources, the relationship between them, and your aggregate endpoint.

## Why it's built this way (key decisions)

- Why this topic, and why this particular relationship between your two resources?
- What design decisions from Stage 7 (storage interface, error shape, ID generation, concurrent-write
  handling) did you carry forward unchanged, and where did your resources need something different —
  especially once two resources are involved instead of one?
- What does your aggregate endpoint compute, and why did you design it that way rather than some other
  shape?
- Where did you spend the most time, and why?

## AI collaboration log

Be specific, not a vague "I used AI to help write some code":

- Which AI tool(s) did you use, and for roughly what portion of the work (scaffolding, a specific
  endpoint, tests, debugging, something else)?
- Give 2–3 concrete examples of what you asked for.
- At least one moment the tool's first suggestion was wrong, inefficient, or didn't fit your design —
  what was wrong with it, how you noticed, and what you did instead. If you genuinely can't think of one,
  that's worth questioning: were you checking closely enough?
- What did you accept largely as-is, and what did you rewrite or redesign yourself?

## How to build it (teach it to the next engineer)

Write this as a guide someone could actually follow to build something like your capstone from scratch
— the order you tackled things in, and why that order made sense. Assume the reader has completed
Stages 1–8 but hasn't built a capstone before.

## Concepts worth explaining

Pick 2–3 ideas from across the whole course that this capstone made concrete for you, and explain each
one in your own words as if teaching it for the first time.

## What tripped me up

The real obstacles — bugs, design mistakes, test failures, anything that cost you real time. These are
usually the most valuable part of a capstone write-up.

## Checkpoint evidence

Show: a clean-clone run (`npm install`, `npm start`, `npm test`), your test suite passing, the
relationship and aggregate endpoint working, and search/filter, pagination, and request logging all
behaving as described in the brief.

## Rubric self-assessment

Score yourself honestly against all six criteria in the fixed rubric in `brief.md` (including
Understanding & defensibility) before your trainer reviews it — where do you think you land, and why?
For the defensibility criterion specifically: is there any part of your own code you'd struggle to
explain cold, without re-reading it first? Name it here rather than hoping it doesn't come up in the
demo.

## What I'd do differently

If you started the capstone over today, what would you change?