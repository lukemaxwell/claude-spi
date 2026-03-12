---
name: spi
description: >
  Use this skill when the user wants to spec out a feature, write a specification document,
  create GitHub issues from a description, or when they say "SPI", "spec this out",
  "create a spec", "write up issues for", "plan this feature", "spec and issues",
  or "turn this into issues". Guides the Spec → Plan → Issues workflow: draft a structured
  spec from a description, confirm it with the user, write SPEC.md, then create GitHub
  issues for each item in the breakdown using `gh issue create`.
version: 1.0.0
---

# SPI — Spec · Plan · Issues

Turns a plain-language feature description into a structured spec document and a set of
GitHub issues, ready to pick up and work.

## Workflow overview

```
/spi <description>
     │
     ├─ Phase 1: Clarify (ask 3–5 questions if description is vague)
     ├─ Phase 2: Draft spec (fill spec template, show to user, wait for OK)
     ├─ Phase 3: Write SPEC.md
     ├─ Phase 4: Create GitHub issues via `gh issue create`
     └─ Phase 5: Print summary table
```

## When to invoke proactively

Suggest running `/spi <description>` when:
- The user describes a feature but hasn't created a spec or issues yet
- The user pastes a rough idea and asks "what do you think?"
- A PR conversation reveals a missing spec for the work being reviewed

## Templates

### Spec template

Load `templates/spec.md` when filling out a spec. Key sections:

- **Goal** — one sentence
- **Non-goals** — explicitly excluded scope
- **Users / scenario** — who, when, why
- **Requirements (must)** — checkbox list
- **Nice-to-haves** — optional improvements
- **Acceptance criteria** — observable, testable outcomes
- **Risks / constraints** — auth, rate limits, backwards compat
- **Issue breakdown** — one entry per GitHub issue to create
- **PR discipline** — branch/PR rules and commands to paste in PR body

### Issue body template

Load `templates/issue.md` when creating each GitHub issue. Key sections:

- **Description** — what needs to be done
- **Acceptance criteria** — checkbox list of outcomes
- **Test plan** — how to verify the work
- **Context** — link back to the parent spec

## gh CLI operations

```bash
# Check repo is on GitHub
gh repo view --json name,url

# Create an issue
gh issue create --title "..." --body "..."

# List existing issues (to avoid duplicates)
gh issue list --state open --limit 50
```

## Quality gates

- Never leave `<placeholder>` text in a delivered spec or issue
- Every requirement in the spec maps to at least one issue
- Every issue has at least one acceptance criterion and one test plan step
- If `gh` is unavailable, print formatted issue bodies for manual creation
- Ask before overwriting an existing SPEC.md
