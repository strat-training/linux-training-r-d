# Linux Fundamentals Brief

**Week:** 1 of 6 (shared with M2, 5–6 hrs total) · **Builds on:** — (course entry point) · **Feeds
into:** M2

## Objective

By the end of this stage, you can explain what actually happens between typing a command and seeing
output — using your own system as the evidence, not a diagram from a slide.

## Scope

You investigate your own system, not the textbook one. Find out, and write down in your own words:

- **Kernel, shell, and distro** — what each one does, and how a typed command travels from the shell
  to the kernel and back as output. Research: what a system call is, and why the shell and terminal
  emulator are two different things.
- **System identity** — which kernel and distribution you're actually running, and where the system
  reports that about itself. Research: what `uname -a` and `/etc/os-release` each tell you, and why
  they aren't the same fact.
- **The filesystem as an abstraction** — Linux exposes hardware, running processes, and configuration
  as entries in the filesystem, not just documents. Research: what lives in `/dev` and `/proc`, and
  what "everything is a file" means in practice.
- **A live system** — what is running on your machine right now, and where it differs from what you
  expected before you looked. Research: how to list running processes, and what the kernel has to do
  with them.

## Stack constraints

Whatever Linux environment you set up before this course began (WSL2, a VM, or Linux-in-a-container).
No tool is required beyond what your shell already gives you to inspect kernel, distro, and process
information.

## Deliverable

Written observation notes: a record of what you found when you actually looked under the hood of your
own system — its kernel, its distribution, what's currently running — detailed enough that someone else
could verify the same facts on their own machine.

## Definition of done

- Your notes name your system's actual kernel version and distribution, not generic facts copied from
  documentation.
- Every claim in your notes is backed by real output from your own terminal, not paraphrased from
  memory.
- You can answer, unprompted, "what happens between typing a command and seeing output?" using the
  concepts from this module — this doubles as the course's self-check at the M1–M2 boundary.

## Still open / ask your trainer

- How much detail "observation notes" need — confirm expected length and depth before investing
  significant time.
