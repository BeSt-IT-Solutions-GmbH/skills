# Skills Plugin

Engineering and productivity agent skills for real software work.

## Installation

```
/plugin marketplace add BeSt-IT-Solutions-GmbH/skills
/plugin install skills@best-skills
```

## Engineering

Skills for daily code work.

- **[create-bug-ticket](./skills/engineering/create-bug-ticket/SKILL.md)** — Create a DoR-conformant bug ticket for the XOSBEC project via Atlassian MCP.
- **[diagnose](./skills/engineering/diagnose/SKILL.md)** — Disciplined diagnosis loop for hard bugs and performance regressions: reproduce → minimise → hypothesise → instrument → fix → regression-test.
- **[improve-codebase-architecture](./skills/engineering/improve-codebase-architecture/SKILL.md)** — Find deepening opportunities in a codebase, informed by the domain language in `CONTEXT.md` and the decisions in `docs/adr/`.
- **[prototype](./skills/engineering/prototype/SKILL.md)** — Build a throwaway prototype to flesh out a design.
- **[refinement](./skills/engineering/refinement/SKILL.md)** — Prepare a Jira ticket for refinement: parse XML export, explore codebase, collect open questions, produce a phased implementation sketch with hour-range estimates.
- **[review](./skills/engineering/review/SKILL.md)** — Dual-model code review (GPT-5.5 + Claude Opus 4.6) against a Jira ticket's acceptance criteria with reconciliation of disagreements.
- **[setup-matt-pocock-skills](./skills/engineering/setup-matt-pocock-skills/SKILL.md)** — Scaffold the per-repo config (issue tracker, triage label vocabulary, domain doc layout) that the other engineering skills consume.
- **[tdd](./skills/engineering/tdd/SKILL.md)** — Test-driven development with a red-green-refactor loop. Builds features or fixes bugs one vertical slice at a time.
- **[to-issues](./skills/engineering/to-issues/SKILL.md)** — Break any plan, spec, or PRD into independently-grabbable GitHub issues using vertical slices.
- **[to-prd](./skills/engineering/to-prd/SKILL.md)** — Turn the current conversation context into a PRD and submit it as a GitHub issue.
- **[zoom-out](./skills/engineering/zoom-out/SKILL.md)** — Tell the agent to zoom out and give broader context or a higher-level perspective on an unfamiliar section of code.

## Productivity

General workflow tools, not code-specific.

- **[caveman](./skills/productivity/caveman/SKILL.md)** — Ultra-compressed communication mode. Cuts token usage ~75% by dropping filler while keeping full technical accuracy.
- **[grill-me](./skills/productivity/grill-me/SKILL.md)** — Get relentlessly interviewed about a plan or design until every branch of the decision tree is resolved.
- **[handoff](./skills/productivity/handoff/SKILL.md)** — Compact the current conversation into a handoff document so another agent can continue the work.
- **[write-a-skill](./skills/productivity/write-a-skill/SKILL.md)** — Create new skills with proper structure, progressive disclosure, and bundled resources.

## Personal

Skills tied to my own setup.

- **[edit-article](./skills/personal/edit-article/SKILL.md)** — Edit and improve written articles.
- **[obsidian-vault](./skills/personal/obsidian-vault/SKILL.md)** — Manage and organize an Obsidian vault.
- **[scan-organizer](./skills/personal/scan-organizer/SKILL.md)** — Organize scanned PDFs: split multi-document scans, rename as `YYYYMMDD - Absender - Betreff.pdf`.
