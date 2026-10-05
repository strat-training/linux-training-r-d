# Package Management Brief

**Week:** 3 of 6 (shared with M5, 5–6 hrs total) · **Builds on:** M5 · **Feeds into:** M7

## Objective

You can install, verify, and reason about where software actually came from — system package manager vs.
language-level package manager — rather than installing things until errors stop.

## Scope

You install real software on your own system and prove it worked. Find out, and write down in your own
words:

- **System-level package management** — almost all software arrives through a package manager that
  tracks what's installed, resolves dependencies, and removes things cleanly, drawing from configured
  repositories. Research: what a repository is, what dependency resolution does, and what installing
  system-wide software requires from M5's permission model.
- **Language-level package management** — language ecosystems add their own package managers for their
  own libraries, separate from the system's. Research: how it differs from the system manager, and
  why installing a library globally into the system's Python can cause problems.
- **Verifying an install** — "ran without error" isn't proof, and the version you got may not be the one
  you expected. Research: how to ask the command line which version of each component is installed and
  where it came from.

## Stack constraints

A JDK and at least one Python package, installed via the appropriate manager for each — a system package
manager for the JDK, a language-level package manager for the Python package. Which specific tools you
use within that constraint is your choice.

## Deliverable

A verified JDK + Python package install: evidence that both are installed, at known versions, through
the correct category of package manager for each.

## Definition of done

- You can state which package manager installed which piece of software, and why that was the right
  manager for that layer.
- The version of each installed component is confirmed from the command line, not assumed from the
  install log.
- You can explain what would need to be true for an install to silently succeed but still be broken.

## Still open / ask your trainer

- Which specific Python package to install is your choice, provided it meaningfully exercises a
  language-level package manager — confirm with your trainer if in doubt.
