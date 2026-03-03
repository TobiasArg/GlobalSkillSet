---
name: syncskills
description: Keep local skills and the GlobalSkillSet repository synchronized. Use this whenever a skill is added, updated, renamed, or reviewed for sync state. Always add new local skills to GlobalSkillSet. Never delete a skill from GlobalSkillSet when it is removed locally unless the user explicitly requests deletion and then reconfirms.
---

# SyncSkills

Keep `/Users/argtobias/.agents/skills` aligned with `/Users/argtobias/GlobalSkillSet` in a safe, append-first way.

## Core policy

1. If a skill is added locally, it must be added to `GlobalSkillSet`.
2. If a skill is removed locally, do not remove it from `GlobalSkillSet` by default.
3. Only delete from `GlobalSkillSet` when both are true:
   - the user explicitly asks for deletion, and
   - the user reconfirms deletion in a second explicit message.

## Paths and source of truth

- Local skills path: `/Users/argtobias/.agents/skills`
- Sync target repository: `/Users/argtobias/GlobalSkillSet`
- Git remote target: `git@github.com:TobiasArg/GlobalSkillSet.git`

For additions and updates, local skill content is the source of truth.
For deletions, preservation in `GlobalSkillSet` is the default behavior.

## Trigger conditions

Use this skill when the user asks to:

- add/install/create a skill
- sync skills to GitHub
- update existing skills in the repository
- check drift between local and repository skills
- remove skills and decide safe retention behavior

## Standard workflow

### 1) Discover state

- List local skill folders in `/Users/argtobias/.agents/skills`.
- List repository skill folders in `/Users/argtobias/GlobalSkillSet`.
- Classify differences as:
  - `new_local`
  - `updated_local`
  - `missing_local_but_in_repo`

### 2) Apply sync policy

- `new_local`: copy into `GlobalSkillSet` and commit.
- `updated_local`: update in `GlobalSkillSet` and commit.
- `missing_local_but_in_repo`: keep as-is unless explicit + reconfirmed delete request exists.

### 3) Commit strategy

- Use one commit per skill change whenever practical.
- Commit message format:
  - add: `feat(skill): add <skill-name>`
  - update: `chore(skill): update <skill-name>`
  - delete (only confirmed): `chore(skill): remove <skill-name>`

### 4) Push policy

- Push to `origin/main` after commits unless user asks otherwise.
- Report commit SHAs and resulting repo status.

## Deletion safety protocol (mandatory)

Before deleting from `GlobalSkillSet`, enforce both checks:

1. Explicit request check: user clearly asks to delete the skill from repo.
2. Reconfirmation check: user confirms deletion again after a warning.

Warning text should clearly include that deletion is permanent in git history progression (even if recoverable through history) and affects shared repository state.

If either check fails, do not delete.

## Output format

Always report:

1. Sync result summary
2. Skills added/updated/kept/deleted
3. Commits created
4. Push status
5. Any policy-blocked deletions

## Guardrails

- Do not force-push.
- Do not rewrite history.
- Do not delete by inference.
- Do not silently skip local additions.
