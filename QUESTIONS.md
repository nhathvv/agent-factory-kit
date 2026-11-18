# Initialization Questionnaire

> **Rule for the Agent:**
> You MUST ask the human developer the following questions before performing any setup steps. If any answer is missing or unclear, stop and wait. Do not assume, guess, or invent answers.

---

### 1. Communication & Coordination Board
- Which GitHub repository will serve as the shared coordination board for issues, priorities, and PR tracking?
- Are GitHub Issues and Pull Requests enabled and accessible on this repository?

### 2. Lanes & Scope
- What are the distinct lanes (product areas, applications, or subsystems) for your project (e.g., frontend, API service, infrastructure, tooling)?
- Which lane is this primary orchestrator session responsible for?

### 3. Merge Authority & Cleared Actions
- Who has the authority to clear merges into `main`?
  - Does the human developer require approving all merge clearances personally?
  - Or may the orchestrator clear merges to `main` for standard changes once independent review passes?
- Can you confirm that sensitive actions (releases, deployments, credentials, schema migrations, payment changes) always require explicit human approval before execution?

### 4. Budgets & Tool Limits
- What model tier should be used for the primary orchestrator (recommended: the strongest model your account offers) and for helper subagents?
- Are there specific tool restrictions (e.g., sandbox policies, disallowing direct git pushes) to enforce?
- Have you enabled branch protection on `main` in GitHub repository settings?

### 5. Explicit Human Decisions
- Beyond the standard 8 categories (Money, Customers, Production, Credentials, Irreversible changes, Direction, Rule changes, Genuine uncertainty), are there any specific files, directories, or tasks you want reserved exclusively for your decision?

---
*Agent Action: Present these questions in a single message and wait for explicit answers before proceeding to Phase 2 of `SETUP.md`.*


