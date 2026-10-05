# Users, Permissions, Ownership Brief

**Week:** 3 of 6 (shared with M6, 5–6 hrs total) · **Builds on:** M4 · **Feeds into:** M6, M13

## Objective

You can reason about and construct a permission scheme deliberately, rather than reaching for a
"make it work" shortcut when access fails. This removes the course's third failure mode:
"Permission denied" / "works on my machine."

## Scope

You design and prove a permission scheme on your own system. Find out, and write down in your own words:

- **Users and groups** — every file and process belongs to a user and a group, and that identity is what
  Linux checks when something asks for access. Research: how to see which user and groups you are, and
  where accounts and groups are defined on the system.
- **Permission bits** — each file carries read, write, and execute permissions for its owner, its group,
  and everyone else, with no separate access-control system on top. Research: how to read the output of
  `ls -l`, and how the symbolic and octal notations map to each other.
- **Ownership vs. permission** — who owns a file and what they're allowed to do with it are two separate
  settings, and changing one doesn't change the other. Research: what `chown` and `chmod` each change,
  and why a directory's execute bit behaves differently from a file's.
- **Elevated privilege** — `sudo` and root can bypass the model, which is exactly why reaching for them
  to make an error go away is a habit to resist. Research: what `sudo` actually does, and when elevated
  privilege is genuinely warranted.

## Stack constraints

Your own environment, with at least one additional user or group available to demonstrate a real
restriction — not just your own default account.

## Deliverable

An access-restricted shared directory: a directory with a permission/ownership scheme you designed,
where access is deliberately restricted for one identity and open for another, with both outcomes
demonstrated, not just configured.

## Definition of done

- You can state, for your directory, exactly what each permission bit is set to and why.
- The restriction is proven, not just configured — you show a denied-access attempt actually failing and
  an authorized attempt actually succeeding.
- No step in the deliverable relies on a blanket "open everything to everyone" permission shortcut.

## Still open / ask your trainer

- What the shared directory is "for" is left to you — pick something concrete rather than a placeholder
  scenario, and confirm with your trainer if you're unsure what counts as a real test of the restriction.
