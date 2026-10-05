# Package Management Tasks

**Objective:** Install, verify, and reason about where software actually came from — system package
manager vs. language-level package manager — rather than installing things until errors stop.

> There is no solutions file for this stage. This checklist guides the work — it doesn't contain it. See
> `brief.md` for the full objective, scope, stack constraints, and definition of done, and fill in
> `write-up-template.md` as you go, not after.

## Setup

- [ ] Confirm with your trainer: that your chosen Python package meaningfully exercises a language-level
      package manager, if in doubt.
- [ ] Choose the Python package you'll install.

## Build

- [ ] Research the "Research:" questions under each Scope bullet in `brief.md` before you start.
- [ ] Install a JDK with the system package manager, noting the repository and dependencies it pulls in.
- [ ] Install your Python package with a language-level package manager.
- [ ] Confirm the version of each installed component from the command line.
- [ ] Note where each piece of software came from and which manager installed it.

## Verify

- [ ] Confirm you can state which manager installed which piece, and why it was the right one.
- [ ] Confirm each version came from the command line, not the install log.
- [ ] Practice explaining what would need to be true for an install to succeed silently but still be
      broken.

## Write-up

- [ ] Under "What I built," describe the JDK and Python package you installed and how you verified them.
- [ ] Under "Why it's built this way," explain your package choice and what made you confident each
      install was correct.
- [ ] Under "How to build it," write a guide to installing and verifying at both the system and language
      level.
- [ ] Under "Concepts worth explaining," pick 1–2 ideas (system vs. language-level management, or
      dependency resolution) and explain each in your own words.
- [ ] Under "What tripped me up," capture anything that didn't go as expected.
- [ ] Under "Checkpoint evidence," show the version-check output for both components.
- [ ] Under "What I'd do differently," reflect on what you'd verify differently.
- [ ] Final self-review: re-read your work against M6's definition of done.
