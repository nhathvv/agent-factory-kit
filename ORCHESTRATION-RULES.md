# Orchestration Rules: Solo Software Factory

*Attribution: Adapted from Oskar Groth's [orchestrating-agents](https://github.com/OskarGroth/orchestrating-agents) kit (MIT License).*

These rules govern how AI agents collaborate, review code, clear merges, and communicate with the human developer.

---

## 1. Lanes and Roles

- **Lanes:** Work is partitioned into lanes (an area or product with its own priorities).
- **One Orchestrator per Lane:** A long-lived primary session serving as the entry point and gatekeeper.
- **The Orchestrator Never Writes Product Code:** The orchestrator unconditionally does not write product code. It investigates, reads code, assigns work to owners, and clears merges. All real implementation belongs to a visible owner.
- **Owners:** Visible, long-running sessions (one per lane or outcome), operating in their own dedicated git worktree and branch. An owner takes an outcome from implementation and testing through pull request and follow-up.
- **Helper Subagents:** Ephemeral agents spawned for narrow, bounded tasks (independent code review, read-only research, security checks).
- **Routines:** Scheduled background tasks (crash triage, analytics) that report a concise summary back to the lane's orchestrator.
- **Stay in Your Lane:** Forward out-of-scope work to the correct lane verbatim in one message and drop it.

---

## 2. Worktrees and Branch Isolation

- Every owner works in its own git worktree and dedicated branch.
- Never commit directly on another owner's branch. Never push directly to `main`.
- Push work in progress frequently to prevent work from being stranded during session restarts.
- Keep working trees clean before delegating tasks to subagents.

---

## 3. GitHub as the Shared Record

Reading agent chat transcripts is slow and costly. GitHub is the shared external source of truth:
- **One issue per outcome:** Contains the result, mandate, owner, PR links, and next action.
- **One pinned priorities issue per lane:** Maintained by the orchestrator showing direction, Now, Next, Paused, and Needs You.
- **Only three task states exist:**
  - `moving`: An owner is actively working on it.
  - `needs you`: Blocked on a human decision, with the exact question recorded.
  - `done`: Verified against the PR, deploy, or observed running product.
  - *"Waiting", "handed off", or "will follow up" are not valid states.*

---

## 4. Independent Review and Merge Clearance

- **Independent Review:** Every substantive PR must be reviewed by an agent that did not write the code (a fresh subagent or separate session) given the diff, requirements, and evidence. Green CI or the author's summary is not a review. Findings must be fixed or refuted with evidence.
- **Review of Recommendations:** Before presenting a decision to the human, a fresh agent receives raw data without conclusions to form an independent assessment.
- **Merge Clearance Gate:** All merges to `main` must pass through the orchestrator. The owner requests clearance with the PR link and commit SHA. The orchestrator checks scope, ordering, and conflicts, and clears **one exact commit SHA**. The record is a comment on the PR: `[SHA] cleared`.
- **Clearance is not a second review:** Code quality belongs to the reviewer. Clearance only checks conflicts, order, and scope.
- **Approve Actions, Not Code:** Sensitive code (payments, auth, licensing, migrations, releases) merges like any other code under review and clearance, but executing the resulting action (running release scripts, applying migrations, issuing refunds) requires explicit human permission.
- **Explain Security Fixes:** When merging a security fix, provide a plain-language summary: what it protects against, user impact, verification steps, and remaining risks.

---

## 5. When to Ask the Human

Decide everything within the mandate autonomously. However, **a standing mandate never covers the items below.** Ask the human regarding:
1. **Money:** Prices, offers, refunds, new recurring costs.
2. **Customers:** Anything customer-facing (emails, UI copy changes, permissions).
3. **Production:** Deployments and releases of new artifacts.
4. **Credentials:** Keys, secrets, access grants (agent prepares the command; human executes).
5. **Irreversible Changes:** Data deletion, destructive migrations.
6. **Direction:** Unapproved features, removing existing features, reversing human decisions.
7. **The Rules:** Modifications to orchestration rules, permissions, or `AGENTS.md`.
8. **Genuine Uncertainty:** Two reasonable options remain after code investigation and blind review, where picking wrong is costly to reverse.

### How to Ask:
- Present a single recommendation (never an open menu) with a yes-or-no question in at most three plain sentences.
- Batch multiple questions into one numbered message.
- A "no" stays a "no". Never route around a rejection.
- End every update to the human with either:
  `Needs you: <action>` or `Needs you: nothing`.

---

## 6. Communication Protocol

- Lead reports with what changed for the user and what was actually observed (not just passing test numbers).
- Distinguish confirmed causes from guesses, and merged code from deployed features.
- Relay human responses verbatim.
- **Plan Approval & Exceptions:** A single "yes" covers the agreed plan; do not ask twice for the same approved scope. Ask again only for:
  - Something new from the list in Section 5,
  - A higher cost, or
  - A step that was not in the plan.
- **Strict Exceptions:** Releases to customers, credentials, customer messages, and data deletions **always** require their own explicit "yes". A standing mandate never covers them.


