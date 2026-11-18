# SOURCES & AUDIT TRAIL

This document maps every rule, configuration parameter, CLI command, and constraint in this kit directly to its upstream official source.

---

### 1. Oskar Groth Kit (`OskarGroth/orchestrating-agents`, MIT License)
*Repository: https://github.com/OskarGroth/orchestrating-agents*

- **Role separation (Orchestrator never builds):** `github-oskargroth-orchestration.md.txt` (Lanes and orchestrators, R12, B4–B5; AGENTS.md: "It doesn't write product code.").
- **Visible owners for outcomes:** `github-oskargroth-orchestration.md.txt` (Owners, R14, B6–B9).
- **Three open item states (`moving`, `needs you`, `done`):** `github-oskargroth-orchestration.md.txt` (Own it to done, B10–B11).
- **Worktree & branch isolation:** `github-oskargroth-agents.md.txt` (Working rules), `github-oskargroth-orchestration.md.txt` (Owners, R7).
- **Independent review & recommendation challenge:** `github-oskargroth-orchestration.md.txt` (Review and merging, R22, B12–B15).
- **Merge clearance of one exact commit SHA:** `github-oskargroth-orchestration.md.txt` (Merge clearance, R23–R24, B16–B20).
- **Approve actions, not code:** `github-oskargroth-orchestration.md.txt` (Approve actions, not code, R25).
- **When to ask the human (8 categories & mandate limits):** `github-oskargroth-orchestration.md.txt` (When to ask the human, R27–R33, B21–B22; "The mandate never covers the things in When to ask the human").
- **Plan approval exceptions:** `github-oskargroth-orchestration.md.txt` ("Ask again only for something new from the list above, a higher cost, or a step that wasn't in the plan. Releases to customers, credentials, customer messages and deletions always need their own yes.").
- **Reporting format ("Needs you: ...", lead with user changes):** `github-oskargroth-agents.md.txt` (Reporting, R35, R42, B23).

---

### 2. Claude Code Official Documentation (`code.claude.com/docs/en/`)

- **AGENTS.md support & CLAUDE.md precedence:** Direct loading of `AGENTS.md` occurs only when no `CLAUDE.md` is present in the working directory or above it, supported in Claude Code v2.1.277 or later (*verify in your version*). Memory hierarchy allows `@AGENTS.md` import inside `CLAUDE.md`.
- **Subagent directory (`.claude/agents/`, `~/.claude/agents/`):** Official subagents documentation.
- **Subagent frontmatter schema:** `name`, `description`, `tools`, `disallowedTools`, `model`, `permissionMode`, `isolation`.
- **Subagent invocation syntax:** Mention syntax `@agent-<name>` (or UI picker `@"<name> (agent)"`).
- **Subagent tool inheritance:** Omitting `Agent` from `tools` prevents the subagent from spawning further subagents. Including `Agent` allows subagent spawning.
- **Worktree isolation flag (`isolation: worktree`):** Runs the subagent in a temporary worktree branched from the default branch.
- **Prompt delegation trigger:** Using actionable intent phrases (e.g., "Use proactively when...") in the subagent `description`.
- **File watcher reload limitation:** Restart required when creating the first agent in a newly created `agents/` directory (*verify in your version*).
- **Settings deny rule limitation:** A deny rule on `git push` in `.claude/settings.json` matches the literal command; command variations such as `git -C . push` are not matched. Must be paired with GitHub branch protection.

---

### 3. Codex Official Documentation (`learn.chatgpt.com/docs/`)
*Note: Codex mechanics based on official Codex docs; Oskar's own setup ran on Claude Code.*

- **AGENTS.md lookup:** Hierarchical discovery from repository root to current directory.
- **Config locations (`.codex/config.toml`, `~/.codex/config.toml`):** Project configuration reference.
- **Project trust requirement:** Codex loads project-level configuration (`.codex/config.toml` and `.codex/agents/`) only when the project directory is marked as trusted.
- **Approval policy values (`on-request`, `never`):** Interactive runs use `on-request`; non-interactive `codex exec` uses `never`.
- **Sandbox mode (`workspace-write`):** Controls filesystem modification scope.
- **Branch cleanliness before delegation:** Official Codex recommendation to maintain clean `git status` on the branch before delegating.
- **Custom agent TOML structure (`.codex/agents/<name>.toml`):** Schema requires `name`, `description`, and `developer_instructions`.
- **Subagent sandbox inheritance:** Custom subagents inherit parent sandbox and approval settings; TOML cannot establish separate sandboxes.
- **Configuration fields (`agents.enabled`, `agents.max_concurrent_threads_per_session`, `agents.default_subagent_model`):** Official configuration reference. Max concurrent threads default is internal; any limit in config is an example value (*verify in your version*).
- **Worktree behavior in Codex:** Attached to ChatGPT desktop application (`$CODEX_HOME/worktrees`). CLI worktree flags were not found in the docs we read; verify in your version.


