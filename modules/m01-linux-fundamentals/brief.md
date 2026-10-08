# Linux Fundamentals Brief

## Objective

By the end of this stage, the trainee can explain what happens between typing a command and seeing
output, using the trainee's own system as the evidence rather than a diagram from a slide.

## Scope

The trainee investigates the trainee's own system, not the textbook one, and records the following in
the trainee's own words:

- **Kernel, shell, and distro** — what each one does, and how a typed command travels from the shell
  to the kernel and back as output. Research: what a system call is, and why the shell and the
  terminal emulator are two different things.
- **System identity** — which kernel and distribution the system is actually running, and where the
  system reports that about itself. Research: what `uname -a` and `/etc/os-release` each report, and
  why they are not the same fact.
- **The filesystem as an abstraction** — Linux exposes hardware, running processes, and configuration
  as entries in the filesystem, not just documents. Research: what lives in `/dev` and `/proc`, and
  what "everything is a file" means in practice.
- **A live system** — what is running on the machine right now, and where it differs from what was
  expected before looking. Research: how to list running processes, and what the kernel has to do with
  them.

## Stack constraints

The trainee uses the Linux environment set up before this course began (WSL2, a VM, or
Linux-in-a-container). No tool is required beyond what the shell already provides for inspecting
kernel, distro, and process information.

## Deliverable

Written observation notes: a record of what the trainee found when examining the kernel, the
distribution, and the running processes of the trainee's own system, detailed enough that another
person could verify the same facts on their own machine.

## Lab

**Goal.** Examine the trainee's own system and record, from real terminal output, which kernel and
which distribution it is running.

**You do, in the trainee's own Linux environment (the one set up in M0).**

1. The trainee runs `uname -a` and `cat /etc/os-release`, and records the raw output of each.
2. From that output, the trainee identifies the kernel version, the kernel architecture (for example
   `x86_64` or `aarch64`), the distro name, and the distro version.
3. In one or two sentences, the trainee explains what would need to change in step 1's output if the
   trainee switched from the WSL2 path to the Virtual Machine path (or the reverse).

**You build and capture.** The observation notes are the deliverable. The trainee captures the raw
output of both commands, the four facts identified from it, and the explanation for step 3.

**Clean-up.** Nothing to clean up. This lab only reads system information.

## Definition of done

- [ ] DoD-01: The trainee's notes show the raw, pasted output of `uname -a` and `cat /etc/os-release` from
  the trainee's own terminal (Lab step 1), not facts copied from documentation or paraphrased from
  memory.
- [ ] DoD-02: From that output, the notes name the kernel version, the kernel architecture, the distro
  name, and the distro version, and show where in the output each one appears (Lab step 2).
- [ ] DoD-03: The notes explain in one or two sentences what would change in step 1's output if the
  trainee switched between the WSL2 and Virtual Machine paths, showing that kernel and distro are
  independent facts (Lab step 3).
- [ ] DoD-04: Using the output from steps 1–3 as evidence, the trainee can answer unprompted, "what
  happens between typing a command and seeing output?" This also serves as the course's self-check at
  the M1–M2 boundary.

## Best practices this stage demonstrates

- Treat kernel and distro version as the first two facts to check when something behaves differently
  across machines
- Do not assume that distro-specific defaults (package manager, default shell, service manager)
  transfer to another distro
- Keep "the shell" and "the terminal emulator" distinct

## Still open / ask your trainer

- How much detail "observation notes" need. The trainee confirms the expected length and depth with
  the trainer before investing significant time.
