# Viewing & Editing Files Brief

**Week:** 2 of 6 (shared with M3, 5–6 hrs total) · **Builds on:** M3 · **Feeds into:** M5, M8

## Objective

You can inspect and search the contents of a file — including one too large to read top to bottom, and
one actively being written to — without opening it in a GUI.

## Scope

You work with a real log file on your own system. Find out, and write down in your own words:

- **Viewing a file** — a small set of tools covers nearly every day-to-day case, and which one fits
  depends on whether the file is short, long, or still growing. Research: how `cat`, `less`, `head`,
  `tail`, and `tail -f` differ, and when you'd reach for each.
- **Searching contents** — a pattern lets you pull the lines you care about out of a file too big to
  read. Research: what `grep` matches, and what its options for case, line numbers, and inverting a
  match do.
- **Following a live file** — logs are how you observe what a system did, and they grow while you
  watch. Research: how to follow a file as it grows, and how to stop.
- **Editing in the terminal** — configuration on a server is routinely edited with no GUI available.
  Research: how to open, change, save, and exit in your chosen editor, and how to show what changed.

## Stack constraints

Your own environment; a terminal-based editor of your choice.

## Deliverable

A filtered log excerpt: a real or realistic log file, filtered down to only the lines matching a pattern
you chose, produced using a terminal viewer/search tool — not an editor's find function.

## Definition of done

- The excerpt contains only lines that actually match your stated pattern, with no manual trimming.
- You have demonstrated following a file as it grows, not just viewing a static file.
- You have made at least one edit to a file directly in the terminal and can show the diff.

## Still open / ask your trainer

- The source log file is your choice (a real system log, or one you generate) — confirm with your
  trainer if nothing suitable is available on your system.
