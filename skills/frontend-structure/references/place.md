# Place

Answer "where does this go?" for one thing: a file, component, hook, store, query, type, util,
asset or test. Give the folder, the file name, and the reason in one or two sentences.

## Decision order

Use the repo's structure (overlay, `AGENTS.md` maps) first; the generic order is:

1. **Who uses it now?** One screen → next to that screen (its page, feature or slice). Do not plan
   for hypothetical users.
2. **Is it free of business rules?** UI with no product knowledge, a formatter, the HTTP client,
   config → the shared layer (`shared/ui` is the Atomic Design kit; `shared/lib`, `shared/api`,
   `shared/config`). Business-themed is fine there (a logo, a page layout); a product rule is not.
3. **Is it a user action several screens repeat?** With a focused job and its own reason to change
   → a feature (FSD `features`, or a group's `shared` in per feature).
4. **Is it a business object several screens read?** Its types, API calls, queries and words → an
   entity (FSD `entities`, Nx `data-access`).
5. **Otherwise** → keep it with its single user. Duplication across two screens is acceptable until
   the extraction rule holds: a second user now, its own reason to change, one job.

## Inside a slice or feature

- UI in `ui`, state and rules in `model`, backend calls and mapping in `api` (Nx: `data-access`),
  helpers in `lib` (Nx: `utils`), constants in `config`.
- Tests sit beside the file they test.
- Docs that describe the slice sit in the slice (`_docs/`).
- Export through the slice's `index.ts` only what another layer needs.

## Check before answering

- The import the new place would need points down, never sideways or up.
- The move would not create a cycle.
- If the right place does not exist yet, say so; a layer folder is created by the first file that
  needs it, not before. If the move is larger than the current task, give the place and add a
  marker instead (`step` takes it later).
