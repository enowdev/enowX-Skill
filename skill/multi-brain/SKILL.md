---
name: multi-brain
description: This skill should be used when work needs to be shared or remembered across Claude Code, OpenCode, Codex, or other agents through a two-level memory index in `.multibrain/session.md`, named sub-index files in `.multibrain/indexes/`, and selective deep context files in `.multibrain/context/`.
---

# Multi Brain

## Overview

Enable shared working memory across multiple coding agents without forcing every agent to read every detail. Keep `.multibrain/session.md` as a small master index, store task-oriented entry lists in `.multibrain/indexes/`, and store fuller context only in pointed markdown files inside `.multibrain/context/`.

## Use This Skill

Use this skill when any task should leave behind resumable context for another agent or tool, especially when switching between Claude Code, OpenCode, Codex, or long-running sessions.

Typical triggers:
- Continue work started by another agent
- Leave a breadcrumb trail before ending a session
- Build long-term memory without bloating the main context window
- Share task progress across tools in the same repository
- Recall prior work by reading only the bucket that matches the current task

Do not use this skill for one-off throwaway tasks when no follow-up context is needed.

## Workflow

### 0. Support `multi brain init`

When the user asks to initialize Multi Brain, treat the request as a bootstrap operation for the current repository.

During `multi brain init`:
- Create `.multibrain/session.md` if missing
- Create `.multibrain/indexes/` if missing
- Create `.multibrain/context/` if missing
- Create at least one starter bucket such as `.multibrain/indexes/agents.md`
- Inspect root-level agent instruction files such as `AGENTS.md` and `CLAUDE.md`
- Update existing root instruction files non-destructively so they direct future agents to read Multi Brain first

Do not delete existing root instructions. Preserve repo-specific rules and append or merge Multi Brain guidance into the existing files.

### 1. Read The Master Index First

Read `.multibrain/session.md` before starting work.

Treat `session.md` as a master index only:
- Read bucket names and short scope descriptions
- Choose the most relevant bucket for the current task
- Do not assume every bucket or pointed file must be opened

If `.multibrain/session.md` does not exist, create:
- `.multibrain/session.md`
- `.multibrain/indexes/`
- `.multibrain/context/`

Use the template in `assets/session-template.md` when bootstrapping the folder. Use `assets/sub-index-template.md` when creating a new named bucket.

When root instruction files exist:
- Read them before editing
- Add a short Multi Brain section near the top or in the mandatory startup section
- Instruct agents to read `.multibrain/session.md` first
- Instruct agents to open pointed `.multibrain/indexes/*.md` files selectively
- Instruct agents to append new memory after meaningful work

When root instruction files do not exist:
- Create `AGENTS.md` with minimal Multi Brain bootstrap guidance
- Create `CLAUDE.md` only when that file pattern is already part of the repo convention or the user explicitly wants it

### 2. Follow The Relevant Named Bucket

Open the most relevant file under `.multibrain/indexes/` based on the task being worked on. Use named buckets such as `auth.md`, `deploy.md`, `ui.md`, `agents.md`, or other concise task-oriented names.

Prefer this decision rule:
- Read only `session.md` when the task is unrelated to prior work
- Read one or two named sub-index files when the task clearly matches their scope
- Read one or more context files only when the chosen sub-index points to details that matter

Keep the memory nested:
- `session.md` stays short
- `.multibrain/indexes/*.md` stores concise task entries for a named area
- Full reasoning, decisions, blockers, changed files, and follow-up notes go in pointed context files

Detailed structure guidance lives in `references/memory-layout.md`.

### 3. Write Into The Right Named Bucket After Work

After completing meaningful work, add one concise entry to the most relevant file in `.multibrain/indexes/`.

Keep each sub-index entry short:
- Include timestamp
- Include agent name
- Include a short description of what was done
- Include a pointer using `->` to the full context file
- Keep newest entries at the top

Do not write full explanations in a sub-index file.

Then update `.multibrain/session.md` only as needed:
- Add a new named bucket entry when creating a new bucket
- Refresh `last_updated` or short summary for an existing bucket
- Keep master index descriptions short and stable

For initialization work, prefer creating or updating an `agents` bucket first so later agent-behavior decisions have a stable home.

### 4. Write A Full Context File Only When Useful

Write a new markdown file in `.multibrain/context/` when the work contains details worth preserving for future agents.

Include only information that improves handoff quality:
- Goal
- What was changed
- Important decisions
- Files touched
- Commands or verification that mattered
- Known follow-up items

Skip a deep context file only when the task is truly trivial and the short index entry is enough.

Use the template in `assets/context-template.md` when helpful.

### 5. Maintain Bucket Size With Summaries

Keep each named sub-index bounded. Prefer a soft cap such as 25 entries per file.

When a bucket grows beyond the cap:
- Summarize older entries into a compact note under `.multibrain/context/` or `.multibrain/memory/`
- Replace many old entries with one compressed summary entry when appropriate
- Preserve pointers to any context files that still matter
- Keep the active bucket readable in under a minute

Prefer summarizing and compressing over deleting raw history without replacement.

## Format Rules

Follow these rules consistently:
- Keep `session.md` human-scannable in under a minute
- Treat `session.md` as an index of indexes, not the main work log
- Keep named sub-index entries newest-first
- Use short, stable bucket names
- Use relative repository paths in pointers when possible
- Prefer filenames such as `YYYY-MM-DD-HHMM-agent-topic.md`
- Use markdown only; no JSON, YAML, or verbose logs in indexes
- Record facts, not stream-of-consciousness notes
- Update root instruction files by insertion or merge, never by destructive replacement unless the user explicitly asks for a rewrite

## Entry Examples

Master index entry:

```md
- `agents` — shared notes for agent workflows, prompts, and skill behavior. Last updated: 2026-05-11 17:35 WIB -> .multibrain/indexes/agents.md
```

Named sub-index entry:

```md
- 2026-05-12 09:10 WIB — Claude Code: fixed the login page and form validation -> .multibrain/context/2026-05-12-0910-claude-login-page.md
```

## Resources

- Read `references/memory-layout.md` for the recommended nested indexing structure.
- Copy from `assets/session-template.md` when creating a new `.multibrain/session.md`.
- Copy from `assets/sub-index-template.md` when creating a new named sub-index file.
- Copy from `assets/context-template.md` when creating a new full context note.
- Reuse `assets/agents-snippet.md` and `assets/claude-snippet.md` when inserting Multi Brain startup guidance into root instruction files.
