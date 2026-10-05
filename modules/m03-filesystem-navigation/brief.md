# Filesystem Navigation Brief

**Week:** 2 of 6 (shared with M4, 5–6 hrs total) · **Builds on:** M2 · **Feeds into:** M4

## Objective

You can navigate and build directory structures anywhere on the filesystem without getting lost, and
locate a file you don't already know the exact location of. This removes the course's second failure
mode: "I can't find the log / config / data file."

## Scope

You explore your own filesystem and build a structure of your own. Find out, and write down in your own
words:

- **The Filesystem Hierarchy Standard (FHS)** — Linux has one root (`/`) tree, not drive letters, and a
  convention for what lives where (configuration, logs, user files, installed programs) so you can find
  your way around almost any distro. Research: what `/etc`, `/var`, `/home`, and `/usr` are each for,
  and why the FHS is a convention rather than a rule the kernel enforces.
- **Absolute vs. relative paths** — a path can be anchored at the root or at where you currently are,
  and knowing which one you're using is what stops you getting lost. Research: what `.` and `..` mean,
  and what `~` expands to.
- **Building structures in one step** — a nested directory tree can be created in a single command
  instead of stepping into each level. Research: the option that creates missing parent directories.
- **Searching instead of browsing** — a file you can't place can be found by name or pattern. Research:
  how `find` differs from `locate`, and how to match a name pattern.

## Stack constraints

Your own environment; no tools beyond core navigation and search utilities.

## Deliverable

A nested project directory structure — multiple levels deep, created and populated in a way that
reflects a deliberate organizational scheme, not a flat dump — plus evidence you located a file in it
using search rather than manual browsing.

## Definition of done

- The structure is at least three levels deep and was created without manually stepping into each new
  level one at a time.
- You can state the absolute path to any file in the structure without running a command to check.
- You have located at least one file in the structure using a search tool, not visual browsing.

## Still open / ask your trainer

- How deep or complex the structure needs to be beyond the minimum is a judgment call — ask your trainer
  if your design is borderline.
