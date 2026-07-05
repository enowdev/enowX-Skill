# Skills (community Skill registry)

enowX has a community registry of Skills. Anyone signed in can upload a skill;
every upload is security-scanned, then committed to the public
`enowdev/enowX-Skill` GitHub repo and indexed.

## Browse / publish (API)

- `GET /api/registry?kind=skill&q=` — browse published skills.
- `GET /api/registry/{id}` — one skill's detail + its GitHub folder URL.
- `POST /api/registry/publish` — publish a skill by sending its files
  (`{ kind:"skill", name, description, version, files:[{path, content(base64)}] }`).
  In the dashboard's **Skills** app you just pick the skill's folder; name,
  description and version are auto-read from its `SKILL.md` frontmatter.

## Install (CLI)

```bash
enx skill install <slug>       # into ./.agents/skill/<slug>   (this project)
enx skill install <slug> -g    # into ~/.agents/skill/<slug>   (global)
```

The installer sparse-checkouts `skill/<slug>/` from the enowX-Skill repo (git is
required). It refuses to overwrite an existing install.

## Skill format

A skill is a folder with a `SKILL.md` that has YAML frontmatter (`name`,
`description`), optional `assets/` and `references/` — the same layout as this
skill.
