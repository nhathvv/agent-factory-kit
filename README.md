# Solo Software Factory Setup Kit

*Adapted from Oskar Groth's [orchestrating-agents](https://github.com/OskarGroth/orchestrating-agents) kit (MIT License).*

---

> **Note for the AI Agent:**
> Before setting up this repository, ask the developer the initialization questions in `QUESTIONS.md`. Adapt all roles, lanes, subagents, and orchestration rules to the specific needs of the user's project based on their answers. Do not assume or guess missing details.
>
> **Claude Code AGENTS.md Note:**
> Claude Code reads `AGENTS.md` directly (v2.1.277+) only when there is no `CLAUDE.md`, `.claude/CLAUDE.md`, or `CLAUDE.local.md` in the working directory or any directory above it (`~/.claude/CLAUDE.md`, an organization-managed CLAUDE.md, and `.claude/rules/` do not block it). In some sessions Claude cannot read `AGENTS.md` directly; then import it from a `CLAUDE.md`. Check all three files (`CLAUDE.md`, `.claude/CLAUDE.md`, `CLAUDE.local.md`): if any exists, add an `@AGENTS.md` line to it (do not delete it); if none exists, rely on `AGENTS.md` and confirm at session start that it loaded (Claude shows a line like "AGENTS.md loaded"). See `SETUP.md`.

---

## Overview

This kit provides templates and rules to bootstrap an autonomous "one-person software factory" powered by AI coding agents (Claude Code or OpenAI Codex).

The primary operator of these files is your primary **AI agent session** acting as the lane orchestrator.

### Repository Layout
- `README.md`: This file. High-level architecture and quickstart.
- `SETUP.md`: Step-by-step setup procedure executed by the agent.
- `QUESTIONS.md`: Mandatory initialization questionnaire asked before any files are generated.
- `ORCHESTRATION-RULES.md`: Core rules governing task states, branch isolation, independent review, merge clearance, and human escalation boundaries.
- `AGENTS.md`: Shared instruction chain for both Claude Code and Codex, detailing orchestrator behavior, owner sessions, and tool-specific mechanics.
- `SOURCES.md`: Audit trail linking every rule, flag, and configuration key to its upstream documentation.
- `.claude/agents/`: Example subagent definitions for Claude Code (`code-reviewer.md`, `implementer.md`, `researcher.md`).
- `diagrams/agent-factory-kit.html`: Animated system diagram of the kit.
- `.codex/`: Configuration and subagent definitions for Codex (`config.toml`, `agents/code-reviewer.toml`, `agents/implementer.toml`).

---

## Diagram

Interactive system diagram of the factory (orchestrator, owners, helper subagents, review and merge flow):

- [Open the diagram source](diagrams/agent-factory-kit.html)
- [**View it live in the browser**](https://htmlpreview.github.io/?https://github.com/nhathvv/agent-factory-kit/blob/main/diagrams/agent-factory-kit.html)

---

## Quickstart

1. Copy these files into the root of your project repository.
2. Start your primary agent session:
   - **For Claude Code:** Run `claude` and prompt:
     `Read SETUP.md, ask me the questions in QUESTIONS.md, and initialize the orchestration system.`
   - **For Codex:** Run `codex` and prompt:
     `Read SETUP.md, ask me the questions in QUESTIONS.md, and initialize the orchestration system.`
3. Answer the questionnaire. The agent is strictly instructed to pause until you provide full answers.
4. The agent will adapt rules, review permissions, configure platform subagents, and guide you through an end-to-end baseline task.
