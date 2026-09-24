# Week 3 — Access Control & Software (M5–M6)

## Objective

This module gives the trainee control over who can do what to a file or
directory (the permission and ownership model) and how to bring new
software onto a Linux system safely, through its package manager rather
than ad hoc downloads — the two skills Layer 3 is built around.

## Topics

- The Linux identity model: users, groups, and how they relate to
  processes and files
- Permission bits: read/write/execute, for owner/group/other
- Changing permissions and ownership: `chmod`, `chown`, `chgrp`
- Privilege escalation: `sudo` and why it's preferred over logging in as
  root
- System-level package managers (`apt`/`yum`/`dnf`) vs. language-level
  package managers (`pip`, `conda`)
- Installing, verifying, and removing packages safely

## M5: Users, Permissions, and Ownership

- **Learning Objective:** By the end of this module, the trainee can
  read a permission string (e.g. `-rwxr-xr--`) and explain exactly who
  can do what to that file, and can change a file's permissions and
  ownership to match a stated requirement.

- **Core Idea:** Every file and process on Linux belongs to a user and a
  group, and every file carries three permission sets — for its owner,
  its group, and everyone else. This three-way model is how Linux
  enforces "who can do what" without needing a separate access-control
  system bolted on top.

- **Why It Matters:**
  - "Permission denied" is one of the most common early errors in
    Linux, and it's almost always resolvable in seconds once the
    ownership/permission model is understood rather than guessed at.
  - M6's package installs, M8's cron jobs, and M13's secrets hygiene all
    depend on getting ownership and permissions right — this module is
    a recurring theme (per the arch doc's dependency notes), not a
    one-off topic.
  - Production incidents caused by overly permissive files (e.g. a
    world-writable credentials file) are a direct, practical
    consequence of skipping this model — it's the conceptual foundation
    M13's least-privilege guidance builds on.

- **How It Works**

  - **Concepts**
    - **Users and groups** — every user has a unique user ID and
      belongs to at least one group; every process runs as some user
      and inherits that user's permissions.
    - **Permission string anatomy** — `-rwxr-xr--` reads as: file type
      (`-` = regular file, `d` = directory), then three groups of
      three: owner (`rwx`), group (`r-x`), other (`r--`). `r` = read,
      `w` = write, `x` = execute (or "enter", for a directory).

      ```bash
      ls -l
      ```

    - **`chmod`** changes permissions, either symbolically or
      numerically:

      ```bash
      chmod u+x script.sh
      chmod 755 script.sh
      ```

      `755` breaks down as owner=7 (rwx), group=5 (r-x), other=5 (r-x)
      — each digit is the sum of read(4)+write(2)+execute(1) for that
      set.
    - **`chown`** changes a file's owning user (and optionally group);
      **`chgrp`** changes only the group:

      ```bash
      sudo chown alice:data notes.txt
      sudo chgrp data notes.txt
      ```

    - **`sudo`** temporarily elevates a command to run as root (or
      another user), rather than requiring a full login as root — the
      trainee's own password is used, not root's:

      ```bash
      sudo apt update
      ```

  - **Best Practices**
    - Use `sudo <command>` for the one command that needs elevation,
      not `sudo su` to become root for an extended session — this
      limits how long elevated privileges are active and keeps an audit
      trail of exactly which commands were run with `sudo`.
    - Default to the least permissive setting that still works — a
      script that only the owner needs to run should be `700` or `750`,
      not `777`.
    - Never treat `777` as a quick fix for a permission error — it
      grants write access to every user on the system and is a common
      root cause of later security incidents, directly relevant to M13.
    - Check both ownership (`chown`) and permission bits (`chmod`) when
      debugging "permission denied" — a file can have the right
      permission bits but the wrong owner/group, or vice versa.

  - **Real-World Example**
    - A classic production incident: a deployment script sets a config
      file to `777` "just to get it working," and months later an audit
      finds any user on the shared server could read (and had been
      reading) database credentials from it — the exact failure mode
      M13's least-privilege guidance exists to prevent, rooted directly
      in this module's permission model.

- **Supplemental Reading**
  - [User and File System Privileges](https://www.varsitytutors.com/practice/subjects/cyber-security/lessons/user-and-file-system-privileges) —
    covers the user/group identity model and how it maps to file
    access.
  - [Linux File Permissions Explained](https://www.redhat.com/en/blog/linux-file-permissions-explained) (Red Hat) —
    covers the `rwx`/owner-group-other permission model and `chmod` in
    more depth.

## M6: Package Management — Installing Software Safely

- **Learning Objective:** By the end of this module, the trainee can
  install, verify, and remove a package using the system package
  manager, and can explain when a language-level package manager
  (`pip`, `conda`) is the more appropriate tool instead.

- **Core Idea:** Almost all software on a Linux system arrives through a
  package manager rather than a manual download — a tool that tracks
  what's installed, resolves dependencies automatically, and lets
  software be removed cleanly. Language ecosystems (Python, in
  particular) add their own package managers on top for
  language-specific libraries.

- **Why It Matters:**
  - M6 depends directly on M5: installing system-wide packages requires
    `sudo`, and understanding why ties straight back to the permission
    model just covered.
  - "Works on my machine" is frequently a package version mismatch;
    knowing how to check what's installed and verify a version is a
    foundational data-engineering skill.
  - The Capstone (from M10 onward) containerizes a script whose
    dependencies were installed with exactly these tools — getting
    package management right here avoids repeating the same debugging
    later inside a Dockerfile.

- **How It Works**

  - **Concepts**
    - **System package managers** — `apt` (Debian/Ubuntu) and
      `dnf`/`yum` (Fedora/RHEL) install software from the distro's
      official repositories, tracking dependencies and providing a
      single, uniform way to update or remove anything installed this
      way. This course's Ubuntu environment uses `apt`.

      ```bash
      sudo apt update
      sudo apt install <package-name>
      apt list --installed
      sudo apt remove <package-name>
      ```

      `apt update` refreshes the local list of available packages and
      versions — it does not install or upgrade anything by itself;
      that's a common early point of confusion.
    - **Language-level package managers** — `pip` (Python) and `conda`
      (Python/data-science-oriented, also manages non-Python
      dependencies) install libraries into a Python environment rather
      than system-wide.

      ```bash
      pip install <package-name>
      pip show <package-name>
      pip uninstall <package-name>
      ```

    - **Verifying an install** — most packages support a version flag
      that confirms both that the install succeeded and which version
      landed.

      ```bash
      java -version
      python3 --version
      ```

  - **Best Practices**
    - Run `sudo apt update` before `sudo apt install` when installing
      anything new in a session — an out-of-date package list can point
      at a version that's no longer available in the repository.
    - Prefer the system package manager for anything that needs to run
      system-wide or be available to every user (e.g. a JDK); prefer
      `pip`/`conda` inside a virtual environment for Python libraries
      specific to one project, to avoid version conflicts between
      projects.
    - Always verify an install with a version check rather than
      assuming success from the absence of an error message.
    - Remove packages with the package manager (`apt remove`, `pip
      uninstall`), never by manually deleting files — the package
      manager's own records of what's installed become inaccurate
      otherwise, causing future installs/removals to behave
      unpredictably.

  - **Real-World Example**
    - A data engineer setting up a new pipeline server typically
      installs a JDK system-wide via `apt` (since tools like Spark need
      it available to every user/process), but installs the pipeline's
      own Python dependencies via `pip` inside a project-specific
      virtual environment — exactly the system-vs-language-level split
      this module teaches, applied in the same setup.

- **Supplemental Reading**
  - **Note:** The research gathered for this course has one relevant,
    narrow source: a set of practical installation guides (including
    installing a JDK, and installing/configuring Nginx from source)
    hosted as part of a broader Linux tutorial reference. That source
    supports the JDK-install workflow behind this module's lab, but
    does not cover `pip` or `conda` specifically — the language-level
    package manager content above is written from general knowledge,
    not from a cited source. Cross-check the official
    [pip documentation](https://pip.pypa.io/en/stable/) directly if
    `pip` behavior differs from what's described here.

## Hands-on lab

### M5 — Users, Permissions, and Ownership Lab

1. Create a practice file and inspect its default permissions:

   ```bash
   mkdir -p ~/linux-course/m5
   cd ~/linux-course/m5
   touch shared-report.txt
   ls -l shared-report.txt
   ```

2. Restrict the file so only the owner can read and write it, and
   confirm the change:

   ```bash
   chmod 600 shared-report.txt
   ls -l shared-report.txt
   ```

3. Create a second file, make it executable by the owner only, and
   confirm:

   ```bash
   touch run.sh
   chmod u+x run.sh
   ls -l run.sh
   ```

4. Use `sudo` to view a file only root can read by default, confirming
   `sudo` prompts for the trainee's own password, not a separate root
   password:

   ```bash
   sudo cat /etc/shadow
   ```

5. In the trainee's own words, write down what `750` means in terms of
   owner/group/other read-write-execute bits, before checking the
   answer against this module's Concepts section.

### M6 — Package Management Lab

1. Update the local package list, then install a JDK:

   ```bash
   sudo apt update
   sudo apt install default-jdk
   ```

2. Verify the install by checking the installed version:

   ```bash
   java -version
   ```

3. Confirm the package is tracked by the package manager:

   ```bash
   apt list --installed | grep jdk
   ```

4. Install a Python package with `pip` and verify it separately from
   the system package manager:

   ```bash
   pip install requests
   pip show requests
   ```

5. Remove the `pip` package cleanly:

   ```bash
   pip uninstall requests
   ```

## Lab exercise

The trainee produces a short written summary, saved alongside their
course notes, containing:

- The `ls -l` output for `shared-report.txt` and `run.sh` before and
  after each `chmod` in the M5 lab, with a one-sentence explanation of
  what changed.
- The JDK version confirmed by `java -version` in the M6 lab.
- One sentence explaining, in the trainee's own words, when they'd
  choose `apt` over `pip` for installing something.

## WSL2 vs macOS

| Behavior | WSL2 | macOS |
|---|---|---|
| Default file permission model | Standard Linux `rwx`/owner-group-other, fully enforced | Also `rwx`-based (Unix heritage), but default ACL and extended-attribute behavior differs from Linux — irrelevant once working inside a real Linux VM/container per M0 |
| System package manager | `apt` (Ubuntu default) | No native system package manager for a Darwin userland; Homebrew manages host-side macOS software but is not a substitute for `apt`/`dnf` inside the Linux environment this course requires |

## Checkpoint (self-assessed)

- [ ] I can read a permission string like `-rwxr-xr--` and state exactly
      who can read, write, and execute the file.
- [ ] I can change a file's permissions using both symbolic (`u+x`) and
      numeric (`755`) `chmod` syntax.
- [ ] I can change a file's owner and group with `chown`/`chgrp`.
- [ ] I can explain why `sudo <command>` is preferred over logging in as
      root.
- [ ] I can install, verify, and remove a package using `apt`.
- [ ] I can install, verify, and remove a package using `pip`.
- [ ] I can explain when to use a system package manager vs. a
      language-level one.
