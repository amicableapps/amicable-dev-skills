# Install

Set a repository up so every agent and person who works in it follows the chosen structure, one
small step at a time. The output is a section in `AGENTS.md`, a map in `docs/architecture/`, and a
marker convention. No source file moves during `install`.

## Step 1: Detect

- Existing structure sections in `AGENTS.md` / `CLAUDE.md` (for example "Atomic Design migration —
  in progress", "Feature-Sliced Design migration — in progress") and the maps they link.
- The marker `<!-- frontend-structure -->` in any agent file. If it is there, this is a re-run:
  update that block in place, never add a second one.
- The current tree (`assess`, quick form) and the framework (routes folder, aliases, monorepo).
- The quality gates the repo already has (type check, build, lint, tests).

## Step 2: Ask once, only for what is missing

- Which structure is the target (from `choose`, or ask). If a structure section already exists
  (FSD, Atomic Design), the default is to keep it and add only what it lacks.
- The marker prefix. Default: one per structure, matching existing ones (`FSD-TODO`,
  `ATOMIC-TODO`); for a new structure `STRUCT-TODO[ST-xxx]`.
- Whether to add enforcement now (`rules`) or later.

## Step 3: Write the AGENTS.md section

Append after the existing structure sections. Do not rewrite other sections.

```markdown
<!-- frontend-structure -->

## <Structure> migration — in progress

Same mode as the other migrations here: an incremental refactor, not a rewrite. Preserve behaviour,
copy and public APIs. Apply the scout rule: leave the area you work on a little better than you
found it.

- Target structure, layers and direction of dependencies: [docs/architecture/<structure>.md](docs/architecture/<structure>.md).
  Imports point one way only. Nothing imports sideways on the same level, nothing imports upwards,
  and outside code imports a folder only through its public entry (`index.ts`).
- New code starts next to where it is used. It moves down a layer only when a second user exists
  now, it changes for its own reason, and it has one job.
- Mark a concrete remaining step with `// <PREFIX>-TODO[<ID>]: <small next step and invariant to
  preserve>` (Svelte or HTML markup `<!-- ... -->`, CSS `/* ... */`). Keep each ID and its
  destination in the map.
- When you meet a marker **in the area of the current task**, take its smallest useful step before
  continuing, instead of adding another marker. Remove a marker only when its step is complete, and
  update the map. Finding markers in a global search does not authorise migrating unrelated areas.
- If a step would need a broad rewrite, do a smaller safe part, narrow the marker, and record what
  remains.
- Verify each step with the repository's gates: <type check command> and <build command>.

<!-- /frontend-structure -->
```

Fill `<Structure>`, `<PREFIX>`, the doc path and the gate commands from Step 1 and 2. If the repo
already has an FSD or Atomic Design section that says the same, do not duplicate it: add only the
missing lines (usually the direction-of-dependencies line and the "start next to the use" line)
inside the existing section, and still place the marker comments around what you added. Leave a blank line after the opening comment and before the closing one, or Prettier
reformats the file.

## Step 4: Write the map

`docs/architecture/<structure>.md`, created if missing, otherwise updated:

```markdown
# <Structure> migration

Incremental, by the scout rule. Behaviour and copy stay. The tables below are the target; "Done"
lists what has moved.

## Direction and layers

| Layer | Where it lives here | Holds | May import |
| ----- | ------------------- | ----- | ---------- |

## Rules

1. …(from `rules`)

## Done

| When | What moved |
| ---- | ---------- |

## Today's code, and where it would go

| Today | Probable destination | Note |
| ----- | -------------------- | ---- |

## Markers

| ID | File | Next step | Destination |
| -- | ---- | --------- | ----------- |
```

## Step 5: Report

Say what was written and where, which gates each step must pass, and what is not enforced yet
(offer `rules` if no lint enforces the direction). Do not commit unless the user asks.
