---
name: atomic-commits
description: Commit finished work as small atomic commits. Invoke each time a unit of work is done and its check passes, when uncommitted changes have piled up, or before finishing with unpushed commits. Not at the start of a task.
context: fork
model: sonnet
agent: atomic-commits:committer
---

# Atomic commits

Commit the work in this repo. Read the state with `git status --short` and `git diff --stat`. If there is no repo yet, run `git init -b master` and commit a `.gitignore` first.

## Units

- One commit per change you can describe without "and".
- Mechanical changes (renames, moves, formatting) go first, in their own commit.
- Tests go with the code they test. The lockfile goes with its dependency change.

## Check

Run the project's own check and tests once, silenced (for example `pnpm check >/dev/null && pnpm test >/dev/null`). If the project has none, skip this step. Rerun with output only if it fails. If it fails, don't commit: report the failure.

## Committing part of a file

1. Untracked file: `git add -N <file>` first.
2. `git diff <file> > /tmp/p.patch`
3. Delete the lines that belong to a later commit.
4. `git apply --cached --recount /tmp/p.patch`

Check with `git diff --cached --stat` before committing.

## Messages

Plain imperative subject of 50 characters or fewer, no `feat:`-style prefix. Add a body only when the why isn't obvious.

## Fixups (unpushed commits only)

If a change belongs to an earlier commit: `git commit --fixup=<sha>`, then `GIT_SEQUENCE_EDITOR=: git rebase -i --autosquash <base>`. Never rewrite commits at or before `@{u}`. After a rebase, rerun the check once on HEAD.

## Report

`git log --oneline -n <commits you made>` and nothing else.
