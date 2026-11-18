# Orchestrator Setup Workflow

> **Note for the AI Agent:**
> You must adapt roles, lanes, subagents, and rules to the user's specific project after asking the questions in `QUESTIONS.md`. Do not guess answers or proceed with generic defaults. If any information is missing, stop and ask.

---

## Compatibility & Tool Warnings

1. **Claude Code `AGENTS.md` Support:**
   Claude Code reads `AGENTS.md` directly (v2.1.277+) only when there is no `CLAUDE.md`, `.claude/CLAUDE.md`, or `CLAUDE.local.md` in the working directory or any directory above it (`~/.claude/CLAUDE.md`, an organization-managed CLAUDE.md, and `.claude/rules/` do not block it). In some sessions Claude cannot read `AGENTS.md` directly; then import it from a `CLAUDE.md`. Check all three files:
   - If any of `CLAUDE.md`, `.claude/CLAUDE.md`, or `CLAUDE.local.md` exists, do **not** delete it; add an `@AGENTS.md` line to it to import shared rules.
   - If none exists, rely on `AGENTS.md` and confirm at session start that it loaded (Claude shows a line like "AGENTS.md loaded").

2. **Git Push Safeguards:**
   Configuring a client-side deny rule on `git push` (such as in `.claude/settings.json`) is not a complete safeguard. Variants such as `git -C . push` or wrapper scripts will bypass simple command matching. You must also protect the `main` branch directly in GitHub repository settings (require pull requests and status checks).

3. **Codex Project Trust:**
   Codex loads project-level configuration layers (`.codex/config.toml` and `.codex/agents/`) only when the user explicitly trusts the project directory (*based on official Codex docs*). Verify trust settings before relying on project-level configurations.

---

## Phase 1: Intake & Questionnaire

1. **Agent Action:** Read `QUESTIONS.md`. Present all questions to the human developer in one structured message.
   - At minimum, obtain:
     1. Which GitHub repository acts as the coordination board;
     2. Defined lanes, products, or subsystems;
     3. Who may merge (human only vs. orchestrator clearance for standard PRs);
     4. Budgets and tool limits (models, sandbox, push restrictions);
     5. Decisions reserved exclusively for the human.
2. **Stop Condition:** **Stop and wait.** Do not create files, modify tools, or start owners until the developer provides complete answers. If any answer is ambiguous, ask a focused clarifying question.

---

## Phase 2: Core Orchestration Documents Setup

1. **Agent Action:**
   - Verify `ORCHESTRATION-RULES.md` and `AGENTS.md` at the project root.
   - For Claude Code: Check for an existing `CLAUDE.md`, `.claude/CLAUDE.md`, or `CLAUDE.local.md` in the working directory or any directory above it. If any exists, add an `@AGENTS.md` line to it (do not delete it). If none exists, rely on `AGENTS.md` directly and confirm at session start that it loaded (Claude shows a line like "AGENTS.md loaded").
   - Set up the GitHub tracking structure (coordination issues and labels for `moving`, `needs you`, `done`).
2. **Stop Condition:** Present the adapted core rules and tracking layout to the developer:
   *"Approve the adapted orchestration rules and tracking configuration?"*
   Proceed only upon receiving an explicit "yes".

---

## Phase 3: Platform Subagent & Permissions Configuration

1. **For Claude Code:**
   - Run the orchestrator session on the strongest model available on the user's account.
   - Inspect project needs and propose narrow helper subagents in `.claude/agents/` (e.g., code reviewer, read-only researcher).
   - Show the proposed list of subagents with their tools and trigger descriptions to the user before writing files.
   - Apply least-privilege tools: give subagents only the tools they require (e.g., do not grant `Agent` unless subagent spawning is required; do not grant `Edit` or `Write` to research subagents).
   - Note: If creating `.claude/agents/` for the first time in a new directory, a session restart may be required for the file watcher to detect them (*verify in your version*).
   - Remind the user to enable GitHub branch protection on `main` in addition to any local settings.

2. **For Codex (*Based on official Codex docs*):**
   - Verify project trust so `.codex/` configuration is loaded.
   - Place project custom subagents in `.codex/agents/<name>.toml` defining `name`, `description`, and `developer_instructions`.
   - Configure sandbox policy (`workspace-write`) and approval policy (`on-request` for interactive work, `never` for non-interactive `codex exec` runs) in `.codex/config.toml`.
   - Avoid hardcoding model names; specify using the strongest model the account offers. Any concurrency limit must be commented as an adjustable example.

3. **Stop Condition:** Present the configured subagent definitions and permission settings to the user for confirmation.

---

## Phase 4: Baseline Run

1. **Agent Action:**
   - Ask the developer to designate a single real task as an Outcome issue on GitHub.
   - Have an owner session pick up the outcome in its own dedicated worktree and branch.
   - Run independent code review before merge clearance.
   - Orchestrator clears exactly one commit SHA after verification passes.
2. **Stop Condition:** Complete this initial outcome through review and merge. Do not scale up multiple lanes until the baseline workflow is confirmed end-to-end.