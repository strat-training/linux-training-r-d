# Week 1 — Linux Fundamentals & CLI Basics (M1–M2)

## Objective

This module builds the trainee's foundational mental model of what Linux
is and how to interact with it via the command line — the shell, kernel,
and basic command syntax that every subsequent module assumes is already
second nature.

## Topics

- What Linux is: kernel, shell, distributions, and the "everything is a
  file" philosophy
- Core system components: kernel, shell, file system, and how they relate
- Command syntax: command, options/flags, arguments
- Piping and redirection: `|`, `>`, `>>`
- Getting help: `man`, `--help`, tab-completion

## M1: Linux Fundamentals — Kernel, Shell, and Distributions

- **Learning Objective:** By the end of this module, the trainee can
  explain, in their own words, what happens between typing a command and
  seeing output — i.e., the roles of the kernel, shell, and a
  distribution — and can identify their own distribution and kernel
  version from the command line.

- **Core Idea:** Linux is not one thing — it's a kernel (the core program
  controlling hardware) wrapped by a shell (the program that reads and
  executes commands) and packaged by a distribution (a specific bundle of
  kernel + tools + package manager + defaults, e.g. Ubuntu, Debian,
  Fedora). Understanding this separation demystifies why different
  distros can "feel" different while still being "Linux" underneath.

- **Why It Matters:**
  - Every later module's commands assume this three-layer model: knowing
    whether an issue is a kernel limitation, a shell quirk, or a distro
    packaging choice narrows debugging dramatically.
  - Data infrastructure (servers, containers, cloud instances) runs
    almost exclusively on Linux; recognizing kernel version and distro is
    a first troubleshooting step for any of these ("does this feature
    exist in this kernel/distro?").
  - Distinguishes "Linux-like" experiences (e.g. macOS's Darwin/BSD
    userland, covered in M0) from a genuine Linux kernel — a distinction
    M0 already relied on trainees eventually understanding.

- **How It Works**

  - **Concepts**
    - **Kernel** — the core program that manages a computer's hardware
      and mediates every other program's access to it: memory
      management, process scheduling (CPU time allocation), device
      drivers, and the filesystem interface. It loads first at boot and
      stays resident until shutdown.
    - **Shell** — the program that reads a trainee's typed commands,
      interprets them, and asks the kernel to execute them. Bash (Bourne
      Again SHell) is the default shell on most Linux distributions and
      the shell this course assumes throughout.
    - **Distribution ("distro")** — a specific packaging of the Linux
      kernel plus a set of default tools, a package manager, and
      configuration conventions. Ubuntu and Debian use `apt`; Fedora and
      RHEL use `dnf`/`yum`; Arch uses `pacman`. This course standardizes
      on Ubuntu LTS, set up in M0.
    - **"Everything is a file"** — Linux represents hardware devices,
      running processes, and configuration all as entries in the
      filesystem (e.g., `/dev`, `/proc`), not just user documents — a
      unifying design principle that M3–M4 build on directly.
    - Checking identity from the command line:

      ```bash
      uname -a
      cat /etc/os-release
      ```

      `uname -a` reports the kernel name, version, and architecture;
      `/etc/os-release` reports the distro name and version — the same
      two commands verified in M0's checkpoint, now understood rather
      than just run.

  - **Best Practices**
    - Treat kernel and distro version as the first two facts to check
      when something behaves unexpectedly across machines — "works on my
      machine" is frequently a kernel or distro version mismatch, not a
      code bug.
    - Don't assume distro-specific defaults (package manager, default
      shell, service manager) transfer to a different distro; this
      course's commands assume Ubuntu LTS + `apt` + Bash + `systemd`, set
      up in M0.
    - Keep "the shell" (Bash) and "the terminal emulator" (the window the
      trainee types into) distinct — they're often conflated but serve
      different jobs. A terminal emulator only displays and forwards
      keystrokes to whatever shell is running inside it.

  - **Real-World Example**
    - A data engineer debugging "the cron job runs fine locally but not
      on the server" often finds the server runs an older kernel or a
      different default shell (`dash` instead of `bash`), silently
      changing how a script's syntax is interpreted — exactly the kind of
      gap this module's mental model is meant to prevent.

- **Supplemental Reading**
  - [What Is Linux? From Kernel to IoT, DevOps, & Supercomputers](https://www.youtube.com/watch?v=H8Q64gHNp6Y) —
    video overview of the kernel/shell/distro model and where Linux runs
    in practice; covers the conceptual model this module teaches, not
    hands-on commands.

## M2: CLI Basics — Command Syntax, Pipes, and Redirection

- **Learning Objective:** By the end of this module, the trainee can
  construct a command with options and arguments from memory, chain
  commands together with pipes, and redirect output to a file, and can
  find help for an unfamiliar command without leaving the terminal.

- **Core Idea:** Nearly every Linux command follows the same shape —
  `command [options] [arguments]` — and commands can be composed into
  pipelines, where one command's output becomes another's input.
  Mastering this composition is what makes the command line more powerful
  than any single fixed tool.

- **Why It Matters:**
  - Every hands-on lab from here forward is expressed as commands with
    options and arguments; misreading command syntax is one of the most
    common early stumbling blocks.
  - Piping and redirection are the mechanism M4 (viewing/filtering
    files), M9 (shell scripting), and M12 (observability/log pipelines)
    all build on directly — this module is where that muscle memory
    starts.
  - Tab-completion and `man`/`--help` reduce reliance on memorization or
    external search, which matters once a trainee is working on a server
    with no internet access.

- **How It Works**

  - **Concepts**
    - **Command anatomy** — a command name, followed by options (flags
      that modify behavior, e.g. `-l`, `--long`), followed by arguments
      (the target of the command, e.g. a filename):

      ```bash
      ls -l /home
      ```

    - **Redirection** — `>` sends a command's output to a file,
      overwriting it; `>>` appends instead of overwriting:

      ```bash
      echo "first line" > notes.txt
      echo "second line" >> notes.txt
      ```

    - **Piping** — `|` sends one command's output directly into another
      command's input, without an intermediate file:

      ```bash
      ls -l /etc | grep ".conf"
      ```

    - **Getting help** — `man <command>` opens a command's full manual
      page; most commands also support `<command> --help` for a shorter
      summary:

      ```bash
      man ls
      ls --help
      ```

    - **Tab-completion** — pressing Tab while typing a command or path
      completes it automatically, or lists possibilities if more than
      one match exists — reduces typos and speeds up navigation once
      paths get longer (relevant from M3 onward).

  - **Best Practices**
    - Prefer `>>` over `>` whenever appending to a log or existing file —
      `>` silently destroys the file's previous contents with no
      confirmation prompt.
    - Read a pipeline left to right as a sequence of transformations on
      data, not as one command — this habit is what makes M9's more
      complex pipelines (`grep` into `awk` into `sort`) tractable later.
    - Check `man`/`--help` before guessing a flag's behavior or searching
      externally, especially once working on a server without internet
      access.

  - **Real-World Example**
    - A data engineer inspecting a large log file for errors typically
      chains several small, single-purpose commands with pipes (filter
      lines, extract a column, count occurrences) rather than writing a
      custom program — this "small tools composed together" habit,
      introduced here, is the same one M12's observability reports rely
      on.

- **Supplemental Reading**
  - **Note:** No cited source material exists for this specific topic
    (command syntax, pipes, redirection, `man`, tab-completion) in the
    research gathered for this course. The Concepts, Best Practices, and
    Real-World Example above are written from general knowledge, not
    from a cited source.

## Hands-on lab

### M1 — Linux Fundamentals Lab

1. Run the following inside the Linux environment set up in M0, and
   record the raw output of each:

   ```bash
   uname -a
   cat /etc/os-release
   ```

2. From the output, identify: kernel version, kernel architecture (e.g.
   `x86_64` or `aarch64`), distro name, and distro version.

3. In one or two sentences, explain what would need to change in step 1's
   output if the trainee switched from the WSL2 path to the Virtual
   Machine path (or vice versa) — this checks that "distro" and "kernel"
   are understood as independent facts, not a single fact.

### M2 — CLI Basics Lab

1. Create a working directory for this course, then create a file and
   add two lines to it using redirection (one `>`, one `>>`):

   ```bash
   mkdir -p ~/linux-course/m2
   cd ~/linux-course/m2
   echo "line one" > practice.txt
   echo "line two" >> practice.txt
   ```

2. Confirm both lines are present:

   ```bash
   cat practice.txt
   ```

3. Use a pipe to filter the output of `ls -l` on `/etc` down to only
   entries containing `.conf`:

   ```bash
   ls -l /etc | grep ".conf"
   ```

4. Open the manual page for `grep`, close it, then get the same command's
   short help instead:

   ```bash
   man grep
   grep --help
   ```

5. Practice tab-completion by typing `cd ~/linux-cou` and pressing Tab to
   auto-complete the rest of the path.

## Lab exercise

The trainee produces a short written summary, saved alongside their
course notes, containing:

- The kernel version, architecture, distro name, and distro version
  identified in the M1 lab.
- The exact command used to filter `/etc` for `.conf` entries, and how
  many lines it returned.
- One example of a mistake `>` could cause if used carelessly (in the
  trainee's own words), and how `>>` avoids it.

## WSL2 vs macOS

| Behavior | WSL2 | macOS |
|---|---|---|
| Default shell | Bash (Ubuntu default) | Zsh (default since Catalina); Bash is available but outdated (3.x) unless upgraded via Homebrew |
| `man` page availability | Full man pages installed by default with Ubuntu | Full BSD-flavored man pages available by default, but describe BSD tool behavior, not GNU — flag meanings can differ from this course's Linux-side `man` pages |

## Checkpoint (self-assessed)

- [ ] I can explain, in my own words, the difference between the kernel,
      the shell, and a distribution.
- [ ] I can run `uname -a` and `cat /etc/os-release` and correctly
      identify my kernel version and distro from the output.
- [ ] I can construct a command using an option and an argument without
      looking up the syntax.
- [ ] I can chain two commands together with `|` and explain what data
      flows between them.
- [ ] I understand the difference between `>` and `>>` and can state a
      scenario where using the wrong one would cause data loss.
- [ ] I can open and close a `man` page, and can use `--help` as a
      faster alternative for a quick reminder.
- [ ] I can use Tab-completion to complete a partially typed path.
