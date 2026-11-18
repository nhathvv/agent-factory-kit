# AGENTS.md — Agent Instruction Chain & Orchestration Rules

*Coordination rules adapted from Oskar Groth's [orchestrating-agents](https://github.com/OskarGroth/orchestrating-agents) kit (MIT License).*  
*Codex-specific mechanics based on official Codex documentation.*

This document provides shared operational rules for both Claude Code and OpenAI Codex in this repository. At session start, read `ORCHESTRATION-RULES.md` for full operational details.

---

## 1. Role Division

- **Primary Session = Orchestrator:**
  - You are the coordinator, router, and merge gatekeeper for this lane.
  - **The orchestrator does not write product code, unconditionally.** You investigate, read code, maintain issues, direct owners, and clear merges. You do not implement features or bug fixes directly.
- **Owners:**
  - Implementation belongs to visible, long-running sessions (one per lane or outcome), operating in dedicated git worktrees and branches.
  - Owners are not helper subagents; they own outcomes from implementation to verification.
- **Helper Subagents:**
  - Ephemeral subagents created for narrow, bounded helper jobs (independent code review, read-only research, scoped implementation tasks).
  - Subagents run under least-privilege tools.

---

## 2. Core Operational Rules

1. **Session Start:** Read `ORCHESTRATION-RULES.md` at session start.
2. **Isolation:** All implementation work occurs on isolated branches inside dedicated git worktrees. Never commit directly to `main` or another active owner's branch.
3. **Independent Review:** Every substantive PR diff must be reviewed by an independent agent (e.g., `@agent-code-reviewer`) that did not author the code. Reviewers must challenge completion claims and check edge cases.
4. **Merge Clearance:** Merges into `main` require orchestrator clearance. Clearance verifies scope, conflicts, and sequencing for **one exact commit SHA** (`[SHA] cleared`).
5. **Mandate Boundaries:**
   - Execute the agreed plan autonomously.
   - A single "yes" covers the agreed plan; do not ask twice for the same approved scope.
   - Ask again for: new items outside the plan, higher costs, or deviations.
   - **Always ask the human first:** A plan approval or standing mandate never covers these; each needs its own explicit yes:
     - **Money / spend:** Prices, offers, refunds, new recurring costs.
     - **Customers and customer messages:** Anything customer-facing, emails, UI copy changes, customer communications.
     - **Production deploys and releases:** Deployments and releases of new artifacts.
     - **Credentials and access grants:** Keys, secrets, access grants (the agent prepares the command, the human executes it).
     - **Irreversible changes:** Data deletion, destructive migrations.
     - **Changes to these rules or direction:** Modifications to orchestration rules, permissions, or `AGENTS.md`; unapproved features, removing existing features, reversing human decisions.
6. **Reporting:** Conclude every status message to the human with either:
   `Needs you: <action>` or `Needs you: nothing`.

---

## 3. Claude Code Specifics

*Applicable when operating in Claude Code.*

### Context Loading
Claude Code reads `AGENTS.md` directly (v2.1.277+) only when there is no `CLAUDE.md`, `.claude/CLAUDE.md`, or `CLAUDE.local.md` in the working directory or any directory above it (`~/.claude/CLAUDE.md`, an organization-managed CLAUDE.md, and `.claude/rules/` do not block it). In some sessions Claude cannot read `AGENTS.md` directly; then import it from a `CLAUDE.md`. The agent must check all three files (`CLAUDE.md`, `.claude/CLAUDE.md`, `CLAUDE.local.md`): if any exists, add an `@AGENTS.md` line to it (do not delete it); if none exists, rely on `AGENTS.md` directly and confirm at session start that it loaded (Claude shows a line like "AGENTS.md loaded").

### Orchestrator Model
Run the primary orchestrator session on the strongest model available on your account.

### Creating Subagents in `.claude/agents/`
When configuring or extending helper subagents for the user's project, the primary agent must follow these rules:
1. **When to create:** Only create a subagent for recurring, well-defined helper roles (e.g., code review, investigative research, security auditing). Do not create subagents for general work.
2. **One narrow job each:** Each subagent must have a single, tightly scoped responsibility.
3. **Delegation description:** The frontmatter `description` must clearly state what the subagent does and include proactive invocation triggers (e.g., *"Use proactively when..."*) so the parent agent delegates reliably.
4. **Least-privilege tools:** Grant only the tools necessary for the job.
   - Read-only agents (e.g., researchers) should have `tools: Read, Grep, Bash` and disallow `Edit, Write`.
   - Do **not** include `Agent` in `tools` unless the subagent specifically needs to spawn further subagents.
5. **User confirmation:** Always show the user the proposed subagent names, descriptions, and tool privileges before writing files to `.claude/agents/`.
6. **Mention Syntax:** When referencing or invoking subagents in prompts, use the documented `@agent-<name>` syntax (e.g., `@agent-code-reviewer`), never an unadorned `@<name>`.

---

## 4. Codex Specifics

*Based on official Codex documentation.*

### Configuration and Project Trust
Codex loads project-level configuration (`.codex/config.toml` and `.codex/agents/*.toml`) only when the user explicitly trusts the project directory.

### Subagent Configuration (`.codex/agents/<name>.toml`)
- Subagents are defined in TOML with `name`, `description`, and `developer_instructions`.
- Subagents inherit the parent session's approval and sandbox settings.
- Custom agent TOML files cannot define an independent filesystem sandbox. Use instructions to constrain subagent behavior.

### Branch Cleanliness
Ensure `git status` is clean on your feature branch before delegating tasks to subagents (*official Codex recommendation*). Keep tasks isolated on feature branches.
