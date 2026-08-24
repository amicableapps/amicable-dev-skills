# AGENTS.md

Instructions for AI agents (and humans) authoring skills in this repository.

## What this repo is

A [skills.sh](https://skills.sh)-compatible catalog of agent skills. One skill = one folder under `skills/` with a `SKILL.md`. Consumers install with:

```bash
npx skills add amicableapps/amicable-dev-skills
```

## Layout

```text
skills/<skill-name>/SKILL.md              # flat (preferred)
skills/<category>/<skill-name>/SKILL.md   # only if the catalog is large enough to categorise
```

`<skill-name>` is kebab-case and must match the `name:` field in the frontmatter. No manifest file is needed — the `skills` CLI auto-discovers these paths.

Never add a `SKILL.md` at the repo root. A root skill short-circuits catalog discovery unless consumers pass `--full-depth`.

## SKILL.md format

```markdown
---
name: my-skill
description: <one line — this is the trigger; see below>
license: MIT
metadata:
  author: amicableapps
  version: "1.0.0"
---

# Title

<the body the agent loads when the skill fires>
```

Required fields: `name`, `description`. Recommended: `license`, `metadata.author`, `metadata.version`.

### The description is the trigger

It is the only part of a skill that stays in the agent's context. The body loads only when the description matches the task. Write it as concrete triggers, not marketing:

- Good: lead with what the skill does, then `Use when …` listing situations, symptoms, and phrases a user might say.
- Bad: `Helps with documentation.`

### The body

- Write for the agent: actionable facts, steps, rules.
- Keep it scannable (headings, short sections, tables, code blocks).
- Keep `SKILL.md` focused. Put heavy material in sibling files and link them so they load only when needed.
- One concern per skill. Split rather than overload.

## Bundling files in a skill

```text
skills/<skill-name>/
  SKILL.md
  references/*.md    # optional — deep detail, read on demand
  scripts/*          # optional — runnable helpers
  assets/            # optional — templates, schemas, static files
  agents/openai.yaml # optional — Codex / agent UI metadata
```

Only `SKILL.md`'s body loads when the skill fires. Everything else loads only when `SKILL.md` references it by relative path. The CLI installs the whole directory, so relative links keep working.

## Adding a skill

1. `npx skills init skills/<skill-name>`
2. Fill in frontmatter + body. Directory name must match `name:`.
3. Add a row to the Skills table in `README.md`.
4. Confirm discovery:

   ```bash
   npx skills add . --list
   ```

5. If `skills-ref` is available: `agentskills validate skills/<skill-name>`

## Conventions

- Bump `metadata.version` (semver) on a meaningful change. Publisher-only metadata (author rename, display name) is not a version bump.
- Conventional Commits for messages (`feat(skill): …`, `docs: …`, `fix: …`).
- Do not put project-specific overlays (`references/project.md`) in the published skill. Those are generated per consuming repo by `adapt` (or equivalent) after install.
