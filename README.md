# Amicable Dev Skills

[![skills.sh](https://skills.sh/b/amicableapps/amicable-dev-skills)](https://skills.sh/amicableapps/amicable-dev-skills)

Portable agent skills for product and engineering work. Compatible with Cursor, Claude Code, Codex, and other agents in the [skills.sh](https://skills.sh) ecosystem.

This repository follows the `npx skills` catalog layout: each skill lives under [`skills/`](./skills) as a self-contained folder with a `SKILL.md`. The CLI auto-discovers those paths — no extra manifest is required.

## Install

```bash
# Interactive — pick which skills and agents to install
npx skills add amicableapps/amicable-dev-skills

# Browse what's here without installing
npx skills add amicableapps/amicable-dev-skills --list

# Install one skill
npx skills add amicableapps/amicable-dev-skills --skill feature-docs-builder

# Install everything, globally (available in every project)
npx skills add amicableapps/amicable-dev-skills --all -g
```

Browse this catalog on skills.sh:

```text
https://skills.sh/amicableapps/amicable-dev-skills
```

Manage installed skills with `npx skills list`, `npx skills update`, and `npx skills remove`.

## Skills

| Skill | What it's for |
| ----- | ------------- |
| [`feature-docs-builder`](./skills/feature-docs-builder/SKILL.md) | Plan a feature into a spec / design / tasks doc pack (and optionally one implementation prompt) before anyone writes code. Use when you want a spec, design notes, task plan, ADR, feature breakdown, or AI-ready handoff. |

## Repository layout

```text
skills/
  <skill-name>/
    SKILL.md          # entry: frontmatter (name + description) + instructions
    references/*.md   # optional — deep docs loaded on demand
    agents/           # optional — agent UI metadata
README.md
AGENTS.md             # conventions for authoring skills in this repo
LICENSE
```

The `skills` CLI discovers `skills/<skill-name>/SKILL.md` (flat) and `skills/<category>/<skill-name>/SKILL.md` (categorised). Do not add a root `SKILL.md` — that short-circuits discovery of the catalog.

## Adding a skill

```bash
npx skills init skills/<skill-name>
```

Then fill in the frontmatter and body, add a row to the table above, and confirm discovery:

```bash
npx skills add . --list
```

See [`AGENTS.md`](./AGENTS.md) for authoring conventions.

## License

[MIT](./LICENSE) © Amicable Apps
