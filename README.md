# SPI — Spec · Plan · Issues

> One command to go from idea to a structured spec and a full GitHub issue backlog — ready for Claude Code, OpenClaw, or any AI agent to pick up and execute.

---

## The problem

AI coding agents are powerful. But left without structure, they drift — building the wrong thing, missing edge cases, or producing code that's hard to review.

Tools like [OpenClaw](https://github.com/nickscamara/openclaw) and [Paperclip](https://github.com/paperclip-ai/paperclip) solve the execution side well. The missing piece is **upstream**: turning a rough idea into something an agent can act on with precision.

The bottleneck isn't execution. It's **specification**.

---

## The insight

When you give an AI agent a well-structured spec and a numbered issue backlog, everything changes:

- The agent knows exactly what to build and what's out of scope
- Each issue is a discrete, self-contained unit of work with acceptance criteria
- You can hand off to any agent, pause, resume, or swap tools — the context is in GitHub
- Reviews are easier because the intent is documented before the code exists

**Spec-driven AI development** is the pattern that makes Claude Code, OpenClaw, and similar tools production-grade.

---

## What SPI does

```
/spi <feature description>
```

Five steps, one command:

1. **Clarify** — asks 3–5 targeted questions if the description is vague
2. **Draft spec** — fills a structured template (goal, requirements, acceptance criteria, risks)
3. **Confirm** — shows you the spec before writing anything; you approve or edit
4. **Write** — saves `SPEC.md` to disk
5. **Create issues** — runs `gh issue create` for every item in the issue breakdown, capturing URLs

### Example

```
/spi users should be able to export their data as a CSV from the account page
```

SPI asks a couple of clarifying questions (max row count? which fields? auth required?), drafts a spec, waits for your OK, then creates:

```
✓ specs/csv-export-account-page.md written

#  Issue                                      URL
1  Add CSV export endpoint to account API     github.com/you/app/issues/42
2  Add export button to account settings UI   github.com/you/app/issues/43
3  Add rate limiting to export endpoint       github.com/you/app/issues/44
4  Write integration tests for CSV export     github.com/you/app/issues/45
```

Specs accumulate in `specs/` over time. Any agent — in any future session — can scan that
directory to understand what has been built, why decisions were made, and where the project
is headed before starting new work.

---

## The workflow

### Step 1 — Spec it

```
/spi add dark mode toggle to the settings page
```

### Step 2 — Hand it to an agent

Once the issues exist in GitHub, any AI agent can pick them up:

**Claude Code (direct):**
```
Claude, pick up project issue #42 and complete all tasks.
```

**Claude Code (by label/milestone):**
```
Complete all open issues labelled "csv-export" in this repo.
```

**OpenClaw agent:**
```
Spawn SeniorDev and ask him to pick up project #42 in <repo> and complete all tasks.
```

**Unattended loop:**
```
For each open issue in milestone "v1.2", spawn a worker agent, implement the changes,
open a PR with `Closes #N` in the body, and move on to the next issue.
```

The agent has everything it needs in the issue: description, acceptance criteria, test plan, and a link back to the parent spec.

### Step 3 — Review

Because the spec existed before the code, PR review is a diff against stated intent — not a reverse-engineering exercise.

---

## Why this works

| Without SPI | With SPI |
|-------------|----------|
| Vague prompt → agent guesses scope | Spec defines scope before a line is written |
| Agent builds something adjacent to the goal | Acceptance criteria are explicit checkboxes |
| Hard to pause/resume/hand off | Context lives in GitHub, not a chat window |
| PR review requires reconstructing intent | Intent is documented in the spec and issue |
| One agent, one session | Any agent, any session, any tool |

---

## Install

### Via Claude Code plugin system

```bash
git clone https://github.com/lukemaxwell/spi
claude /plugin install ./spi
```

### Manual (no plugin system required)

```bash
git clone https://github.com/lukemaxwell/spi

# Command
cp spi/commands/spi.md ~/.claude/commands/spi.md

# Skill (proactive trigger)
mkdir -p ~/.claude/skills/spi/templates
cp spi/skills/spi/SKILL.md ~/.claude/skills/spi/SKILL.md
cp spi/skills/spi/templates/*.md ~/.claude/skills/spi/templates/
```

### Requirements

- [Claude Code](https://claude.ai/code) installed
- [`gh` CLI](https://cli.github.com/) authenticated (`gh auth status`)
- A Git repo with a GitHub remote

> If `gh` isn't available, SPI prints formatted issue bodies to the terminal instead — you can paste them manually.

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

Both templates live in `skills/spi/templates/` and are embedded in the command — no external file reads required at runtime.

---

## File structure

```
spi/
├── .claude-plugin/
│   └── plugin.json            # Plugin metadata
├── commands/
│   └── spi.md                 # /spi slash command
├── skills/
│   └── spi/
│       ├── SKILL.md           # Proactive skill trigger
│       └── templates/
│           ├── spec.md        # Spec template
│           └── issue.md       # GitHub issue body template
└── README.md
```

---

## Philosophy

> Agents are execution engines. Specs are the fuel.

The best AI development workflows I've seen share one trait: **the human defines intent precisely, the agent handles implementation**. SPI is the bridge — it forces you to think through a feature for 5 minutes before the agent spends 5 hours on it.

You don't need a heavyweight PM tool or an elaborate agent framework. A structured markdown spec and a numbered GitHub backlog is enough for a capable agent to run autonomously with minimal hand-holding.

Start with `/spi`. Hand off to the agent. Review the PR.

---

## Contributing

PRs welcome. The templates in `skills/spi/templates/` are the easiest place to start — if your team uses a different issue format or spec structure, fork and adapt them.

---

## License

MIT
