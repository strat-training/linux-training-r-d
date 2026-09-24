# Week 4 — Automation & Scheduling (M7–M9)

## Objective

This module gives the trainee the tools to inspect what a Linux machine
is doing right now (networking, resource usage, running processes),
keep long-running services alive and scheduled automatically (systemd,
cron), and automate repeatable tasks with shell scripts — collectively
removing the "the job didn't run" / manual-toil failure mode Layer 4
targets.

## Topics

- Networking primitives: `curl`, `scp`, `ss`
- Resource monitoring: `df -h`, `free -h`
- Process lifecycle: `ps`, `kill`
- Service management: `systemctl`, `journalctl`, the boot process
- Scheduling: `crontab`
- Shell scripting: control flow (`if`/`for`/`while`), functions, exit
  codes (`$?`), `sed`/`awk`

## M7: Networking & Resource Monitoring

- **Learning Objective:** By the end of this module, the trainee can
  transfer a file to/from a remote machine, check whether a network
  port is open, and confirm a machine has enough disk and memory
  headroom before running a job.

- **Core Idea:** Before automating anything (M8–M9), a data engineer
  needs to know whether the machine they're working on actually has the
  resources and connectivity a job needs — networking and
  resource-monitoring commands are the "vital signs" check performed
  before and during any automated task.

- **Why It Matters:**
  - M8's cron jobs and M9's scripts fail silently and confusingly if
    disk fills up or memory runs out mid-run; this module's
    `df -h`/`free -h` checks are the first diagnostic step, not an
    afterthought.
  - `scp` (built on SSH) is the standard way data engineers move files
    to/from remote servers — directly relevant once the course moves
    toward multi-machine, cloud-hosted work.
  - Confirming network reachability (`curl`, `ss`) before assuming "the
    service is down" rules out an entire class of false debugging
    leads.

- **How It Works**

  - **Concepts**
    - **`curl`** — fetches a URL from the command line, useful for
      testing whether a web service or API is reachable:

      ```bash
      curl -I https://example.com
      ```

      `-I` fetches only the response headers, a quick way to confirm a
      service is up without downloading the whole response body.
    - **`scp`** — securely copies files to or from a remote machine
      over SSH:

      ```bash
      scp localfile.txt user@remote-host:/home/user/
      scp user@remote-host:/home/user/remotefile.txt .
      ```

    - **`ss`** — lists network sockets (listening ports and active
      connections), the modern replacement for the older `netstat`:

      ```bash
      ss -tulnp
      ```

      `-t` TCP, `-u` UDP, `-l` listening only, `-n` numeric (skip DNS
      lookups), `-p` show the owning process.
    - **`df -h`** — reports disk space usage per filesystem, in
      human-readable units:

      ```bash
      df -h
      ```

    - **`free -h`** — reports memory (RAM) and swap usage, in
      human-readable units:

      ```bash
      free -h
      ```

  - **Best Practices**
    - Check `df -h` and `free -h` before kicking off any job expected
      to write a lot of data or hold a lot in memory — catching "disk
      is 98% full" before a run is far cheaper than debugging a job
      that failed halfway through.
    - Use `curl -I` rather than a full `curl` request when the goal is
      only to confirm reachability — it's faster and doesn't download
      unnecessary data.
    - Prefer `ss` over the older `netstat` on modern Linux systems —
      `netstat` is deprecated on many distros and may not be installed
      by default.
    - Always transfer files with `scp` (or `rsync`) over SSH rather
      than an unencrypted protocol — this ties directly into M13's
      security hygiene later.

  - **Real-World Example**
    - A data engineer's pipeline job fails at 2am with a cryptic error;
      `df -h` reveals the disk hit 100% mid-run because a log file grew
      unbounded — a five-second check that immediately explains a
      failure that would otherwise take much longer to diagnose from
      the error message alone.

- **Supplemental Reading**
  - **Note:** The research gathered for this course has one relevant,
    narrow source: a set of practical guides that includes enabling SSH
    and using `scp` for secure file transfer, hosted as part of a
    broader Linux tutorial reference. That source supports this
    module's `scp` content, but does not cover `curl`, `ss`, `df -h`,
    or `free -h` — that content above is written from general
    knowledge, not from a cited source.

## M8: Processes, systemd & Cron

- **Learning Objective:** By the end of this module, the trainee can
  list and terminate a running process, check and control a systemd
  service, read a service's logs with `journalctl`, and schedule a
  recurring job with `crontab`.

- **Core Idea:** Linux runs software as processes, most long-running
  services are managed by `systemd` (which starts them at boot and
  keeps them running), and recurring tasks that aren't full services are
  scheduled with `cron` — three related but distinct layers of "make
  something run, and keep it running, automatically."

- **Why It Matters:**
  - This is the direct prerequisite for M9 (a script is only
    "automated" once something schedules or manages it) and the
    capstone (which explicitly allows cron OR a Kubernetes CronJob as
    the scheduling mechanism).
  - "The job didn't run" — the failure mode Layer 4 is named for — is
    most often a cron syntax error, a failed/stopped systemd service,
    or a process that silently died; this module's tools are exactly
    how each of those is diagnosed.
  - `journalctl` is the direct predecessor to M12's
    observability/log-based reporting — a trainee who can already read
    structured service logs here has a head start on M12.

- **How It Works**

  - **Concepts**
    - **`ps`** — lists running processes:

      ```bash
      ps aux
      ```

      `a` show processes for all users, `u` show the user that owns
      each process, `x` include processes not attached to a terminal.
    - **`kill`** — sends a signal to a process by its process ID (PID),
      most commonly to terminate it:

      ```bash
      kill 1234
      kill -9 1234
      ```

      A plain `kill` sends `SIGTERM` (ask the process to shut down
      cleanly); `kill -9` sends `SIGKILL` (force-terminate immediately,
      no cleanup) and should be a last resort.
    - **`systemctl`** — starts, stops, enables, and checks the status
      of a systemd-managed service:

      ```bash
      sudo systemctl status nginx
      sudo systemctl start nginx
      sudo systemctl enable nginx
      ```

      `enable` makes a service start automatically at boot; `start`
      only starts it for the current session.
    - **`journalctl`** — reads the systemd journal (structured logs for
      systemd-managed services):

      ```bash
      journalctl -u nginx
      journalctl -u nginx -f
      ```

      `-u <service>` filters to one service's logs; `-f` follows new
      log entries live, the systemd-journal equivalent of M4's
      `tail -f`.
    - **`crontab`** — schedules a command to run automatically on a
      recurring schedule:

      ```bash
      crontab -e
      ```

      A crontab line has the form
      `minute hour day month weekday command`, e.g.
      `0 2 * * * /home/alice/backup.sh` runs `backup.sh` at 2:00 AM
      every day.

  - **Best Practices**
    - Try `kill` (SIGTERM) before `kill -9` (SIGKILL) — SIGTERM gives
      the process a chance to close files and clean up; SIGKILL can
      leave data in an inconsistent state.
    - Always check `systemctl status <service>` after starting or
      restarting a service, rather than assuming success — a service
      can fail to start, and `systemctl` will report it clearly if
      checked.
    - Redirect a cron job's output explicitly rather than letting it
      disappear — cron jobs run without a terminal attached, so
      anything not redirected to a log file is effectively lost when
      something goes wrong.
    - Use absolute paths inside crontab entries and the scripts they
      call — cron runs with a minimal environment and different
      working-directory assumptions than an interactive shell, so
      relative paths that work when tested manually can silently fail
      under cron.

  - **Real-World Example**
    - A cron job that works perfectly when run manually from the
      terminal but "does nothing" on schedule is one of the most common
      automation bugs in production — almost always caused by a
      relative path or a missing environment variable that only
      existed in the trainee's interactive shell, not cron's minimal
      one; this is exactly why the capstone's validation criterion
      requires proving the pipeline runs correctly under its actual
      scheduled conditions, not just when triggered by hand.

- **Supplemental Reading**
  - **Note:** The research gathered for this course has one relevant
    source covering systemd, the boot process, `systemctl`, and
    `journalctl` specifically, hosted as part of a broader Linux
    tutorial reference — this source backs the
    systemd/`systemctl`/`journalctl` content above directly. It does
    not cover `ps`, `kill`, or `crontab`; that content is written from
    general knowledge, not from a cited source.

## M9: Shell Scripting — Control Flow, Functions, and Exit Codes

- **Learning Objective:** By the end of this module, the trainee can
  write a Bash script that validates input, branches on a condition,
  loops over a set of files, and reports success or failure through its
  exit code.

- **Core Idea:** A shell script is just a sequence of the same commands
  used interactively, made repeatable and given control flow
  (conditionals, loops) and a machine-readable success/failure signal
  (the exit code) — turning one-off manual steps into automation M8 can
  schedule reliably.

- **Why It Matters:**
  - M9 is the central hinge of the course (per the arch doc's
    dependency notes): it's the last "core skill" module and feeds
    directly into Docker (M10), Observability (M12), and Security
    (M13).
  - The capstone's entire pipeline — validate a CSV, route it to
    `processed/` or `rejected/`, log the result — is a single M9
    script; every module from here to the end either extends this
    script or wraps it.
  - Exit codes are how M8's `systemctl`/`cron` and M11's Kubernetes both
    know whether a script succeeded — a script that doesn't set its
    exit code correctly breaks every layer built on top of it.

- **How It Works**

  - **Concepts**
    - **Conditionals (`if`):**

      ```bash
      if [ -f "$1" ]; then
        echo "File exists"
      else
        echo "File not found"
        exit 1
      fi
      ```

    - **Loops (`for`, `while`):**

      ```bash
      for file in *.csv; do
        echo "Processing $file"
      done
      ```

    - **Functions:**

      ```bash
      validate_file() {
        local path="$1"
        [ -f "$path" ]
      }
      ```

    - **Exit codes (`$?`)** — every command sets an exit code when it
      finishes: `0` means success, any nonzero value means failure.
      `$?` holds the most recently finished command's exit code:

      ```bash
      grep "error" logfile.txt
      echo $?
      ```

      A script sets its own exit code explicitly with `exit <code>`,
      which is what a caller (cron, systemd, Kubernetes) checks to know
      whether it succeeded.
    - **`sed`/`awk`** — stream-editing and text-processing tools for
      transforming or extracting data from text, often used inside a
      script rather than typed interactively:

      ```bash
      sed 's/foo/bar/' file.txt
      awk -F, '{print $2}' file.csv
      ```

      `sed 's/foo/bar/'` replaces the first occurrence of `foo` with
      `bar` on each line; `awk -F, '{print $2}'` treats each line as
      comma-separated fields and prints the second one.

  - **Best Practices**
    - Always quote variable expansions (`"$file"`, not `$file`) — an
      unquoted variable containing a space or special character breaks
      in ways that are hard to diagnose later.
    - Check `$?` (or better, act directly on a command's own exit
      status with `if command; then`) immediately after any command
      whose success matters — don't assume a command worked just
      because the script didn't crash.
    - Set an explicit `exit <code>` at every script exit point,
      including error paths — a script that falls off the end without
      an explicit exit code returns whatever the last command happened
      to return, which may not reflect the script's actual overall
      outcome.
    - Test every script against both a valid and a deliberately
      invalid input before considering it done — this is the
      implement → test-happy-path → test-failure-path pattern used
      throughout the course.

  - **Real-World Example**
    - The capstone's CSV validator is a direct application of this
      module: a `for` loop iterates incoming files, an `if` checks the
      column count, valid files move to `processed/` and invalid ones
      to `rejected/`, and the script's exit code (plus its log output)
      is exactly what M8's cron job and M12's report both depend on to
      know what happened.

- **Supplemental Reading**
  - **Note:** No cited source material exists for this topic (Bash
    control flow, functions, exit codes, `sed`/`awk`) in the research
    gathered for this course. The Concepts, Best Practices, and
    Real-World Example above are written from general knowledge, not
    from a cited source.

## Hands-on lab

### M7 — Networking & Resource Monitoring Lab

1. Check whether a public site is reachable:

   ```bash
   curl -I https://example.com
   ```

2. Copy a practice file to `localhost` over SSH, to confirm `scp`'s
   syntax works end to end (substitute an actual remote host if one is
   available):

   ```bash
   echo "network lab" > ~/linux-course/m7-note.txt
   scp ~/linux-course/m7-note.txt "$(whoami)@localhost:~/m7-note-copy.txt"
   ```

3. List listening ports:

   ```bash
   ss -tulnp
   ```

4. Check disk and memory headroom:

   ```bash
   df -h
   free -h
   ```

5. Record, in the trainee's own words, whether the machine currently
   has enough disk and memory headroom to safely run a job that needs
   1 GB of scratch space and 512 MB of RAM.

### M8 — Processes, systemd & Cron Lab

1. List running processes and identify one owned by the trainee's own
   user:

   ```bash
   ps aux | grep "$(whoami)"
   ```

2. Start a long-running background process, then terminate it cleanly:

   ```bash
   sleep 300 &
   ps aux | grep sleep
   kill $(pgrep sleep)
   ```

3. Check the status of a systemd service already installed on the
   training machine (e.g. `cron` itself):

   ```bash
   systemctl status cron
   ```

4. Read that service's recent logs:

   ```bash
   journalctl -u cron -n 20
   ```

5. Schedule a cron job that appends a timestamp to a log file every
   minute:

   ```bash
   crontab -e
   ```

   Add the following line, then save and exit the editor:

   ```cron
   * * * * * /bin/date >> /home/USERNAME/linux-course/m8-cron.log
   ```

   Replace `USERNAME` with the trainee's actual username. Wait at least
   two minutes, then confirm it ran:

   ```bash
   cat ~/linux-course/m8-cron.log
   ```

   Remove the line from `crontab -e` once confirmed, to avoid leaving
   it running indefinitely.

### M9 — Shell Scripting Lab

1. Set up the practice directory structure:

   ```bash
   mkdir -p ~/linux-course/m9/incoming
   mkdir -p ~/linux-course/m9/processed
   mkdir -p ~/linux-course/m9/rejected
   ```

2. Create the validation script:

   ```bash
   nano ~/linux-course/m9/validate.sh
   ```

   Script contents:

   ```bash
   #!/bin/bash
   set -e

   file="$1"
   expected_columns=3

   if [ ! -f "$file" ]; then
     echo "File not found: $file"
     exit 1
   fi

   actual_columns=$(head -n 1 "$file" | awk -F, '{print NF}')

   if [ "$actual_columns" -eq "$expected_columns" ]; then
     mv "$file" ~/linux-course/m9/processed/
     echo "Valid: moved to processed/"
     exit 0
   else
     mv "$file" ~/linux-course/m9/rejected/
     echo "Invalid: expected $expected_columns columns, got $actual_columns. Moved to rejected/"
     exit 1
   fi
   ```

3. Make it executable:

   ```bash
   chmod +x ~/linux-course/m9/validate.sh
   ```

4. Test the happy path with a valid file:

   ```bash
   printf "a,b,c\n1,2,3\n" > ~/linux-course/m9/incoming/valid.csv
   ~/linux-course/m9/validate.sh ~/linux-course/m9/incoming/valid.csv
   echo "Exit code: $?"
   ```

5. Test the failure path with a deliberately malformed file:

   ```bash
   printf "a,b\n1,2\n" > ~/linux-course/m9/incoming/invalid.csv
   ~/linux-course/m9/validate.sh ~/linux-course/m9/incoming/invalid.csv
   echo "Exit code: $?"
   ```

6. Confirm both files landed in the correct directory:

   ```bash
   ls ~/linux-course/m9/processed/
   ls ~/linux-course/m9/rejected/
   ```

## Lab exercise

The trainee produces a short written summary, saved alongside their
course notes, containing:

- The `df -h`/`free -h` output from the M7 lab and a one-sentence
  judgment on whether the machine has enough headroom for a 1 GB / 512
  MB job.
- The `systemctl status` output for the service checked in the M8 lab,
  and the last two log lines from `journalctl` for that service.
- The exit codes recorded for both the valid and invalid CSV runs in
  the M9 lab, and which directory each file ended up in.

## WSL2 vs macOS

| Behavior | WSL2 | macOS |
|---|---|---|
| `systemd`/`systemctl` availability | Available on recent builds, opt-in via `/etc/wsl.conf` (per M0) | Not available — macOS uses `launchd`, a different service manager with different commands (`launchctl`); irrelevant once working inside a real Linux VM/container per M0 |
| `crontab` availability | Available once inside the WSL2 Linux environment | `cron` exists on Darwin but is deprecated in favor of `launchd`; the Linux VM/container path (per M0) provides a standard `cron` instead |
| Default shell for scripts (`#!/bin/bash`) | Bash present and current on Ubuntu | Bash present but outdated (3.x) unless upgraded; scripts targeting modern Bash features should be run inside the Linux VM/container, not Terminal.app's default shell |

## Checkpoint (self-assessed)

- [ ] I can use `curl` to check whether a service is reachable and
      `scp` to transfer a file to/from a remote host.
- [ ] I can use `ss` to list listening ports and `df -h`/`free -h` to
      check disk and memory headroom.
- [ ] I can list running processes with `ps` and terminate one with
      `kill`.
- [ ] I can check, start, and enable a systemd service with
      `systemctl`, and read its logs with `journalctl`.
- [ ] I can write and confirm a working `crontab` entry.
- [ ] I can write a Bash script using `if`, `for`/`while`, and a
      function.
- [ ] I can explain what `$?` and `exit <code>` do and why a caller
      (cron, systemd, Kubernetes) depends on them.
- [ ] I tested my M9 script against both a valid and a deliberately
      invalid input, per the implement → test-happy-path →
      test-failure-path pattern.
