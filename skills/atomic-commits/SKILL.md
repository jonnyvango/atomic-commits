---
name: atomic-commits
description: Commit finished work as small atomic commits. Invoke each time a unit of work is done and its check passes, when uncommitted changes have piled up, or before finishing with unpushed commits. Not at the start of a task. Pass as the argument what changed and why, in a sentence or two, and the check command that passed.
argument-hint: [what changed and why; check that passed]
context: fork
model: sonnet
agent: atomic-commits:committer
---

# Atomic commits

Commit the work in this repo. Read the state with `git status --short` and `git diff --stat`. If `git rev-parse --show-toplevel` fails, there is no repo: report that and stop. Create one with `git init -b master` only if the caller's context asks for a new repo, and commit a `.gitignore` first.

## Context from the caller

$ARGUMENTS

Commit only the changes this context describes. Leave everything else uncommitted. If the context is empty, every change is in scope.

## Units

- One commit per change you can describe without "and".
- Mechanical changes (renames, moves, formatting) go first, in their own commit.
- Tests go with the code they test. The lockfile goes with its dependency change.

## Staging

- Stage files by name. Never `git add -A` or `git add .`.
- Never commit secrets (`.env` files, keys, credentials), build output, logs or dependency folders. Leave them uncommitted.

## Check

If the caller's context names a check that passed, don't rerun it. Otherwise run the project's own check and tests once, silenced (for example `pnpm check >/dev/null && pnpm test >/dev/null`). If the project has none, skip this step. Rerun with output only if it fails. If it fails, don't commit: report the failure.

## Committing part of a file

1. Untracked file: `git add -N <file>` first.
2. `git diff <file> > /tmp/p.patch`
3. Delete the lines that belong to a later commit.
4. `git apply --cached --recount /tmp/p.patch`

Check with `git diff --cached --stat` before committing.

## Hooks

Never pass `--no-verify`. If a hook rewrites files, stage its changes to the files in that commit and commit again. If a hook fails, don't work around it: unstage, then report its output.

## Messages

Plain imperative subject of 50 characters or fewer, no `feat:`-style prefix. Take the why from the caller's context. Add a body only when the subject doesn't make it obvious.

## Fixups (unpushed commits only)

If a change belongs to an earlier commit: `git commit --fixup=<sha>`, then `GIT_SEQUENCE_EDITOR=: git rebase -i --autosquash <base>`. Never rewrite commits at or before `@{u}`. After a rebase, rerun the check once on HEAD.

## Report

`git log --oneline -n <commits you made>`, then each file you left uncommitted, one per line. On failure, report only the failure. Nothing else.
