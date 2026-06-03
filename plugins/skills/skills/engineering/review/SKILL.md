---
name: review
description: >
  Dual-model code review against a Jira ticket's acceptance criteria and branch diff.
  Spawns two parallel reviewers (GPT-5.5 + Claude Opus 4.6), reconciles disagreements,
  and produces a final verdict with an acceptance-criteria checklist.
  Use when user says "review", "code review", wants to review a branch, PR, or mentions a ticket number for review.
---

# Review

Dual-model code review that checks branch changes against a linked Jira ticket's acceptance criteria.

## Process

### 1. Determine scope

Identify the changes to review:

- Base branch: determine via `git merge-base HEAD <base>` (ask user if ambiguous — typically `main` or `develop`).
- Diff: `git diff <merge-base>...HEAD` (three-dot) — includes staged, unstaged, and committed changes on the branch.
- Changed files: `git diff --name-only <merge-base>...HEAD`.
- Commit list: `git log <merge-base>..HEAD --oneline`.

### 2. Read the ticket

The user provides a ticket reference (e.g. `XOSBEC-1234`).

1. Fetch the ticket via `atlassian-mms-jira_get_issue`.
2. Extract from the ticket:
   - **Summary**
   - **Description** (implementation-relevant parts)
   - **Acceptance criteria** — list each criterion as a numbered item
   - **Comments** — any implementation-relevant clarifications
   - **Subtasks** — if linked, check for additional requirements
3. Present the extracted acceptance criteria to confirm scope before proceeding.

If Jira MCP is unavailable, ask the user to paste the acceptance criteria.

### 3. Spawn two reviewers in parallel

Launch two `code-review` sub-agents with identical prompts but different models:

| Reviewer | Model | Reasoning |
|----------|-------|-----------|
| Reviewer A | `gpt-5.5` | high |
| Reviewer B | `claude-opus-4.6` | high |

**Shared prompt for both reviewers:**

```
Review the code changes in this branch compared to its base branch.

Ticket: <TICKET-KEY> — <summary>

Acceptance criteria:
<numbered list of acceptance criteria>

Scope: all changed files in the diff (list attached).

Find:
- Bugs
- Security vulnerabilities
- Logic errors
- Race conditions
- Edge cases
- Missing error handling
- String-concatenated log messages that should be parameterized
- Architectural violations
- Missing or incomplete implementation of the ticket requirements
- Acceptance criteria not fulfilled, partially fulfilled, or fulfilled incorrectly
- Code changes that implement behavior outside the ticket scope without clear reason

Ignore:
- Style, formatting, trivial matters

For each issue found, provide:
1. What the bug or requirement gap is
2. Why it matters
3. Which acceptance criterion it relates to (if applicable)
4. The suggested fix

Acceptance criteria check:
- List each acceptance criterion
- Mark as: ✅ Fulfilled | ⚠️ Partially fulfilled | ❌ Not fulfilled | ❓ Cannot verify from code alone
- Brief reasoning for each status
- If a criterion requires tests, verify whether appropriate tests were added/updated

If nothing is wrong and all acceptance criteria are fulfilled, say so explicitly.
```

### 4. Reconcile findings

After both reviewers respond:

**If they agree** — present the unified findings directly.

**If they disagree** on any finding:

1. List each disputed finding with both reviewers' positions.
2. Recheck only those findings yourself against the actual code.
3. For each disputed finding, decide: **Valid**, **Invalid**, or **Uncertain**.
4. Explain the final decision briefly (1–2 sentences).

### 5. Final report

Present the report in this structure:

```markdown
## Review: <TICKET-KEY>

### Findings

<numbered list of confirmed issues, each with: what / why / criterion / fix>

### Acceptance Criteria Status

| # | Criterion | Status | Reasoning |
|---|-----------|--------|-----------|
| 1 | ... | ✅/⚠️/❌/❓ | ... |

### Reviewer Agreement

- Agreed: X findings
- Disputed: Y findings (resolved: Z valid, W invalid, V uncertain)

### Verdict

<one-line summary: pass / pass with minor issues / fail>
```

## Rules

- Never comment on style, formatting, or trivial matters.
- Only surface issues with genuine impact (bugs, security, logic, requirements).
- Always ground findings in specific code locations (file + line/hunk).
- Always tie findings back to acceptance criteria where applicable.
- If both reviewers find nothing wrong, say so — don't invent issues.
