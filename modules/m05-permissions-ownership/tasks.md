# Users, Permissions, Ownership Tasks

**Objective:** Reason about and construct a permission scheme deliberately, rather than reaching for a
"make it work" shortcut when access fails.

> There is no solutions file for this stage. This checklist guides the work — it doesn't contain it. See
> `brief.md` for the full objective, scope, stack constraints, and definition of done, and fill in
> `write-up-template.md` as you go, not after.

## Setup

- [ ] Confirm with your trainer: what counts as a real test of the restriction, if you're unsure.
- [ ] Decide what your shared directory is for, and which identity gets access and which doesn't.
- [ ] Make sure at least one additional user or group is available to test with.

## Build

- [ ] Research the "Research:" questions under each Scope bullet in `brief.md` before you start.
- [ ] Inspect the users, groups, and permissions already on your system and note what you find.
- [ ] Create the shared directory and set its ownership and permissions to your designed scheme,
      without a blanket "open to everyone" shortcut.
- [ ] Demonstrate a denied-access attempt actually failing for the restricted identity.
- [ ] Demonstrate an authorized attempt actually succeeding for the allowed identity.

## Verify

- [ ] Confirm you can state what each permission bit on your directory is set to and why.
- [ ] Confirm the restriction is proven by a failed attempt and a successful one, not just configured.
- [ ] Confirm no step relies on a blanket permission shortcut.

## Write-up

- [ ] Under "What I built," describe the scheme you designed and what the directory is for.
- [ ] Under "Why it's built this way," explain which identity you restricted vs. allowed, and where
      ownership vs. permission bits mattered.
- [ ] Under "How to build it," write a guide to designing and proving a permission scheme, including both
      the denial and the success case.
- [ ] Under "Concepts worth explaining," pick 1–2 ideas (the identity model, or permission bits vs.
      ownership) and explain each in your own words.
- [ ] Under "What tripped me up," capture anything that behaved unexpectedly.
- [ ] Under "Checkpoint evidence," show the permission/ownership state, the denied attempt, and the
      authorized attempt.
- [ ] Under "What I'd do differently," reflect on what you'd design differently.
- [ ] Final self-review: re-read your work against M5's definition of done.
