# Step

Take one migration step by the scout rule: the smallest useful move in the area of the current
task, then record it.

## Pick the step

1. List markers (`<PREFIX>-TODO[...]`) in the files the current task touches, and in the map.
2. A marker in the area of the task comes first. If none, take the smallest move the map lists for
   that area. Never go hunting in unrelated areas.
3. If the step would need a broad rewrite, choose a smaller safe part, narrow the marker to what
   remains, and say so.

## Make the move

- Move with history (`git mv`), whole files, before editing their content.
- Rewrite imports: outsiders go through the new public entry (`index.ts`); internals use relative
  paths inside the slice.
- Keep behaviour, copy and public API. A rename of an export is a separate, stated change.
- Move tests and slice docs with the code.
- On Windows, a running dev server can lock folders: stop it before renaming directories, or move
  file by file.
- Tooling help: the editor's or the language server's "move file" updates imports; `git grep` for
  the old path afterwards.

## Verify

Run the repo's gates (type check, build, lint, tests). Load the moved screens once in the running
app if the move touched UI. A move is done only when all pass.

## Record

- Remove the marker if the step is complete; otherwise narrow it.
- Add new markers for what the move revealed (each at one import site, with the destination).
- Update the map: a row in "Done", the marker table, and the "Today's code" table.
- If the move shows that a rule or layer was wrong, propose an ADR change; do not silently bend the
  structure.
