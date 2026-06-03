This repository is a GitHub Copilot CLI plugin marketplace (`best-skills`).

## Published skills

Published skills live under `plugins/skills/skills/<bucket>/<skill>/SKILL.md`.

Two buckets are published:

- `engineering/` — daily code work (10 skills)
- `productivity/` — daily non-code workflow tools (4 skills)

Every published skill must:

1. Have a valid `SKILL.md` with Agent Skills spec frontmatter (`name`, `description`)
2. Be listed in `plugins/skills/plugin.json` (via the `skills` array referencing the bucket)
3. Be referenced in the top-level `README.md` and in `plugins/skills/README.md`

## Archived skills (not published)

Archived skills live under `skills/` at the repo root:

- `misc/` — kept around but rarely used
- `personal/` — tied to my own setup, not promoted
- `in-progress/` — drafts not yet ready to ship
- `deprecated/` — no longer used

Skills in these buckets must NOT appear in `plugin.json`, `marketplace.json`, or the published README.

## Marketplace manifest

The marketplace is declared in `.github/plugin/marketplace.json`. The marketplace name (`best-skills`) comes from its `name` field. Installation:

```
/plugin marketplace add BeSt-IT-Solutions-GmbH/skills
/plugin install skills@best-skills
```
