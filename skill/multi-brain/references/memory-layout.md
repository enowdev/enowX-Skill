# Multi Brain Memory Layout

## Purpose

Use `.multibrain/` as a lightweight shared memory area across agents. Keep the top-level index short, route reads through named sub-index files, and only expand into deeper files when needed.

## Folder Structure

```text
.multibrain/
├── session.md
├── indexes/
│   ├── agents.md
│   ├── auth.md
│   └── deploy.md
└── context/
    ├── 2026-05-11-1735-codex-multi-brain-skill.md
    └── 2026-05-12-0910-claude-login-page.md
```

During repository bootstrap, root instruction files may also be updated:

```text
AGENTS.md
CLAUDE.md
```

These root files should point agents toward the Multi Brain startup flow without replacing repository-specific rules.

## `session.md` Rules

Treat `session.md` as a master index of sub-index files, not a diary.

Required parts per entry:
- Bucket name
- Short scope description
- Last updated timestamp
- Pointer to the sub-index file

Recommended format:

```md
- `agents` — shared notes for agent workflows, prompts, and skill behavior. Last updated: 2026-05-11 17:35 WIB -> .multibrain/indexes/agents.md
```

Guidelines:
- Keep each entry to one line
- Keep the file short enough to scan in under a minute
- Add a new line only when creating a new bucket
- Update `Last updated` when appending work to an existing bucket
- Avoid full file lists when they are not necessary
- Avoid dumping command output

## Init Rules

Treat `multi brain init` as a non-destructive repository bootstrap.

Preferred actions:
1. Create `.multibrain/` structure if missing
2. Create a starter named bucket such as `.multibrain/indexes/agents.md`
3. Update `AGENTS.md` to require reading `.multibrain/session.md` first
4. Update `CLAUDE.md` with the same startup rule when the repository already uses `CLAUDE.md` conventions
5. Preserve all existing root instructions and merge Multi Brain guidance into them

Avoid replacing whole root files just to add Multi Brain support.

## Named Sub-Index Rules

Treat each file in `.multibrain/indexes/` as the main log for one task area.

Useful bucket names:
- Use concise, durable names such as `agents`, `auth`, `deploy`, `testing`, `ui`, `billing`
- Prefer task/topic names over repo-specific names when possible
- Create a new bucket only when existing ones do not fit cleanly

Required parts per entry:
- Timestamp in local repo timezone when possible
- Agent name
- One-sentence summary
- Pointer using `->` when a deeper note exists

Recommended format:

```md
- 2026-05-12 09:10 WIB — Claude Code: fixed the login page and form validation -> .multibrain/context/2026-05-12-0910-claude-login-page.md
```

Guidelines:
- Keep newest entries at the top
- Keep each entry to one line
- Use one bucket per closely related task area
- Prefer a soft cap such as 25 entries per sub-index
- When the cap is exceeded, summarize older entries into one compressed note and keep the bucket readable

## Deep Context File Rules

Open a file in `.multibrain/context/` only when the task needs detail.

Useful sections:
- `## Goal`
- `## Summary`
- `## Changes`
- `## Files`
- `## Verification`
- `## Next`

Avoid unnecessary verbosity. The purpose is recall and handoff, not a complete transcript.

## Read Strategy

Before starting:
1. Read `.multibrain/session.md`
2. Identify the named bucket that best matches the current task
3. Read only the matching sub-index file
4. Read only the pointed context files that matter

During work:
1. Keep notes locally if needed
2. Distill them before writing to `.multibrain/context/`

After work:
1. Create or update one context note
2. Add one short line to the relevant sub-index file in `.multibrain/indexes/`
3. Update the corresponding master index line in `session.md` if needed
4. Summarize older bucket history when the bucket becomes too long

After `multi brain init`:
1. Confirm the `.multibrain/` structure exists
2. Confirm root instruction files now mention Multi Brain startup
3. Add an initialization entry to the `agents` bucket
