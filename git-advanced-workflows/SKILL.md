---
name: git-advanced-workflows
description: Practical Git methods and best practices for clean history and safe collaboration. Use for branch strategy, commit quality, PR hygiene, rebase/cherry-pick/bisect/worktree/reflog workflows, and controlled recovery.
---

# Git Advanced Workflows

Practical guide focused on methods and good practices for day-to-day Git/GitHub collaboration.

## When to Use This Skill

- Defining or enforcing branch, commit, and PR standards
- Cleaning up commit history before opening or merging a PR
- Applying focused fixes across branches with `cherry-pick`
- Finding regressions with `bisect`
- Working on parallel tasks with `worktree`
- Recovering safely from mistakes with `reflog`

## Activation Triggers

Use this skill when a request includes terms like:

- "clean history"
- "fix commits before PR"
- "rebase vs merge"
- "recover lost commit"
- "git workflow standard"
- "branch naming"
- "conventional commits"
- "PR checklist"

## Core Methods

### 1) Interactive Rebase for Local Cleanup

Use only on local or unshared branch history.

- `pick`: Keep commit as-is
- `reword`: Change commit message
- `edit`: Amend commit content
- `squash`: Combine commit and message
- `fixup`: Combine and discard fixup message
- `drop`: Remove commit from local history

Safe flow:

```bash
git fetch origin
git rebase -i origin/main
# resolve conflicts if any
git add <files>
git rebase --continue
```

### 2) Cherry-Pick for Targeted Backports

Use to move one fix without merging full branch context.

```bash
git checkout release/1.9
git cherry-pick <commit_sha>
# conflict handling
git cherry-pick --continue
# abort if needed
git cherry-pick --abort
```

### 3) Bisect for Regression Isolation

Use deterministic test command when possible.

```bash
git bisect start
git bisect bad
git bisect good <known_good_tag_or_sha>
git bisect run npm test
git bisect reset
```

### 4) Worktree for Parallel Streams

Use to avoid stashing/switching churn.

```bash
git worktree add ../repo-hotfix -b hotfix/login-timeout origin/main
git worktree list
git worktree remove ../repo-hotfix
git worktree prune
```

### 5) Reflog for Recovery

Use when commits/branches appear "lost" after reset/rebase.

```bash
git reflog
git branch recover/<ticket> <sha_from_reflog>
```

## Safety Rules (Mandatory)

- Never rewrite shared/public history unless team agreed.
- Prefer `git push --force-with-lease` over `--force`.
- Create backup branch before heavy rewrite:

```bash
git branch backup/<branch>-<date>
```

- Keep working tree clean before rebase/bisect/cherry-pick.
- After any history rewrite, run tests before push.

## Team Policy (Mandatory)

These standards are required for repository consistency, review quality, and traceability.

### Branch Naming

- `feat/<scope>-<short-description>`
- `fix/<scope>-<short-description>`
- `chore/<scope>-<short-description>`
- `docs/<scope>-<short-description>`
- `refactor/<scope>-<short-description>`

Examples:

- `feat/auth-session-refresh`
- `fix/api-timeout-retry`

### Commit Format Policy

Pattern:

`type(scope): short-description`

Types:

- `feat`, `fix`, `refactor`, `docs`, `test`, `chore`

Examples:

- `feat(auth): add refresh token rotation`
- `fix(api): handle 504 retry with backoff`

### Pull Request Policy

- Every PR must include:
- Problem statement (what and why)
- Solution summary (how)
- Impact level: LOW/MEDIUM/HIGH/CRITICAL
- Validation evidence (tests/typecheck/build)
- Risks and rollback notes (if applicable)

### Minimal PR Checklist (Required)

- Scope is single objective
- Commit history is clean and intentional
- CI passes
- Reviewer can test with explicit steps
- No unrelated refactors included

## Decision Guide: Rebase vs Merge

- Rebase when: cleaning local commits and updating feature branch from main.
- Merge when: integrating completed work into shared branch preserving collaboration context.
- Avoid rebasing commits already consumed by others.

## Recovery Toolkit

```bash
git rebase --abort
git merge --abort
git cherry-pick --abort
git bisect reset
git restore --source=<sha> <path>
git reset --soft HEAD^
git reflog
git branch recover/<ticket> <sha_from_reflog>
```
