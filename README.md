# caveman — token-optimization toolkit + multi-agent crew for Claude Code

A suite of Claude Code **skills, subagents, and Python tooling** that cuts LLM token consumption by ~60–75% without losing technical accuracy. Built and used daily as my personal agent stack.

Two halves:

1. **caveman skills** — response-compression modes and compressed output formats for commits, code reviews, and memory files.
2. **cavecrew** — three purpose-built subagents (investigator / builder / reviewer) whose outputs are caveman-compressed, so delegating work costs the main conversation a fraction of the usual context.

## Why

Long agent sessions die of context exhaustion. Subagent results are injected back into the main conversation verbatim — so a verbose subagent quietly eats your context window. Compressing agent *output contracts* (not the reasoning) means ~3× more delegations fit in the same window. The same idea applied to replies, commit messages, and review comments cuts everyday token spend.

## The crew (multi-agent design)

| Agent | Job | Model | Tools | Contract |
|-------|-----|-------|-------|----------|
| `cavecrew-investigator` | Locate code — defs, callers, usages | Haiku (cheap, fast) | Read, Grep, Glob, Bash | `path:line — symbol — ≤6-word note` table; refuses to propose fixes |
| `cavecrew-builder` | Surgical 1–2 file edits | inherit | Read, Edit, Write, Grep, Glob | Caveman diff receipt; hard-refuses 3+ file scope |
| `cavecrew-reviewer` | Diff/file review | inherit | Read, Grep, Bash | One line per finding: `path:line: severity: problem. fix.` No praise, no scope creep |

Design decisions that matter:

- **Model tiering** — the read-only locator runs on Haiku; capability is matched to task cost.
- **Tool restriction** — each agent gets only the tools its job needs (the investigator cannot edit; the reviewer cannot write).
- **Refusal rules** — agents decline out-of-scope work and name the right agent for it, which keeps orchestration honest.
- **Output contracts** — fixed, terse formats so the orchestrator can parse results predictably.
- **Auto-clarity escape hatch** — compression is dropped for security warnings and destructive-action confirmations, where ambiguity is dangerous.

The [`skills/cavecrew/SKILL.md`](skills/cavecrew/SKILL.md) decision guide tells the orchestrating agent *when* to delegate to which crew member versus doing work inline.

## The skills

| Skill | What it does |
|-------|--------------|
| [`caveman`](skills/caveman/) | Session-wide compressed replies; 6 intensity levels from `lite` to classical-Chinese `wenyan-ultra` |
| [`caveman-commit`](skills/caveman-commit/) | Conventional Commits with zero filler |
| [`caveman-review`](skills/caveman-review/) | One-line PR review comments |
| [`caveman-compress`](skills/caveman-compress/) | Compresses memory files (CLAUDE.md, notes) to cut *input* tokens — ships Python compress/validate/benchmark tooling in `scripts/` and a [SECURITY.md](skills/caveman-compress/SECURITY.md) |
| [`caveman-help`](skills/caveman-help/) | Quick-reference card |

## Bonus agent

[`agents/ai-trend-hunter.md`](agents/ai-trend-hunter.md) — an autonomous web-research agent with its own config file, dedup memory log, and report pipeline: it hunts fresh AI news on the live web, deduplicates against what it already reported, writes a dated report file, and returns a sourced digest for email delivery.

## Install

Copy `skills/*` into `~/.claude/skills/` and `agents/*` into `~/.claude/agents/`, then invoke with `/caveman`, `/caveman-commit`, etc., or dispatch the crew via the Agent tool.

## Author

**Madhav Sharma** — B.Tech CSE (IT), Siksha 'O' Anusandhan · [GitHub](https://github.com/Madhav0414)
