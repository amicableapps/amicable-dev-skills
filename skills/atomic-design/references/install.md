# Install

Set a repository up so every agent and person who works in it builds shared UI by Atomic Design and
moves old UI into the kit one small step at a time. The output is a section in `AGENTS.md` and a map
in `docs/architecture/atomic-design.md`. No component moves during `install`.

## Step 1: Detect

- An existing Atomic Design section in `AGENTS.md` / `CLAUDE.md` (for example "Atomic Design
  migration — in progress") and the map it links.
- The marker `<!-- atomic-design -->` in any agent file. If it is there, this is a re-run: update
  that block in place, never add a second one.
- The framework and how a component receives classes (`class`, `className`, `style`, attribute
  fallthrough), so the variant rule names the right props.
- Where tokens live (a CSS file of custom properties, a Tailwind theme, a tokens package), and
  whether there is a second scale beside it.
- Where shared UI lives today: a kit folder by level, a flat `components/`, a vendored primitive
  folder (`ui/`, shadcn and the like) that keeps class props as a legacy seam, and feature folders
  that hold generic-looking UI.
- Existing markers (`ATOMIC-TODO[...]`) and their IDs.
- The quality gates the repo already has (type check, build, lint, tests) and the commands.
- Another structure skill's block (`<!-- frontend-structure -->`): the two sections sit side by
  side, and this one covers shared UI only.

## Step 2: Ask once, only for what is missing

- The kit's folder (default `src/components/{atoms,molecules,organisms,templates}`) and the token
  file (default `src/quarks/tokens.css` or the existing one).
- The marker prefix and ID form. Default `ATOMIC-TODO[AD-xxx]`, or the one the repo already uses.
- Legacy seams to keep (a vendored primitive folder) and whether new code may still use them.
- What shared UI must never import in this repo (routes, stores, API clients, product-specific
  modules): name them.
- Corrections to an existing section or map: lines naming folders or modules that do not exist, or
  saying the opposite of each other.

Present what Step 1 found as "here is what I detected, correct me".

## Step 3: Write the AGENTS.md section

Append after the existing structure sections. Do not rewrite other sections. When an Atomic Design
section already exists, keep it and add only the missing lines inside it; put the marker comments
around the whole section (its old and new lines), so a re-run finds one block. A line in it that
names code that does not exist, or contradicts the map, is proposed as a correction in Step 2 and
changed only on the user's yes. Leave a blank line after the opening comment and before the closing
one, or Prettier reformats the file.

```markdown
<!-- atomic-design -->

## Atomic Design migration — in progress

An incremental refactor, not a redesign or a repository-wide rewrite. Preserve behaviour, copy,
accessibility, responsive layouts and token values. Apply the scout rule: leave the area you work
on a little better than you found it.

- Tokens live in `<token file>`. Shared UI lives in `<kit folder>/{atoms,molecules,organisms,templates}`.
  Higher levels import lower ones, never the reverse. Shared UI does not import <forbidden list>;
  pass content, callbacks and slots downward instead.
- **Variants and props over className.** Atoms declare `className?: never` and forward no caller
  class or style by any name: not through a rest spread, a `class` attribute, a `style` prop, slot
  props or a wrapper. A new look is a new typed variant with a caller now. Layout belongs to the
  parent, in a wrapper it owns. Do not add a differently named escape hatch.
- <Legacy seam, if any: `<folder>` primitives keep their class props while their callers migrate.
  New shared components go into the kit. Do not mass-remove class props from legacy APIs.>
- Prefer an existing shared component over new markup. Extract only when the next small, useful
  step is clear; do not move files merely to fill a level.
- Mark a concrete remaining extraction with `// <PREFIX>-TODO[<ID>]: <small next step and invariant
  to preserve>` (markup `<!-- ... -->`, CSS `/* ... */`). Keep its ID and destination in the map.
- When you meet a marker **in the area of the current task**, take its smallest useful extraction
  before continuing, instead of adding another marker. Remove a marker only when its extraction is
  complete, and update the map. Finding markers in a global search does not authorise migrating
  unrelated screens.
- If an extraction would need a broad rewrite, do a smaller safe part, narrow the marker, and
  record what remains. Tokenise the styles that extraction touches, without normalising
  neighbouring ones.
- Verify each step with <type check command> and <build command>.

Current scope and marker map: [docs/architecture/atomic-design.md](docs/architecture/atomic-design.md).

<!-- /atomic-design -->
```

Fill the placeholders from Steps 1 and 2. Drop the legacy line when there is no legacy seam.

## Step 4: Write the map

`docs/architecture/atomic-design.md`, created if missing. When it exists, keep its headings and
order, correct rows that no longer match the code, and add only the tables it lacks, under the
template's names:

```markdown
# Atomic Design migration

Incremental, by the scout rule. Behaviour, copy and token values stay. <Where the kit lives, what
stays the integration layer, and what shared UI does not import.>

Atoms declare `className?: never`. Layout stays on the parent.

## Quarks

<The token file, what it holds, and the scales that stay as framework utilities.>

## Shared UI

| Level | File | Owns |
| ----- | ---- | ---- |

## Still outside the kit

| File | Level when it moves | Why it stays |
| ---- | ------------------- | ------------ |

## Done

| When | What moved |
| ---- | ---------- |

## Markers

| ID | File | Next step | Destination |
| -- | ---- | --------- | ----------- |
```

Fill "Shared UI" from the kit folders as they are, and "Still outside the kit" with the components
in feature code that import no domain type, route or store and that a second screen uses or repeats
as markup. Add markers only for extractions whose
next step is clear; list the rest as deferred, with the reason (a change of motion, element or
spacing that would alter the look).

## Step 5: Optional enforcement

Offer, do not impose:

- A type check is the enforcement for atoms: `className?: never` makes a caller's class a type
  error.
- A lint rule that forbids the kit importing routes, features or stores (`no-restricted-imports`
  patterns, or the structure lint `frontend-structure rules` writes).

## Step 6: Report

Say what was written and where, which gates each step must pass, and what is not enforced yet. Do
not commit unless the user asks.
