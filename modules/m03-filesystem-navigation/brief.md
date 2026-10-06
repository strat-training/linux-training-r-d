# Filesystem Navigation Brief

## Objective

By the end of this stage, the trainee can navigate and build directory structures anywhere on the
filesystem without getting lost, and can locate a file whose exact location is not known in advance.
This removes the course's second failure mode: "I can't find the log / config / data file."

## Scope

The trainee explores the trainee's own filesystem, builds a structure of the trainee's own, and
records the following in the trainee's own words:

- **The Filesystem Hierarchy Standard (FHS)** — Linux has one root (`/`) tree, not drive letters, and a
  convention for what lives where (configuration, logs, user files, installed programs) so that almost
  any distro can be navigated. Research: what `/etc`, `/var`, `/home`, and `/usr` are each for, and why
  the FHS is a convention rather than a rule the kernel enforces.
- **Absolute vs. relative paths** — a path can be anchored at the root or at the current location, and
  knowing which one is in use is what prevents getting lost. Research: what `.` and `..` mean, and what
  `~` expands to.
- **Building structures in one step** — a nested directory tree can be created in a single command
  instead of stepping into each level. Research: the option that creates missing parent directories.
- **Searching instead of browsing** — a file that cannot be placed can be found by name or pattern.
  Research: how `find` differs from `locate`, and how to match a name pattern.

## Stack constraints

The trainee uses the trainee's own environment. No tool beyond core navigation and search utilities is
required.

## Deliverable

A nested project directory structure, multiple levels deep, created and populated in a way that
reflects a deliberate organizational scheme rather than a flat dump, plus evidence that the trainee
located a file in it using search rather than manual browsing.

## Lab

**Goal.** Move around the filesystem, build a nested directory tree in one step, and find files by
search instead of browsing.

**You do, in the trainee's own Linux environment.**

1. From the home directory, the trainee confirms the current location with `pwd`, navigates to
   `/var/log` with `cd /var/log`, confirms again with `pwd`, and returns with `cd ~`.
2. The trainee creates a nested practice tree: `mkdir -p ~/linux-course/m3/data/raw` and
   `mkdir -p ~/linux-course/m3/data/processed`.
3. The trainee lists the tree that was created, including hidden files, with
   `ls -la ~/linux-course/m3`.
4. The trainee searches `/etc` for every entry ending in `.conf` with `find /etc -name "*.conf"`, and
   records how many results are returned.
5. From `~/linux-course/m3/data/raw`, the trainee references the `processed` sibling directory with a
   relative path: `cd ~/linux-course/m3/data/raw`, then `ls ../processed`.

**You build and capture.** The trainee captures the `pwd` output before and after the move, the
`ls -la` listing of the tree, the count of `.conf` results, and the relative-path command with its
output.

**Clean-up.** Nothing to clean up. The practice tree stays under `~/linux-course/`.

## Definition of done

[ ] DoD-01: The trainee's pasted `pwd` output shows the location before and after `cd /var/log`, and
  that `cd ~` returned to the home directory (Lab step 1).
[ ] DoD-02: The tree under `~/linux-course/m3` is at least three levels deep and was created with
  `mkdir -p`, without stepping into each new level one at a time. The pasted `ls -la` output shows it
  (Lab steps 2–3).
[ ] DoD-03: The notes record how many results `find /etc -name "*.conf"` returned, taken from the
  trainee's own output, and show that files were located by search rather than by visual browsing (Lab
  step 4).
[ ] DoD-04: `ls ../processed` ran from `~/linux-course/m3/data/raw` using a relative path, and the
  trainee can state the absolute path of `processed` without running a command to check (Lab step 5).

## Best practices this stage demonstrates

- Use `pwd` liberally when learning to navigate
- Prefer `mkdir -p` when creating nested paths
- Keep practice files in the home directory, not in `/etc`, `/usr`, or other system directories
- Use `find` to locate files by name or attribute, and `grep` for file contents

## Still open / ask your trainer

- How deep or complex the structure needs to be beyond the minimum is a judgment call. The trainee
  asks the trainer if the design is borderline.
