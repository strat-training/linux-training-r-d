# CLI Basics Brief

**Week:** 1 of 6 (shared with M1, 5–6 hrs total) · **Builds on:** M1 · **Feeds into:** M3

## Objective

You can compose commands together — not just run them one at a time — to produce and reshape output
exactly how you want it.

## Scope

You work with real commands on your own system. Find out, and write down in your own words:

- **Command anatomy** — nearly every command has the same shape (a name, options that change its
  behavior, and arguments it acts on), and that shared shape is why one habit works across thousands of
  tools. Research: the difference between a short flag and a long flag, and what an argument is.
- **Pipes** — one command's output can become another's input without an intermediate file, so small
  single-purpose tools combine into something larger. Research: what `|` does, and how to read a
  pipeline left to right as a sequence of transformations.
- **Redirection** — output can go to a file instead of the screen, and whether it overwrites or appends
  decides whether you keep what was there. Research: what `>` and `>>` each do, and what is lost if you
  pick the wrong one.
- **Built-in help** — `man`, `--help`, and tab-completion let you work without memorizing every flag,
  which matters on a server with no internet. Research: how to navigate and search a man page, and what
  Tab does when more than one match exists.

## Stack constraints

Your own Linux environment from M1; no tools beyond the shell's own built-ins.

## Deliverable

A redirected file listing: a file produced by piping and/or redirecting the output of at least one
command into another, showing deliberate control over where output goes.

## Definition of done

- The deliverable file exists on disk and its contents can be reproduced by re-running your documented
  command.
- Your notes distinguish when you used overwrite vs. append redirection, and why.
- You can explain, unprompted, something `man` or tab-completion told you that you didn't already know.

## Still open / ask your trainer

- The exact command/data to filter is your choice — pick something real on your own system rather than
  a toy example, and confirm with your trainer if you're unsure what counts.
