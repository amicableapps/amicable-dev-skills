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
| [`atomic-design`](./skills/atomic-design/SKILL.md) | Build and keep a shared UI kit by Atomic Design: which level a component belongs to, typed variants and props over `className`, and the scout-rule migration of old UI into the kit; `install` writes the rules into a repo's AGENTS.md with a marker map. Use when asking where a component goes, whether it may take `className`, or how to extract shared UI step by step. |
| [`feature-docs-builder`](./skills/feature-docs-builder/SKILL.md) | Plan a feature into a spec / design / tasks doc pack (and optionally one implementation prompt) before anyone writes code. Use when you want a spec, design notes, task plan, ADR, feature breakdown, or AI-ready handoff. |
| [`find-rest-combos`](./skills/find-rest-combos/SKILL.md) | Find combos in a frontend backed by a REST API: pick or create inline, a "what next?" step after saving, one action with two effects, dead ends fixed in place. Each is checked against the API's endpoints and roles, scored and written up as a document. Use when a form makes users leave to create something and come back, or when you have one example and want more like it. |
| [`frontend-architecture-review`](./skills/frontend-architecture-review/SKILL.md) | Review a frontend diff or branch before a merge against the repo's architecture, shared UI, framework, API, security and privacy (OWASP, LLM features), design system, accessibility and testing conventions; `install` writes the repo's project layer. Use when you want a branch, staged or recent changes reviewed for architecture and code quality. |
| [`frontend-structure`](./skills/frontend-structure/SKILL.md) | Decide and keep a frontend's folder structure: per type, per feature, per domain, Feature-Sliced Design or Atomic Design; dependency direction, boundaries and import rules; `install` writes the scout-rule section into a repo's AGENTS.md. Use when asking where a file belongs, whether an import is allowed, or how to reorganise folders step by step. |

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
