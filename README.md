# enowX Skill

Curated skill library for Codex-style agents.

Each skill lives in its own folder and should include:
- `SKILL.md` as the skill entrypoint
- `README.md` with usage guidance
- Optional `assets/`, `references/`, and `scripts/` folders

## Available Skills

- [`multi-brain`](./multi-brain/README.md) - Two-level shared memory and handoff system for multi-agent workflows

## Structure

```text
enowX Skill/
├── README.md
└── multi-brain/
    ├── README.md
    ├── SKILL.md
    ├── assets/
    └── references/
```

## Usage

Browse into a skill folder to review its `README.md` and `SKILL.md`.

When publishing new skills to this library:
- Keep each skill self-contained
- Add a dedicated `README.md` per skill
- Prefer English for reusable public documentation
