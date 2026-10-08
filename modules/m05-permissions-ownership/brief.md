# Users, Permissions, Ownership Brief

## Objective

By the end of this stage, the trainee can reason about and construct a permission scheme deliberately,
rather than reaching for a "make it work" shortcut when access fails. This removes the course's third
failure mode: "Permission denied" / "works on my machine."

## Scope

The trainee designs and proves a permission scheme on the trainee's own system and records the
following in the trainee's own words:

- **Users and groups** — every file and process belongs to a user and a group, and that identity is
  what Linux checks when something asks for access. Research: how to see which user and groups the
  current account has, and where accounts and groups are defined on the system.
- **Permission bits** — each file carries read, write, and execute permissions for its owner, its
  group, and everyone else, with no separate access-control system on top. Research: how to read the
  output of `ls -l`, and how the symbolic and octal notations map to each other.
- **Ownership vs. permission** — who owns a file and what the owner is allowed to do with it are two
  separate settings, and changing one does not change the other. Research: what `chown` and `chmod`
  each change, and why a directory's execute bit behaves differently from a file's.
- **Elevated privilege** — `sudo` and root can bypass the model, which is exactly why reaching for
  them to make an error go away is a habit to resist. Research: what `sudo` actually does, and when
  elevated privilege is genuinely warranted.

## Stack constraints

The trainee uses the trainee's own environment, with at least one additional user or group available
to demonstrate a real restriction, not just the trainee's own default account.

## Deliverable

An access-restricted shared directory: a directory with a permission and ownership scheme designed by
the trainee, where access is deliberately restricted for one identity and open for another, with both
outcomes demonstrated and not just configured.

## Lab

**Goal.** Read and change permission bits on real files, observe what `sudo` actually asks for, and
state what an octal mode means.

**You do, in the trainee's own Linux environment.**

1. The trainee creates a practice file and inspects its default permissions:
   `mkdir -p ~/linux-course/m5`, `cd ~/linux-course/m5`, `touch shared-report.txt`,
   `ls -l shared-report.txt`.
2. The trainee restricts the file so that only the owner can read and write it, and confirms the
   change: `chmod 600 shared-report.txt`, `ls -l shared-report.txt`.
3. The trainee creates a second file, makes it executable by the owner only, and confirms:
   `touch run.sh`, `chmod u+x run.sh`, `ls -l run.sh`.
4. The trainee uses `sudo` to view a file that only root can read by default, confirming that `sudo`
   prompts for the trainee's own password and not a separate root password: `sudo cat /etc/shadow`.
5. In the trainee's own words, the trainee writes down what `750` means in terms of owner, group, and
   other read-write-execute bits, before checking the answer against the course's Concepts section.

**You build and capture.** The trainee captures the `ls -l` output before and after each `chmod`, the
`sudo` prompt behavior (with password hashes from `/etc/shadow` redacted as the trainer directs), and
the trainee's own-words explanation of `750`.

**Clean-up.** Nothing to clean up. The practice files stay under `~/linux-course/`.

## Definition of done

- [ ] DoD-01: The trainee's pasted `ls -l shared-report.txt` output before and after `chmod 600` shows the
  change, and the notes read the permission string for the owner, group, and other positions (Lab
  steps 1–2).
- [ ] DoD-02: The pasted `ls -l run.sh` output shows the execute bit set for the owner only, and the notes
  state what `u+x` changed and what it left alone (Lab step 3).
- [ ] DoD-03: The notes show that `sudo cat /etc/shadow` prompted for the trainee's own password rather
  than a root password, with the password hashes redacted (Lab step 4).
- [ ] DoD-04: The trainee's own-words explanation of `750` was written before it was checked against the
  Concepts section, and states what owner, group, and other can each do (Lab step 5).
- [ ] DoD-05: No step in the notes uses a blanket `777` "open everything" shortcut; every mode set is the
  least permissive one that still works.

## Best practices this stage demonstrates

- Use `sudo <command>` for the one command that needs it, not `sudo su`
- Default to the least permissive setting that still works
- Never treat `777` as a quick fix for a permission error
- Check both ownership (`chown`) and permission bits (`chmod`) when debugging "permission denied"

## Still open / ask your trainer

- What the shared directory is "for" is left to the trainee. The trainee picks something concrete
  rather than a placeholder scenario, and confirms with the trainer what counts as a real test of the
  restriction if unsure.
