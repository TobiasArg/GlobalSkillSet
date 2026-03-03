---
name: skillauditskill
description: Audit any agent skill for authenticity, security risk, real executable capability, and practical usefulness. Use this whenever the user asks to audit, review, verify, vet, compare, or trust a skill/package before installing or using it, including requests like "is this skill safe", "does this skill actually do something", or "worth using?".
---

# Skill Audit Skill

Audit skills with strict, evidence-backed criteria and produce a clear go/no-go recommendation.

## What this skill does

1. Verifies skill identity and source integrity.
2. Extracts real capabilities from files/scripts (not marketing text).
3. Assesses security exposure and abuse potential.
4. Scores practical usefulness by real workflow value.
5. Produces a final decision with risks and trade-offs.

## When to use

Use this skill when the user asks to:

- audit a skill before install
- compare 2+ skills on safety/usefulness
- validate if a skill is "real" or just prompt fluff
- review a skill repository/file structure for red flags
- decide if a skill is production-appropriate

## Audit workflow

### 1) Identity and provenance

- Confirm repository/org owner, popularity signals, and update recency.
- Confirm target skill path exists and matches claimed name.
- Prefer immutable references (commit SHA) when possible.

Minimum evidence:

- repository metadata (owner, stars/forks/watchers, pushed_at)
- skill file location and frontmatter
- latest commits touching the skill

### 2) Structure and surface area

- Enumerate files under the skill directory.
- Classify by type:
  - `instructions`: `SKILL.md`, references
  - `execution`: scripts, binaries, subprocess calls
  - `assets`: templates/static files
- Note hidden complexity: large script trees, generated artifacts, opaque blobs.

### 3) Real capability extraction

Do not trust descriptions alone. Extract what can actually happen:

- list concrete commands/scripts and required tooling
- identify required runtime dependencies
- identify environment assumptions (CLI tools, API keys, browser, filesystem)
- distinguish:
  - `guidance-only` (textual pattern library)
  - `semi-operational` (some scripts/helpers)
  - `operational` (full runnable workflow)

### 4) Security review

Check for risky behavior patterns:

- network exfiltration vectors
- credential handling and secret leakage risk
- shell execution and destructive operations
- remote code fetch/execute patterns
- prompt-injected unsafe instructions
- misleading or hidden behavior that violates user intent

Classify security risk:

- `LOW`: no suspicious execution paths; bounded behavior
- `MEDIUM`: broad execution powers or poor guardrails
- `HIGH`: destructive/exfiltration potential or unsafe defaults
- `CRITICAL`: malicious behavior or clear abuse patterns

### 5) Utility and fit

Score practical value (1-10 each):

1. `Outcome clarity`: outputs are concrete and testable
2. `Operationality`: can be executed end-to-end in realistic environments
3. `Signal/noise`: instruction quality vs verbosity/overhead
4. `Reusability`: value across repeated tasks
5. `Adoption confidence`: reputation + maturity indicators

Compute a weighted verdict:

- `final_score = 0.25*Outcome + 0.30*Operationality + 0.20*Signal + 0.15*Reusability + 0.10*Adoption`

Interpretation:

- `>= 8.0`: strongly recommended
- `6.5 - 7.9`: recommended with caveats
- `5.0 - 6.4`: situational use only
- `< 5.0`: not recommended

## Required output format

Always return this structure:

1. **Verdict**: `Go` / `Go with caveats` / `No-go`
2. **Security Risk**: `LOW|MEDIUM|HIGH|CRITICAL` + one-line reason
3. **Real Capabilities**: concise bullet list of what it truly can do
4. **Non-Obvious Risks**: concise bullet list
5. **Usefulness Score**: numeric + short rationale
6. **Evidence Used**: repo/paths/scripts checked
7. **Recommendation**: install/use guidance and safer alternative if needed

## Red flags (instant caution)

- commands that disable safety controls without explicit user need
- undocumented network calls or upload behavior
- destructive shell operations in default path
- unclear ownership + low transparency + high claimed capability
- mismatch between description and actual files/scripts

## Good audit behavior

- be strict but fair: distinguish risk from inconvenience
- separate facts from assumptions
- prioritize reproducible evidence
- if evidence is missing, mark as unknown (do not invent)
