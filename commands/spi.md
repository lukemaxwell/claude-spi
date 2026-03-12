---
description: Spec → Plan → Issues for a feature. Generates SPEC.md and creates GitHub issues.
argument-hint: <feature description>
allowed-tools: [Read, Write, Glob, Grep, Bash(gh issue create:*), Bash(gh issue list:*), Bash(gh label list:*), Bash(gh label create:*), Bash(gh repo view:*), Bash(git rev-parse:*), Bash(git branch:*)]
---

# SPI — Spec · Plan · Issues

**Feature description:** $ARGUMENTS

---

## Your task

Turn the feature description above into a completed spec, then create GitHub issues for every item in the issue breakdown.

Follow the phases below in order. Do not skip phases.

---

## Phase 1 — Clarify

If `$ARGUMENTS` is vague (fewer than ~10 words, no clear scope, or missing acceptance criteria), ask the user **3–5 targeted questions** before continuing. Cover:

- What problem does this solve, and who is affected?
- What does success look like (observable outcome)?
- What is explicitly out of scope?
- Any known constraints (auth, rate limits, backwards compat, stack)?
- Rough size: single PR or multi-issue epic?

Wait for answers before proceeding.

If the description is already specific enough, skip to Phase 2.

---

## Phase 2 — Draft spec

Fill in the spec template below using the feature description (and any answers from Phase 1). Replace every `<placeholder>` with real content. Remove placeholder text entirely — never leave `<…>` in the output.

```
# SPEC: {title}

## Goal
- {one sentence}

## Non-goals
- {explicitly excluded scope}

## Users / scenario
- {who uses it and when}

## Requirements (must)
- [ ] {requirement}

## Nice-to-haves
- [ ] {optional}

## Acceptance criteria (definition of done)
- [ ] {observable outcome}
- [ ] {tests / checks}

## Risks / constraints
- {auth, rate limits, backwards compat, stack specifics}

## Issue breakdown (to create in GitHub)
- {ISSUE 1 title}
  - Description: {what needs to be done}
  - Acceptance: {observable outcome}
  - Test plan: {how to verify}

## PR discipline
- Branch → PR only
- PR body includes: `Closes #…` / `Fixes #…`
- Commands to run (paste output in PR):
  - {linter command}
  - {formatter check}
  - {test command}
```

Show the filled spec to the user. Ask: **"Looks good? Any changes before I write the file and create issues?"**

Wait for confirmation or edits before proceeding.

---

## Phase 3 — Write SPEC.md

Once confirmed, write the spec to `SPEC.md` in the current directory (or a path the user specifies).

Use the Write tool.

---

## Phase 4 — Create GitHub issues

For each item in the **Issue breakdown** section of the spec:

1. Check the repo is a GitHub repo: `git rev-parse --show-toplevel` + `gh repo view --json name`
2. Create the issue using:

```bash
gh issue create \
  --title "{issue title}" \
  --body "{issue body using the issue template below}"
```

**Issue body template:**

```markdown
## Description
{what needs to be done}

## Acceptance criteria
- [ ] {observable outcome}

## Test plan
- [ ] {how to verify}

## Context
Spec: {link or title of parent spec}
```

3. Capture the URL returned by `gh issue create` for each issue.

If `gh` is not available or the directory is not a GitHub repo, print the issue titles and bodies to the terminal instead and tell the user to create them manually.

---

## Phase 5 — Summary

Print a summary table:

| # | Issue | URL |
|---|-------|-----|
| 1 | {title} | {url} |
| … | … | … |

Then: "SPEC.md written ✓  {N} issues created ✓"

---

## Error handling

- If `gh issue create` fails for an individual issue, continue with the rest, then list all failures at the end.
- If SPEC.md already exists, ask before overwriting.
- Never silently skip an issue.
