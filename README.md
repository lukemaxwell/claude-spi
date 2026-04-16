# SPI — Spec · Plan · Issues

> One command to go from idea to a structured spec, a full GitHub issue backlog, and autonomous implementation — ready for Claude Code or any AI agent.

---

## The problem

AI coding agents are powerful. But left without structure, they drift — building the wrong thing, missing edge cases, or producing code that's hard to review.

The bottleneck isn't execution. It's **specification**.

---

## The insight

When you give an AI agent a well-structured spec and a numbered issue backlog, everything changes:

- The agent knows exactly what to build and what's out of scope
- Each issue is a discrete, self-contained unit of work with acceptance criteria
- You can hand off to any agent, pause, resume, or swap tools — the context is in GitHub
- Reviews are easier because the intent is documented before the code exists
- Specs accumulate in `specs/` — a permanent record any agent can read to understand project history, past decisions, and direction before starting new work

**Spec-driven AI development** is the pattern that makes Claude Code and similar tools production-grade.

---

## Two commands

### `/spi <description>` — Spec + Issues

Turns a rough idea into a structured spec and a GitHub issue backlog.

```
/spi users should be able to export their data as a CSV from the account page
```

Five steps:

1. **Clarify** — asks 3–5 targeted questions if the description is vague
2. **Draft spec** — fills a structured template (goal, requirements, acceptance criteria, risks)
3. **Confirm** — shows you the spec before writing anything; you approve or edit
4. **Write** — saves `specs/<slug>.md` to disk
5. **Create issues** — runs `gh issue create` for every item in the issue breakdown

Output:

```
✓ specs/csv-export-account-page.md written

#  Issue                                      URL
1  Add CSV export endpoint to account API     github.com/you/app/issues/42
2  Add export button to account settings UI   github.com/you/app/issues/43
3  Add rate limiting to export endpoint       github.com/you/app/issues/44
4  Write integration tests for CSV export     github.com/you/app/issues/45
```

---

### `/spi go` — Autonomous Implementation

Implements open GitHub issues autonomously using TDD, code review, and conflict-safe PRs.

```
/spi go
```

Five phases:

1. **Sync** — pulls latest `main`; aborts if working tree is dirty
2. **Triage** — fetches open issues, groups by theme, identifies file footprints and conflicts; asks you to confirm scope
3. **Sequence** — orders issues to minimise merge conflicts; prints the plan and waits for confirmation
4. **Implement** — for each issue: branch → TDD (red→green→refactor) → **mandatory code review** → rebase onto `main` → PR
5. **Summary** — prints a table of all branches and PRs created

#### Key behaviours

- **Tests first.** Failing tests are written before any implementation.
- **Code review is a hard gate.** The `code-reviewer` agent runs on every issue. CRITICAL/HIGH findings must be fixed before a PR is created.
- **Rebase immediately before push.** Each branch rebases onto `origin/main` just before `git push`, collapsing drift from predecessor PRs merging mid-run. Conflicts are resolved once, here, not in the PR.
- **Squash merge.** Each PR should be squash-merged so `main` history has one clean commit per feature. TDD micro-commits disappear into the squash.
- **Never push to main.** Branch → PR only.

#### Example output

```
## /spi go — complete

| # | Issue | Branch | PR | Status |
|---|-------|--------|----|--------|
| #42 | feat: CSV export endpoint | feat/issue-42-csv-export | #55 | ✅ PR open |
| #43 | feat: export button UI    | feat/issue-43-export-btn | #56 | ✅ PR open |
| #44 | feat: rate limiting       | feat/issue-44-rate-limit | #57 | ✅ PR open |
| #45 | test: integration tests   | feat/issue-45-int-tests  | #58 | ✅ PR open |
```

---

## Why this works

| Without SPI | With SPI |
|-------------|----------|
| Vague prompt → agent guesses scope | Spec defines scope before a line is written |
| Agent builds something adjacent to the goal | Acceptance criteria are explicit checkboxes |
| Hard to pause/resume/hand off | Context lives in GitHub, not a chat window |
| PR review requires reconstructing intent | Intent is documented in the spec and issue |
| One agent, one session | Any agent, any session, any tool |
| Agent starts each session blind to project history | `specs/` gives every agent a full record of what was built and why |
| Merge conflicts accumulate silently | Rebase-before-push surfaces conflicts immediately |
| TDD skipped under time pressure | `/spi go` enforces red→green→refactor on every issue |

---

## Install

```bash
git clone https://github.com/lukemaxwell/claude-spi

# Command
cp -r claude-spi/commands/. ~/.claude/commands/

# Skill (proactive trigger + /spi go)
mkdir -p ~/.claude/skills/spi/templates
cp claude-spi/skills/spi/SKILL.md ~/.claude/skills/spi/SKILL.md
cp claude-spi/skills/spi/templates/*.md ~/.claude/skills/spi/templates/
```

### Requirements

- [Claude Code](https://claude.ai/code) installed
- [`gh` CLI](https://cli.github.com/) authenticated (`gh auth status`)
- A Git repo with a GitHub remote

> If `gh` isn't available, SPI prints formatted issue bodies to the terminal instead of creating them.

---

## Templates

### Spec template (`templates/spec.md`)

```
# SPEC: <title>

## Goal
## Non-goals
## Users / scenario
## Requirements (must)
## Nice-to-haves
## Acceptance criteria (definition of done)
## Risks / constraints
## Issue breakdown (to create in GitHub)
## PR discipline
```

### Issue body template (`templates/issue.md`)

```
## Description
## Acceptance criteria
## Test plan
## Context (link to parent spec)
```

---

## File structure

```
claude-spi/
├── commands/
│   └── spi.md                 # /spi slash command
├── skills/
│   └── spi/
│       ├── SKILL.md           # Proactive skill + /spi go implementation
│       └── templates/
│           ├── spec.md        # Spec template
│           └── issue.md       # GitHub issue body template
└── README.md
```

---

## Philosophy

> Agents are execution engines. Specs are the fuel.

The best AI development workflows share one trait: **the human defines intent precisely, the agent handles implementation**. SPI is the bridge — it forces you to think through a feature for 5 minutes before the agent spends 5 hours on it.

You don't need a heavyweight PM tool or an elaborate agent framework. A structured markdown spec and a numbered GitHub backlog is enough for a capable agent to run autonomously with minimal hand-holding.

Start with `/spi`. Hand off with `/spi go`. Review the PRs.

---

## Contributing

PRs welcome. The templates in `skills/spi/templates/` are the easiest place to start — if your team uses a different issue format or spec structure, fork and adapt them.

---

## License

MIT
