# Pattern: Content-Pack File Structure

Each content-pack file (`modules/week-XX-<topic-slug>.md`) follows this
section structure. See `modules/week-01-linux-fundamentals-cli-basics.md`
for a filled-in example.

```
# Week N — <Topic> (M<start>–M<end>)

## Objective
## Topics
## M<n>: <Module Name>
- Learning Objective
- Core Idea
- Why It Matters
- How It Works
  - Concepts
  - Best Practices
  - Real-World Example
- Supplemental Reading
## Hands-on lab
### M<n> — <lab section, one per module in this pack>
## Lab exercise
## WSL2 vs macOS
## Checkpoint (self-assessed)
```

## Rules

- **Supplemental Reading** is populated only from real, cited sources.
  Check `knowledge/references/` first (the raw research
  material, with actual URLs attached to specific topics) — it's more
  authoritative than the arch doc's Resource Map (`docs/arch-docs/
  linux-training.md` §7), which only has titles, no URLs. If no source
  material exists for a topic, say so explicitly with a `**Note:**` line
  instead of writing Concepts/Best-Practices/Real-World-Example content
  from general knowledge dressed up as sourced material — see M2
  (`week-01`) for the zero-source precedent, and M4 (`week-02`) /
  M6/M7 (`week-03`/`week-04`) for the narrow-source precedent: cite the
  one real, specific source that exists and say explicitly what it does
  and doesn't cover, rather than treating a partial source as if it
  covered the whole topic.
- **Commands always go in fenced code blocks**, never as inline
  backticks strung together as if they were a runnable line — even a
  single command gets its own ```` ```bash ```` (or ```` ```powershell ````
  / ```` ```ini ````, as applicable) block. Inline backticks are for
  naming a command/flag/file/path in prose (e.g., "the `ls` command"),
  not for showing something the trainee is meant to type or paste.
- **Never reference this repo's own internal file paths in
  trainee-facing content.** Files under `modules/` are read by trainees,
  who have no access to this project's `docs/`, `knowledge/`, or
  `graphify-out/` directories. A "no source" `**Note:**` should say
  *"No cited source material exists for this topic in the research
  gathered for this course"* — never name where that was checked (e.g.
  never write "`knowledge/references/` has no entry for this"). This
  rule applies only to `modules/*.md`; internal docs like this pattern
  file or `knowledge/rules/arch-summary.md` may reference paths freely,
  since only course authors read those.
- **Link syntax:** always use inline links, `[Label](url)`. Never use
  `[Label]: url` (Markdown reference-*definition* syntax) — it only
  renders if something else in the document references `[Label]`
  elsewhere; used standalone, as a "citation," it silently disappears
  from the rendered page instead of showing as a link. (`week-01`'s M1
  citation still has this bug as of this writing — fix it opportunistically
  if you're ever editing that file for another reason.)
- **WSL2 vs macOS** only lists rows where platform behavior actually
  differs — don't pad it with rows that are identical on both.
- Every content-pack file ends in a self-assessed Checkpoint — a short
  checklist the trainee ticks off themselves, not a graded quiz.
- Files are flat, one per content pack, directly under `modules/` — never
  nested in a per-module folder, never named `README.md`.
