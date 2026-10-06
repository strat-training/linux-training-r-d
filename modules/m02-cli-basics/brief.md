# CLI Basics Brief

## Objective

By the end of this stage, the trainee can compose commands together, not just run them one at a time,
to produce and reshape output exactly as intended.

## Scope

The trainee works with real commands on the trainee's own system and records the following in the
trainee's own words:

- **Command anatomy** — nearly every command has the same shape (a name, options that change its
  behavior, and arguments it acts on), and that shared shape is why one habit works across thousands of
  tools. Research: the difference between a short flag and a long flag, and what an argument is.
- **Pipes** — one command's output can become another's input without an intermediate file, so small
  single-purpose tools combine into something larger. Research: what `|` does, and how to read a
  pipeline left to right as a sequence of transformations.
- **Redirection** — output can go to a file instead of the screen, and whether it overwrites or appends
  decides whether the earlier content is kept. Research: what `>` and `>>` each do, and what is lost
  when the wrong one is chosen.
- **Built-in help** — `man`, `--help`, and tab-completion allow work without memorizing every flag,
  which matters on a server with no internet. Research: how to navigate and search a man page, and what
  Tab does when more than one match exists.

## Stack constraints

The trainee uses the Linux environment from M1. No tool beyond the shell's own built-ins is required.

## Deliverable

A redirected file listing: a file produced by piping and/or redirecting the output of at least one
command into another, showing deliberate control over where output goes.

## Lab

**Goal.** Compose commands with redirection, pipes, and the built-in help, and keep evidence of what
each one did.

**You do, in the trainee's own Linux environment.**

1. The trainee creates a working directory for the course, then creates a file and adds two lines to
   it using redirection, one with `>` and one with `>>`: `mkdir -p ~/linux-course/m2`,
   `cd ~/linux-course/m2`, `echo "line one" > practice.txt`, `echo "line two" >> practice.txt`.
2. The trainee confirms that both lines are present with `cat practice.txt`.
3. The trainee uses a pipe to filter the output of `ls -l` on `/etc` down to only the entries
   containing `.conf`: `ls -l /etc | grep ".conf"`.
4. The trainee opens the manual page for `grep` with `man grep`, closes it, then gets the same
   command's short help with `grep --help`.
5. The trainee practices tab-completion by typing `cd ~/linux-cou` and pressing Tab to complete the
   rest of the path.

**You build and capture.** The trainee captures the contents of `practice.txt`, the output of the
piped command, one thing that `man grep` or `grep --help` revealed, and the path that Tab completed.

**Clean-up.** Nothing to clean up. The practice files stay under `~/linux-course/`.

## Definition of done

[ ] DoD-01: `practice.txt` exists on disk and the trainee's pasted `cat` output shows both lines (Lab
  steps 1–2). Re-running the documented commands reproduces the file.
[ ] DoD-02: The trainee's notes state which line was written with `>` and which with `>>`, and what
  would have been lost if `>` had been used for both (Lab step 1).
[ ] DoD-03: The pasted output of `ls -l /etc | grep ".conf"` shows only matching entries, and the
  trainee can read the pipeline left to right as two steps: what `ls -l` produces and what `grep` keeps
  (Lab step 3).
[ ] DoD-04: The notes show that the trainee opened both `man grep` and `grep --help`, and state
  something that one of them revealed which the trainee did not already know (Lab step 4).
[ ] DoD-05: The notes show what Tab-completion did with the partial path `~/linux-cou` (Lab step 5).

## Best practices this stage demonstrates

- Prefer `>>` over `>` when appending to a log or an existing file
- Read a pipeline left to right as a sequence of transformations
- Check `man` or `--help` before guessing a flag's behavior

## Still open / ask your trainer

- The exact command and data to filter are the trainee's choice. The trainee picks something real on
  the trainee's own system rather than a toy example, and confirms with the trainer what counts if
  unsure.
