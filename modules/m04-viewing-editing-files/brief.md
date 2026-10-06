# Viewing & Editing Files Brief

## Objective

By the end of this stage, the trainee can inspect and search the contents of a file, including one too
large to read top to bottom and one that is actively being written to, without opening it in a GUI.

## Scope

The trainee works with a real log file on the trainee's own system and records the following in the
trainee's own words:

- **Viewing a file** — a small set of tools covers nearly every day-to-day case, and which one fits
  depends on whether the file is short, long, or still growing. Research: how `cat`, `less`, `head`,
  `tail`, and `tail -f` differ, and when each is the appropriate choice.
- **Searching contents** — a pattern pulls the relevant lines out of a file too big to read. Research:
  what `grep` matches, and what its options for case, line numbers, and inverting a match do.
- **Following a live file** — logs are how the behavior of a system is observed, and they grow while
  being watched. Research: how to follow a file as it grows, and how to stop.
- **Editing in the terminal** — configuration on a server is routinely edited with no GUI available.
  Research: how to open, change, save, and exit in the chosen editor, and how to show what changed.

## Stack constraints

The trainee uses the trainee's own environment and a terminal-based editor of the trainee's choice.

## Deliverable

A filtered log excerpt: a real or realistic log file, filtered down to only the lines matching a
pattern the trainee chose, produced using a terminal viewer or search tool rather than an editor's
find function.

## Lab

**Goal.** View, follow, search, and edit files entirely from the terminal.

**You do, in the trainee's own Linux environment.**

1. The trainee views a system file in full, then a page at a time: `cat /etc/os-release`, then
   `less /etc/os-release` (pressing `q` to exit `less`).
2. In a separate terminal or tab, the trainee starts following the system log with
   `tail -f /var/log/syslog`. The trainee leaves it running, triggers a new log line in the original
   terminal (for example `sudo apt update`), and confirms that it appears in the `tail -f` output. The
   trainee stops following with Ctrl+C.
3. The trainee searches the same log for a case-insensitive pattern:
   `grep -i "error" /var/log/syslog`.
4. The trainee creates and edits a file with `nano ~/linux-course/m3/data/raw/notes.txt`, adding two
   lines of text, saving, and exiting.
5. The trainee confirms the file's contents from the command line with
   `cat ~/linux-course/m3/data/raw/notes.txt`.

**You build and capture.** The trainee captures how `cat` and `less` differed, the new line that
appeared in the `tail -f` output, the `grep` result, and the contents of `notes.txt`.

**Clean-up.** The trainee stops `tail -f` with Ctrl+C. The practice file stays under
`~/linux-course/`.

## Definition of done

[ ] DoD-01: The trainee's notes describe how `cat` and `less` behaved differently on `/etc/os-release`
  and when each would be chosen (Lab step 1).
[ ] DoD-02: The pasted `tail -f` output shows the new line produced by the trainee's own
  `sudo apt update`, demonstrating that a file was followed as it grew and then stopped (Lab step 2).
[ ] DoD-03: The `grep -i "error" /var/log/syslog` output contains only lines that match the pattern,
  with no manual trimming (Lab step 3).
[ ] DoD-04: `notes.txt` exists with the two lines written in `nano`, and the pasted `cat` output
  confirms it from the command line (Lab steps 4–5).

## Best practices this stage demonstrates

- Use `less` rather than `cat` for anything longer than a screenful
- Use `tail -f` to watch a log during an active process
- Remember `Esc` and `:wq` when first learning `vim`
- Search logs with `grep -i`, since log messages are inconsistently capitalized

## Still open / ask your trainer

- The source log file is the trainee's choice (a real system log, or one the trainee generates). The
  trainee confirms with the trainer if nothing suitable is available on the system.
