# Week 0 — Environment Setup & Prerequisites (M0)

## Objective

This module prepares every trainee, regardless of whether their laptop runs
Windows, macOS, or Linux, with a verified, working Linux command-line
environment before Module 1 begins. Because every later module assumes
Linux-specific behavior, the trainee's choice of environment here directly
determines whether the rest of the course behaves as the labs describe.

## Topics

- Choosing the right environment path for the trainee's host operating
  system: Windows Subsystem for Linux 2 (WSL2), a virtual machine, or —
  specifically for macOS — a virtual machine or a Linux container layer
- Installing and configuring the chosen environment
- Baseline tooling every path needs before Module 1 (`git`, `curl`, a text
  editor)
- Verifying the environment is genuinely Linux, not merely "Linux-like"

## M0: Environment Setup — WSL2, Virtual Machine, or macOS

- **Learning Objective:** By the end of this module, the trainee can open a
  terminal (the text-based interface used to type commands to the
  operating system) and confirm they are working inside a real Linux
  environment, with the baseline tools the course requires already
  installed.

- **Core Idea:** Before any Linux command is typed, the trainee needs a
  genuine Linux kernel (the core program that manages a computer's
  hardware and mediates every other program's access to it) running
  underneath them. Windows and macOS are different operating systems with
  different kernels; only WSL2, a virtual machine, or a Linux container
  actually supply a real one.

- **Why It Matters:**
  - Every module from M1 onward assumes Linux-specific behavior —
    permission bits, `systemd` (Linux's service manager, used from M8
    onward), the `apt`/`dnf` package managers, and GNU-flavored command
    flags — none of which exist identically on Windows or macOS.
  - Skipping proper setup is one of the most common sources of confusion
    in any Linux course: commands don't fail loudly when the environment
    is wrong, they behave subtly differently, which is harder to debug
    than an outright error.
  - Production data infrastructure runs on Linux almost without exception.
    Setting up a real Linux environment now is itself a skill data
    engineers repeat throughout their careers — spinning up development
    VMs, containers, and cloud instances is routine, not a one-time setup
    task.

- **How It Works**

  - **Concepts**

    - **WSL2 (Windows only)**
      - WSL2 (Windows Subsystem for Linux, version 2) runs an actual Linux
        kernel inside a lightweight, Microsoft-managed virtual machine
        (a software-simulated computer running its own operating system),
        integrated directly into Windows so that Linux and Windows files,
        networking, and command lines can interact.
      - This differs from the original WSL1, which translated Linux
        system calls into Windows equivalents rather than running a real
        kernel. WSL2's real kernel is why kernel-level features this
        course depends on — proper file permission bits, a full
        networking stack, and (on recent builds) `systemd` — behave like
        an actual Linux machine rather than an approximation of one.
      - Requirements: Windows 10 version 2004 (Build 19041) or later, or
        Windows 11, with hardware virtualization (Intel VT-x or AMD-V)
        enabled in the BIOS/UEFI.
      - Installation: from an administrator PowerShell or Command Prompt,
        run the install command. This installs the WSL platform
        components and a default Ubuntu LTS (Long-Term Support — a
        release branch that receives five years of security updates)
        distribution in one step.

        ```powershell
        wsl --install
        ```

        A specific distribution can be listed and chosen instead:

        ```powershell
        wsl --list --online
        wsl --install -d Ubuntu-22.04
        ```

      - Windows will prompt a restart after installation. On first launch
        of the installed distribution, the trainee creates a Unix
        username and password — unrelated to their Windows login.
      - `systemd` support (needed for M8's `systemctl`/`journalctl` labs)
        should be verified, not assumed: open `/etc/wsl.conf` inside the
        distribution and confirm it contains the following:

        ```ini
        [boot]
        systemd=true
        ```

        Recent Ubuntu WSL images enable this by default, but older or
        customized images may not.

    - **Virtual Machine (any host operating system)**
      - A virtual machine (VM) uses a hypervisor (software that creates
        and runs virtual machines by presenting virtual hardware to a
        guest operating system) to run a complete, independent operating
        system inside a window on the host machine.
      - Recommended hypervisors: VirtualBox (free; Windows, macOS, Linux),
        VMware Fusion (free for personal use; macOS) or VMware Workstation
        Pro (Windows, Linux), UTM (free; macOS, with native support for
        Apple Silicon's ARM architecture), or Hyper-V (built into Windows
        Pro and Enterprise editions).
      - Recommended guest operating system: an Ubuntu Server or Ubuntu
        Desktop LTS release. LTS releases match what most production
        Linux servers run.
      - Minimum resource allocation: 2 virtual CPU cores, 4 GB RAM (8 GB
        strongly recommended once the course reaches Docker and
        Kubernetes in M10–M11), and 25 GB of virtual disk space.
      - Installation flow: download the hypervisor, download the Ubuntu
        ISO (a single file containing the installer image), create a new
        VM pointing at that ISO, and follow the guided installer. The
        installer's default disk partitioning is sufficient for this
        course.

    - **macOS**
      - macOS's Terminal application provides access to a genuine
        Unix-family command line, but the operating system underneath it
        — Darwin — is not Linux. Darwin shares Unix ancestry and many
        command names with Linux, but its kernel and default command-line
        tools (collectively, the BSD userland — utilities such as `ls`,
        `sed`, and `grep`, built from BSD source rather than GNU source)
        differ in ways that matter for this course: flag syntax for tools
        such as `sed` differs, and entire subsystems the course depends
        on — the `apt`/`dnf` package managers, `systemd`, and native cron
        behavior — do not exist on macOS at all.
      - Because of that gap, Terminal.app by itself is not sufficient to
        complete this course. macOS trainees need a genuine Linux
        environment layered on top of macOS, using either a virtual
        machine (full fidelity, as described above) or Docker Desktop (a
        lighter-weight option that runs Linux containers — isolated
        Linux userspace environments that share the host's kernel — using
        a small managed VM behind the scenes).
      - Apple Silicon (M-series) Macs should use UTM or a hypervisor with
        native ARM support; Intel Macs can also use VirtualBox or VMware
        Fusion.
      - Homebrew (macOS's most widely used package manager — a tool that
        automates downloading and installing software) is worth
        installing on the host Mac regardless of which Linux path is
        chosen, since host-side tooling such as a code editor is easiest
        to install through it:

        ```bash
        /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
        ```

  - **Best Practices**

    - **WSL2**
      - Store all course files inside the Linux filesystem (for example,
        `~/projects/`), never under `/mnt/c/...`. Files accessed across
        the Windows/Linux boundary carry a significant I/O performance
        penalty and can behave inconsistently with permission bits.
      - Update the distribution immediately after installation:

        ```bash
        sudo apt update && sudo apt upgrade -y
        ```

      - Install Windows Terminal from the Microsoft Store for a proper
        terminal emulator (tabs, better text rendering) instead of
        relying on the default console host.
      - Pair WSL2 with an editor that has remote/WSL support (for
        example, VS Code's "WSL" extension) so files are edited directly
        inside the Linux filesystem.
      - Keep WSL2 as the default version for any future distribution:

        ```powershell
        wsl --set-default-version 2
        ```

    - **Virtual Machine**
      - Install the hypervisor's guest utilities after the OS installs
        (VirtualBox Guest Additions, VMware Tools, or UTM's SPICE guest
        tools). These enable clipboard sharing, shared folders, and a
        resizable display; without them the VM still works but is
        noticeably harder to use.
      - Take a snapshot (a saved, restorable state of the entire VM)
        immediately after a clean installation, and again once this
        module's checkpoint passes. If a later lab breaks the
        environment, the trainee can roll back instead of reinstalling.
      - Leave the network adapter on NAT (Network Address Translation)
        mode unless a later module instructs otherwise — NAT gives the
        VM outbound internet access while keeping it isolated from the
        trainee's local network, which is sufficient through M9.
      - Once M7 introduces networking, connect to the VM over SSH from
        the host terminal rather than only working inside the
        hypervisor's graphical console — this mirrors how real servers
        are actually used.
    - **macOS**
      - Do not treat Terminal.app plus Homebrew's GNU coreutils as a
        substitute for a real Linux environment. Installing GNU versions
        of `ls`, `sed`, and `grep` narrows the command-syntax gap, but it
        does not provide `systemd`, `apt`/`dnf`, or a genuine Linux
        kernel — all of which later modules (M6, M8) depend on directly.
        Treat GNU coreutils as a host-side convenience, not a substitute
        for the VM or container this module requires.
      - If choosing Docker Desktop over a full VM, confirm that
        `systemd`-dependent labs (M8) are still run inside a VM, since
        most Docker containers do not run a full init system by default.
      - Verify Rosetta 2 (Apple's Intel-to-ARM translation layer) is
        installed if any later tool ships only as an Intel binary:

        ```bash
        softwareupdate --install-rosetta
        ```

  - **Real-World Example**
    - Many data engineers on Windows laptops use WSL2 as their day-to-day
      development shell specifically because it provides a real Linux
      kernel for testing shell scripts, cron jobs, and containerized
      pipelines locally before the same code is deployed to Linux-based
      cloud infrastructure, such as an EC2 instance or a Kubernetes node.
    - On macOS, a common and costly mistake is writing and testing a
      script in Terminal.app, then finding it behaves differently once
      deployed to a Linux server — a classic case being `sed -i`, which
      requires a backup-suffix argument on macOS/BSD but not on
      Linux/GNU. Working inside a real Linux VM or container from the
      start removes that discrepancy before it costs debugging time in
      production.

- **Supplemental Reading**
  - [Installing Ubuntu on VMware in Windows](https://www.geeksforgeeks.org/linux-unix/linux-tutorial/) —
    covers one specific slice of the Virtual Machine path: installing
    Ubuntu under VMware on a Windows host. It does not cover VirtualBox,
    UTM, Hyper-V, WSL2, or macOS/Homebrew/Docker Desktop; those sections
    above are written from general knowledge, not from a cited source.

## Hands-on lab

### M0 — Environment Setup Lab

The trainee completes only the path that matches their host machine.

- **Windows — WSL2 path**
  1. Confirm the Windows build supports WSL2, then open PowerShell as
     Administrator and run:

     ```powershell
     wsl --install
     ```

  2. Restart when prompted, then launch the installed distribution from
     the Start menu and create a Unix username and password.
  3. Update the distribution:

     ```bash
     sudo apt update && sudo apt upgrade -y
     ```

  4. Open `/etc/wsl.conf` inside the distribution and confirm it
     contains:

     ```ini
     [boot]
     systemd=true
     ```

     Add it if missing, then restart WSL from PowerShell and relaunch
     the distribution:

     ```powershell
     wsl --shutdown
     ```

  5. Install Windows Terminal from the Microsoft Store.

- **Any host — Virtual Machine path**
  1. Install a hypervisor appropriate to the host: VirtualBox, VMware
     Fusion/Workstation, UTM, or Hyper-V.
  2. Download an Ubuntu LTS ISO.
  3. Create a new VM (2 vCPU / 4 GB RAM / 25 GB disk minimum) and install
     Ubuntu from the ISO using the installer's default options.
  4. Install the hypervisor's guest utilities.
  5. Take a snapshot of the clean install before continuing.

- **macOS path**
  1. Install Homebrew on the host Mac.
  2. Choose a Linux layer: install a hypervisor (UTM for Apple Silicon;
     UTM, VirtualBox, or VMware Fusion for Intel) and follow the Virtual
     Machine path above, or install Docker Desktop if a container-based
     workflow is preferred for the non-`systemd` modules.
  3. If on Apple Silicon, confirm whether Rosetta 2 is needed and install
     it if so.

- **Verification (all paths)** — run each command inside the Linux
  environment just set up and confirm it returns a result rather than an
  error:

  ```bash
  uname -a
  cat /etc/os-release
  whoami
  git --version
  curl --version
  bash --version
  ```

## Lab exercise

The trainee produces a short written confirmation, saved alongside their
course notes, containing:

- Which path was used (WSL2, VM, or macOS + VM/Docker) and why it was
  chosen for their machine.
- The full output of the six verification commands above.
- Two to three sentences, in the trainee's own words, explaining the
  difference between their host operating system and the Linux
  environment they just set up — this checks that the concept, not just
  the installation, has landed.

## WSL2 vs macOS

Only rows where the two paths actually diverge are listed; a Virtual
Machine, by design, behaves as native Linux and is not included as a
comparison row.

| Behavior | WSL2 | macOS |
|---|---|---|
| Kernel | Real Linux kernel, via a lightweight managed VM | Darwin (BSD-based) — not Linux |
| Course-readiness out of the box | Ready after `wsl --install` | Not ready on its own — requires an additional VM or Docker layer |
| `systemd` support | Available on recent builds (opt-in via `/etc/wsl.conf`) | Not available natively |
| Default package manager | `apt` (Ubuntu), inside the Linux environment | Homebrew, on the host — not a substitute for `apt`/`dnf` inside Linux |
| Command-line tool flavor | GNU coreutils (matches Linux servers) | BSD coreutils by default; flag syntax differs (e.g., `sed -i`) |
| Filesystem layout | Linux paths inside WSL (`/home/...`); Windows drives mounted under `/mnt/c/...` | Native Unix paths (`/Users/...`) on the host; a separate filesystem exists inside any VM/container |

## Checkpoint (self-assessed)

- [ ] I can open a terminal and confirm I am running inside a real Linux
      environment — `uname -a` reports `Linux`, not `Darwin` or a
      Windows-native result.
- [ ] `cat /etc/os-release` prints a recognized Linux distribution name
      and version.
- [ ] I have a non-root user account and can run `sudo` successfully.
- [ ] `git --version` and `curl --version` both return a version number
      without error.
- [ ] I have a text editor available inside the Linux environment (for
      example, `nano`, `vim`, or an editor connected via a remote/WSL
      extension) and can open and save a file with it.
- [ ] If I used a Virtual Machine, I have taken a snapshot of the clean
      install.
- [ ] If I am on macOS, I understand that Terminal.app alone is not
      sufficient for this course, and I am working inside a VM or Linux
      container.
- [ ] I can explain, in my own words, the difference between my host
      operating system and the Linux environment I just set up.
