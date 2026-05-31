# Multi Brain

Multi Brain is a reusable Codex skill for shared memory across agents.

It helps multiple agents or tools work in the same repository without forcing each agent to reread the full project history. The skill uses a two-level indexing model:
- `.multibrain/session.md` as the master index
- `.multibrain/indexes/*.md` as named topic buckets
- `.multibrain/context/*.md` as deeper handoff or investigation notes

## What It Does

- Preserves short, reusable memory between agent runs
- Reduces token use by routing reads through the most relevant bucket
- Supports `multi brain init` to bootstrap `.multibrain/` and update root agent instruction files
- Encourages summarization when buckets grow too large

## Recommended Layout

```text
.multibrain/
├── session.md
├── indexes/
│   ├── agents.md
│   ├── auth.md
│   └── deploy.md
└── context/
    └── YYYY-MM-DD-HHMM-agent-topic.md
```

## How To Use

### 1. Initialize

Ask the agent to run or follow `multi brain init`.

Expected bootstrap behavior:
- Create `.multibrain/session.md`
- Create `.multibrain/indexes/`
- Create `.multibrain/context/`
- Create a starter bucket such as `.multibrain/indexes/agents.md`
- Non-destructively update root `AGENTS.md` and `CLAUDE.md` so future agents read Multi Brain first

### 2. Read Memory Before Work

Before starting a task:
- Read `.multibrain/session.md`
- Pick the bucket in `.multibrain/indexes/` that best matches the task
- Open only the deeper context files that are actually relevant

### 3. Write Back After Work

After meaningful work:
- Add one short entry to the matching bucket
- Update the master index only if the bucket is new or its summary needs refreshing
- Write a context note when decisions, blockers, verification, or deeper handoff details matter

### 4. Keep Buckets Small

When a bucket gets too long:
- Summarize older entries into a compact context or memory note
- Keep the active bucket readable in under a minute

## Included Files

- `SKILL.md` - Skill instructions for the agent
- `assets/session-template.md` - Master index template
- `assets/sub-index-template.md` - Named bucket template
- `assets/context-template.md` - Deep note template
- `assets/agents-snippet.md` - Reusable snippet for `AGENTS.md`
- `assets/claude-snippet.md` - Reusable snippet for `CLAUDE.md`
- `references/memory-layout.md` - Supporting memory layout guidance

## Notes

- This package is written in English for reuse across repositories.
- The skill is designed to stay universal rather than project-specific.
