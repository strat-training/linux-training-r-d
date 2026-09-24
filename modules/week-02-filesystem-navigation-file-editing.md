# Week 2 — Filesystem Navigation & Viewing/Editing Files (M3–M4)

## Objective

This module gives the trainee a working mental map of the Linux
filesystem — its standard layout and how paths resolve — and the ability
to inspect and edit file contents directly from the terminal, the two
skills every later module's labs assume are already fluent.

## Topics

- The Filesystem Hierarchy Standard (FHS): what lives under `/`,
  `/home`, `/etc`, `/var`, `/usr`, and why
- Absolute vs. relative paths, and how `pwd`/`cd` resolve them
- Creating and navigating directory structures: `ls`, `mkdir -p`, `find`
- Viewing file contents: `cat`, `less`, `tail -f`
- Searching file contents: `grep`
- In-place editing: `nano` and `vim`, at a beginner level

## M3: Filesystem Navigation — FHS, Paths, and Directory Structure

- **Learning Objective:** By the end of this module, the trainee can
  navigate to any location in the filesystem using both absolute and
  relative paths, create a nested directory structure in one command,
  and explain what at least four top-level FHS directories are for.

- **Core Idea:** Linux organizes everything under a single root (`/`)
  directory tree — there's no per-drive-letter system like `C:\` on
  Windows. The Filesystem Hierarchy Standard (FHS) is the convention
  most distros follow for what lives where, so a trainee who understands
  the FHS on Ubuntu can find their way around almost any Linux system.

- **Why It Matters:**
  - M1's "everything is a file" idea only becomes useful once the
    trainee can actually find things — configuration in `/etc`,
    variable/log data in `/var`, and user files in `/home`.
  - Every later lab (M6's package installs, M8's cron logs, M9's
    scripts, M13's `.gitignore`'d credentials) references specific FHS
    locations by convention; not knowing the layout turns every lab into
    a scavenger hunt.
  - "I can't find the log / config / data file" is explicitly the
    failure mode this course's Layer 2 exists to remove (per the arch
    doc's layer table) — this module is where that failure mode gets
    addressed directly.

- **How It Works**

  - **Concepts**
    - **The Filesystem Hierarchy Standard (FHS)** — a convention (not a
      strict rule enforced by the kernel) that most distros, including
      Ubuntu, follow closely:
      - `/` — the root of the entire filesystem; every other directory
        is a subdirectory of it.
      - `/home` — personal directories for each non-root user (e.g.
        `/home/alice`).
      - `/etc` — system-wide configuration files, almost always plain
        text.
      - `/var` — variable data that changes at runtime: logs
        (`/var/log`), spool files, caches.
      - `/usr` — the bulk of installed user programs and their
        supporting files (not "user" in the personal-directory sense —
        a historical naming quirk).
      - `/tmp` — temporary files, typically cleared on reboot.
    - **Absolute vs. relative paths** — an absolute path starts from `/`
      and always points to the same location regardless of the
      trainee's current directory (e.g. `/home/alice/notes.txt`); a
      relative path is resolved starting from the current working
      directory (e.g. `notes.txt` or `../notes.txt`).
    - **Core navigation commands:**

      ```bash
      pwd
      cd /var/log
      cd ..
      ls -la
      ```

    - **`mkdir -p`** creates a full nested directory path in one
      command, creating any missing parent directories along the way
      instead of failing if they don't already exist:

      ```bash
      mkdir -p ~/linux-course/m3/data/raw
      ```

    - **`find`** searches a directory tree for files/directories
      matching a condition:

      ```bash
      find /etc -name "*.conf"
      ```

  - **Best Practices**
    - Use `pwd` liberally when first learning to navigate — "lost in the
      filesystem" is a normal early experience, and `pwd` is the fastest
      way out of it.
    - Prefer `mkdir -p` over plain `mkdir` when creating any nested
      path — plain `mkdir` fails outright if an intermediate directory
      doesn't exist yet.
    - Avoid working directly inside `/etc`, `/usr`, or other system
      directories for personal practice files; keep course work inside
      the home directory (e.g. `~/linux-course/`) to avoid accidentally
      modifying system configuration.
    - Use `find` for locating files by name or attribute across a tree;
      reach for `grep` (M4) when the question is about file *contents*
      instead.

  - **Real-World Example**
    - A data engineer troubleshooting "the pipeline can't find its
      config" almost always resolves it by checking whether the script
      assumed an absolute path but was actually run from a different
      working directory, producing a relative-path mismatch — exactly
      the absolute-vs-relative distinction this module teaches.

- **Supplemental Reading**
  - [Linux Filesystem Explained](https://www.youtube.com/watch?v=ISJ44S5sZu8) —
    video overview of how the Linux filesystem is organized and why.
  - [Classic SysAdmin: The Linux Filesystem Explained](https://www.linuxfoundation.org/blog/blog/classic-sysadmin-the-linux-filesystem-explained) (Linux Foundation) —
    covers the FHS layout in more depth.
  - [Linux File System](https://www.geeksforgeeks.org/linux-unix/linux-file-system/) (GeeksforGeeks) —
    reference-style rundown of top-level directories.
  - [Understanding the Linux File System: A Beginner-Friendly Guide](https://medium.com/@aartikale427/understanding-the-linux-file-system-a-beginner-friendly-guide-b9a517a00762) (Medium) —
    beginner-oriented walkthrough of the same FHS concepts.
  - [Linux File System Structure Explained: From / to /usr](https://www.youtube.com/watch?v=roES8iAaJEM) —
    video walking through the tree from `/` down to `/usr` specifically.
  - [Basic Linux Navigation and File Management](https://www.digitalocean.com/community/tutorials/basic-linux-navigation-and-file-management) (DigitalOcean) —
    hands-on tutorial covering `pwd`/`cd`/`ls`/`mkdir`-style navigation.
  - [Navigating the Filesystem in the Linux Terminal](https://www.redhat.com/en/blog/navigating-filesystem-linux-terminal) (Red Hat) —
    covers absolute vs. relative paths and core navigation commands.
  - [File Management in Linux](https://www.geeksforgeeks.org/linux-unix/file-management-in-linux/) (GeeksforGeeks) —
    covers file/directory creation, including `mkdir`; does not
    specifically cover `find` in depth, so `find`'s usage above is
    written from general knowledge rather than this source.

## M4: Viewing & Editing Files — Streams, Search, and In-Place Editing

- **Learning Objective:** By the end of this module, the trainee can
  view a file's contents in full or a page at a time, follow a growing
  log file live, search file contents for a pattern, and make a small
  edit to a file using a terminal-based editor.

- **Core Idea:** Once a trainee can navigate to a file (M3), the next
  skill is reading and changing what's inside it — without leaving the
  terminal or needing a GUI text editor. A small set of tools (`cat`,
  `less`, `tail -f`, `grep`, and a terminal editor) covers nearly every
  day-to-day case.

- **Why It Matters:**
  - Log files (`/var/log`, and later the pipeline's own logs from the
    capstone) are the primary way a data engineer observes what a
    system did — M12's observability work is a direct extension of the
    `grep`/`tail -f` skills built here.
  - Editing configuration files directly on a server (no GUI available)
    is routine in production Linux work; basic `nano`/`vim` fluency
    removes a dependency on a graphical environment that may not exist
    on a remote server.
  - This module produces the "filtered log excerpt" output artifact
    called for in the arch doc's per-module component table (§4) — a
    direct, gradeable checkpoint of the skill.

- **How It Works**

  - **Concepts**
    - **Viewing a file in full** — `cat` prints the entire file to the
      terminal at once; fine for short files, unwieldy for long ones:

      ```bash
      cat /etc/os-release
      ```

    - **Viewing a file a page at a time** — `less` opens a file for
      scrollable, searchable viewing without loading the whole thing
      into the terminal's scrollback:

      ```bash
      less /var/log/syslog
      ```

      Inside `less`: arrow keys or Page Up/Down to scroll, `/pattern` to
      search forward, `q` to quit.

    - **Following a growing file live** — `tail -f` prints new lines as
      they're appended; the standard way to watch a log file in real
      time:

      ```bash
      tail -f /var/log/syslog
      ```

    - **Searching file contents** — `grep` prints lines matching a
      pattern:

      ```bash
      grep "error" /var/log/syslog
      grep -i "error" /var/log/syslog
      ```

      `-i` makes the match case-insensitive; combined with M2's piping,
      `grep` also filters other commands' output, not just files.

    - **In-place editing** — `nano` is a simple, beginner-friendly
      terminal editor (on-screen shortcut hints at the bottom); `vim` is
      more powerful but has a steeper learning curve (it starts in a
      mode where keystrokes are commands, not text, until `i` is pressed
      to insert):

      ```bash
      nano notes.txt
      ```

  - **Best Practices**
    - Reach for `less` instead of `cat` for anything longer than a
      screenful — `cat`-ing a large file floods the terminal and makes
      the actually-relevant lines scroll past.
    - Use `tail -f` (not repeated manual `cat`s) to watch a log during an
      active process — this is the pattern M8's cron jobs and M11's
      `kubectl logs` build on later.
    - When first learning `vim`, remember `Esc` returns to command mode
      and `:wq` saves and quits — the single most common beginner "how
      do I get out of this" moment.
    - Combine `grep` with `-i` by default when searching logs for
      something like "error" or "fail" — real-world log messages are
      inconsistently capitalized.

  - **Real-World Example**
    - A common production debugging sequence is: `tail -f` a service's
      log while reproducing an issue, then once the failing lines
      appear, `grep` the full log file for that same pattern to see
      every past occurrence and whether it's new or recurring — the
      exact `tail -f` + `grep` combination this module teaches, used
      together rather than in isolation.

- **Supplemental Reading**
  - [Linux Basics Part 5: Viewing and Editing Files from the Terminal](https://dev.to/alkesh009/linux-basics-part-5-viewing-and-editing-files-from-the-terminal-1g0k) (dev.to) —
    this is the only cited source for this module. It's part 5 of a
    beginner series and, per its title, covers viewing and editing files
    from the terminal at an introductory level; it should not be
    assumed to cover every command above in equal depth — in
    particular, `tail -f`'s live-following behavior and `vim`'s modal
    editing are common gaps in beginner-level "viewing and editing"
    articles. Cross-check `man tail` and `man vim`/`vimtutor` directly
    if either behaves differently than expected.

## Hands-on lab

### M3 — Filesystem Navigation Lab

1. From the home directory, confirm the current location, then navigate
   to `/var/log` and back:

   ```bash
   pwd
   cd /var/log
   pwd
   cd ~
   ```

2. Create a nested practice directory tree in one command:

   ```bash
   mkdir -p ~/linux-course/m3/data/raw
   mkdir -p ~/linux-course/m3/data/processed
   ```

3. List the tree just created, including hidden files:

   ```bash
   ls -la ~/linux-course/m3
   ```

4. Search `/etc` for every entry ending in `.conf`, and record how many
   results are returned:

   ```bash
   find /etc -name "*.conf"
   ```

5. From `~/linux-course/m3/data/raw`, reference the `processed` sibling
   directory using a relative path (not an absolute one):

   ```bash
   cd ~/linux-course/m3/data/raw
   ls ../processed
   ```

### M4 — Viewing & Editing Files Lab

1. View a system file in full, then the same file a page at a time:

   ```bash
   cat /etc/os-release
   less /etc/os-release
   ```

   (Press `q` to exit `less`.)

2. In a separate terminal or tab, start following the system log live:

   ```bash
   tail -f /var/log/syslog
   ```

   Leave it running, then in the original terminal, trigger a new log
   line (for example, run `sudo apt update`, which logs activity), and
   confirm it appears in the `tail -f` output. Stop following with
   `Ctrl+C`.

3. Search the same log for a case-insensitive pattern:

   ```bash
   grep -i "error" /var/log/syslog
   ```

4. Create and edit a file with `nano`: add two lines of text, save, and
   exit.

   ```bash
   nano ~/linux-course/m3/data/raw/notes.txt
   ```

5. Confirm the file's contents from the command line:

   ```bash
   cat ~/linux-course/m3/data/raw/notes.txt
   ```

## Lab exercise

The trainee produces a short written summary, saved alongside their
course notes, containing:

- The FHS purpose of `/etc`, `/var`, `/home`, and `/usr`, in the
  trainee's own words.
- The exact `find` command used in the M3 lab and how many `.conf`
  entries it found.
- Whatever line(s) matched the `grep -i "error"` search in the M4 lab
  (or a note that none matched, which is a valid and expected result on
  a clean system).
- One sentence on when they'd reach for `cat` vs. `less` vs. `tail -f`,
  based on this module's Best Practices.

## WSL2 vs macOS

| Behavior | WSL2 | macOS |
|---|---|---|
| `/var/log/syslog` availability | Present by default on Ubuntu WSL2 | Not present in the same form on Darwin — macOS uses the unified logging system (`log show`) instead; inside a Linux VM/container (per M0), a standard `/var/log/syslog` is present as usual |
| Default terminal editor available out of the box | `nano` and `vim` both preinstalled on Ubuntu | Terminal.app ships `vim`/`vi`; `nano` is present but often an older BSD build with fewer default keybindings — irrelevant once working inside a real Linux VM/container per M0 |

## Checkpoint (self-assessed)

- [ ] I can state the purpose of `/etc`, `/var`, `/home`, and `/usr`
      without looking it up.
- [ ] I can navigate to a location using an absolute path and a
      different location using a relative path, and explain the
      difference.
- [ ] I can create a nested directory structure with a single
      `mkdir -p` command.
- [ ] I can use `find` to locate files by name pattern in a directory
      tree.
- [ ] I can explain when to use `cat` vs. `less` vs. `tail -f` to view a
      file.
- [ ] I can use `grep` (with `-i` when needed) to search a file's
      contents for a pattern.
- [ ] I can open, edit, save, and exit a file using `nano`.
- [ ] I produced the filtered log excerpt called for by this module's
      lab.
