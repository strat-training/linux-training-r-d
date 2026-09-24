# Week 5 — Security & Secrets Hygiene (M13)

## Objective

This module closes the automation work from M8–M9 by removing the
single most common security mistake in scripts written so far in this
course: credentials hardcoded directly in source. It applies the
least-privilege thinking from M5 and the secure-transfer tools from M7
to the trainee's own M9 script, rather than treating security as a
separate, bolted-on exercise.

## Topics

- Least privilege as an operating principle, not a one-time setting
- Why hardcoded credentials are a liability, even in "private" scripts
- Environment variables as the standard credential-injection mechanism
- `.gitignore` and why credentials must never reach version control
- Secure transfer (`scp`/`sftp`) as the default for moving sensitive
  files

## M13: Security & Secrets Hygiene

- **Learning Objective:** By the end of this module, the trainee can
  take a script with a hardcoded credential and refactor it to read
  that credential from an environment variable instead, and can
  configure `.gitignore` so the file holding that credential is never
  committed.

- **Core Idea:** Security in this course isn't a separate checklist run
  at the end — it's the same least-privilege thinking from M5
  (permissions) and secure-transfer tools from M7 (`scp`), applied
  specifically to how credentials are stored and passed to scripts, on
  the exact same script the trainee already built in M9.

- **Why It Matters:**
  - M5 (Permissions) is a dependency for M13 for a reason: file
    permissions and credential hygiene are the same underlying
    discipline — restricting who can read a secret is only half the
    problem if the secret is also sitting in plaintext inside a script
    anyone can open.
  - M9 (Shell Scripting) feeds directly into M13: the capstone's
    requirement that "security is baked in, not bolted on" means the
    trainee applies this module's fix to the same script from M9, not a
    separate toy example.
  - Hardcoded credentials are one of the most common and most damaging
    real-world security incidents — accidentally committing a
    credential to a public or shared repository is a well-documented,
    frequently repeated mistake across the industry, not a
    hypothetical.

- **How It Works**

  - **Concepts**
    - **Least privilege** — a process, script, or user should have
      only the access it strictly needs to do its job, and no more —
      the same principle M5 introduced for file permissions applies
      equally to what a script can read, write, or connect to.
    - **Environment variables as credential injection** — instead of
      writing a value directly into a script, the script reads it from
      the environment at runtime, so the credential never appears in
      the script's own text:

      ```bash
      export DB_PASSWORD="example-value"
      ```

      Inside a script:

      ```bash
      #!/bin/bash
      if [ -z "$DB_PASSWORD" ]; then
        echo "DB_PASSWORD is not set"
        exit 1
      fi
      ```

      `-z` checks whether the variable is empty/unset — failing loudly
      here is safer than silently proceeding with a missing credential.
    - **`.gitignore`** — a file listing patterns Git should never
      track, so files matching them (e.g. a `.env` file holding real
      credentials) are never accidentally committed:

      ```gitignore
      .env
      *.pem
      *.key
      ```

    - **Secure transfer for sensitive files** — when a credential-
      bearing file must move between machines at all, `scp`/`sftp`
      (both built on SSH, from M7) keep it encrypted in transit; an
      unencrypted protocol should never be used for this:

      ```bash
      scp .env user@remote-host:/home/user/app/
      ```

  - **Best Practices**
    - Never commit a `.env` file (or any file containing a real
      credential) to version control, even to a private repository —
      private today doesn't guarantee private forever, and history
      persists even after a later deletion.
    - Provide a `.env.example` (with placeholder values, no real
      credentials) alongside a `.gitignore`'d real `.env`, so
      collaborators know what variables a script expects without ever
      seeing an actual secret.
    - Fail loudly and immediately (`exit 1` with a clear message) when
      a required credential is missing, rather than letting a script
      proceed with an empty or default value that might silently cause
      incorrect behavior.
    - Treat any credential that was ever hardcoded and later removed as
      compromised — removing it from the current version of a file does
      not remove it from Git history; a leaked credential should be
      rotated (replaced with a new one), not just deleted from the
      script.

  - **Real-World Example**
    - A well-documented, recurring category of real-world security
      incidents is a developer committing a database password or API
      key directly in a script to a public GitHub repository;
      automated scanners actively search public repos for exactly this
      pattern within minutes of a push — which is why this module
      treats "remove the hardcoded credential" as a required refactor
      of the trainee's own M9 script, not an optional cleanup step.

- **Supplemental Reading**
  - [Cyber Hygiene for Developers: Secure Coding Practices You Need Today](https://insights2techinfo.com/cyber-hygiene-for-developers-secure-coding-practices-you-need-today/) —
    covers secure coding habits broadly, including credential handling.
  - [Secrets Hygiene as a Culture: Training, Tooling and Policies](https://checkmarx.com/learn/secrets-detection/secrets-hygiene-as-a-culture-training-tooling-and-policies/) (Checkmarx) —
    covers secrets management as an organizational practice, not just a
    one-off technical fix.
  - [Software Security Hygiene](https://apiiro.com/glossary/software-security-hygiene/) (Apiiro) —
    glossary-style reference on security hygiene practices generally.

## Hands-on lab

### M13 — Security & Secrets Hygiene Lab

1. Revisit the M9 validation script and add a line that hardcodes a
   fake credential, to see the anti-pattern directly before fixing it:

   ```bash
   nano ~/linux-course/m9/validate.sh
   ```

   Temporarily add near the top:

   ```bash
   DB_PASSWORD="hardcoded-example-123"
   ```

2. Refactor the script to remove the hardcoded value and read it from
   the environment instead, failing loudly if it's missing:

   ```bash
   nano ~/linux-course/m9/validate.sh
   ```

   Replace the hardcoded line with:

   ```bash
   if [ -z "$DB_PASSWORD" ]; then
     echo "DB_PASSWORD is not set"
     exit 1
   fi
   ```

3. Create a `.env` file holding the real value, and a `.env.example`
   with a placeholder, in the script's directory:

   ```bash
   echo "DB_PASSWORD=hardcoded-example-123" > ~/linux-course/m9/.env
   echo "DB_PASSWORD=your-password-here" > ~/linux-course/m9/.env.example
   ```

4. Add a `.gitignore` that excludes the real `.env` but keeps the
   example:

   ```bash
   cd ~/linux-course/m9
   printf ".env\n" > .gitignore
   ```

5. Confirm the refactored script fails loudly when the variable is
   unset, then succeeds once it's exported:

   ```bash
   unset DB_PASSWORD
   ./validate.sh incoming/valid.csv
   echo "Exit code: $?"
   ```

   ```bash
   export DB_PASSWORD="hardcoded-example-123"
   ./validate.sh incoming/valid.csv
   echo "Exit code: $?"
   ```

6. Confirm `.gitignore` actually excludes `.env` (requires `git` to be
   initialized in the directory first):

   ```bash
   git init
   git status
   ```

   `.env` should not appear as a trackable file in `git status`'s
   output; `.env.example` should.

## Lab exercise

The trainee produces a short written summary, saved alongside their
course notes, containing:

- The exit code and message produced when `DB_PASSWORD` was unset,
  versus when it was correctly exported.
- The contents of the `.gitignore` file created in step 4.
- The `git status` output from step 6, confirming `.env` is excluded
  and `.env.example` is not.
- One sentence, in the trainee's own words, on why a credential that
  was ever committed to Git — even if later removed — should be treated
  as compromised.

## WSL2 vs macOS

| Behavior | WSL2 | macOS |
|---|---|---|
| Environment variable persistence | Set in `~/.bashrc` inside the WSL2 Linux environment, same as any Linux system | Same mechanism inside a real Linux VM/container (per M0); setting a variable in the host macOS shell does not make it visible inside the Linux environment — they're separate environments |

## Checkpoint (self-assessed)

- [ ] I can explain, in my own words, why a hardcoded credential is a
      security risk even in a script I never intend to share.
- [ ] I refactored my M9 script to read a credential from an
      environment variable instead of hardcoding it.
- [ ] My refactored script fails loudly with a clear message when the
      required environment variable is unset.
- [ ] I created a `.env` (real values) and a `.env.example` (placeholder
      values), and a `.gitignore` that excludes only the real `.env`.
- [ ] I confirmed with `git status` that `.env` is excluded from version
      control and `.env.example` is not.
- [ ] I can explain why a credential that was ever committed to Git
      history should be rotated, not just deleted from the current
      file.
