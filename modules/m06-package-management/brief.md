# Package Management Brief

## Objective

By the end of this stage, the trainee can install, verify, and reason about where software actually
came from (system package manager vs. language-level package manager), rather than installing things
until errors stop.

## Scope

The trainee installs real software on the trainee's own system, proves that it worked, and records the
following in the trainee's own words:

- **System-level package management** — almost all software arrives through a package manager that
  tracks what is installed, resolves dependencies, and removes things cleanly, drawing from configured
  repositories. Research: what a repository is, what dependency resolution does, and what installing
  system-wide software requires from M5's permission model.
- **Language-level package management** — language ecosystems add their own package managers for their
  own libraries, separate from the system's. Research: how it differs from the system manager, and why
  installing a library globally into the system's Python can cause problems.
- **Verifying an install** — "ran without error" is not proof, and the version installed may not be the
  one expected. Research: how to ask the command line which version of each component is installed and
  where it came from.

## Stack constraints

A JDK and at least one Python package, each installed via the appropriate manager: a system package
manager for the JDK and a language-level package manager for the Python package. Which specific tools
to use within that constraint is the trainee's choice.

## Deliverable

A verified JDK + Python package install: evidence that both are installed, at known versions, through
the correct category of package manager for each.

## Lab

**Goal.** Install a JDK and a Python package through the correct package manager for each, verify both
from the command line, and remove the Python package cleanly.

**You do, in the trainee's own Linux environment.**

1. The trainee updates the local package list, then installs a JDK: `sudo apt update`, then
   `sudo apt install default-jdk`.
2. The trainee verifies the install by checking the installed version with `java -version`.
3. The trainee confirms that the package is tracked by the package manager with
   `apt list --installed | grep jdk`.
4. The trainee installs a Python package with `pip install requests` and verifies it separately from
   the system package manager with `pip show requests`.
5. The trainee removes the `pip` package cleanly with `pip uninstall requests`.

**You build and capture.** The trainee captures the `java -version` output, the `apt list` match, the
`pip show requests` output, and the `pip uninstall` output.

**Clean-up.** The `pip uninstall` in step 5 removes the Python package.

## Definition of done

- [ ] DoD-01: The trainee's pasted `java -version` output shows the actual JDK version that `apt`
  installed, taken from the command line and not assumed from the install log (Lab steps 1–2).
- [ ] DoD-02: The pasted `apt list --installed | grep jdk` output shows that the JDK is tracked by the
  system package manager (Lab step 3).
- [ ] DoD-03: The pasted `pip show requests` output shows the Python package and its version, verified
  separately from `apt`, and the notes state which package manager installed which piece and why that
  was the right one for that layer (Lab steps 1 and 4).
- [ ] DoD-04: The pasted `pip uninstall requests` output shows that the package was removed through the
  package manager and not by deleting files (Lab step 5).
- [ ] DoD-05: Using the version checks from steps 2 and 4, the trainee can explain what would need to be
  true for an install to succeed silently but still be broken.

## Best practices this stage demonstrates

- Run `sudo apt update` before `sudo apt install`
- Use the system package manager for system-wide software and `pip` or `conda` in a virtual
  environment for project-specific Python libraries
- Verify an install with a version check, not by the absence of an error
- Remove packages with the package manager, never by deleting files by hand

## Still open / ask your trainer

- Which specific Python package to install is the trainee's choice, provided that it meaningfully
  exercises a language-level package manager. The trainee confirms with the trainer if in doubt.
