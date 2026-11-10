# Step

Take one extraction by the scout rule: the smallest useful move of UI into the kit, in the area of
the current task, then record it.

## Pick the step

1. List `ATOMIC-TODO` markers (or the repo's prefix) in the files the current task touches, and in
   the map.
2. A marker in the area of the task comes first. If none, take the smallest extraction the map
   lists for that area, or duplicated markup the task is already changing. Never go hunting in
   unrelated screens.
3. If the step would need a broad rewrite, choose a smaller safe part, narrow the marker to what
   remains, and say so.

## Make the extraction

- Start from the most reused, least opinionated piece: usually an atom or a token the markup
  already repeats.
- Copy the current look exactly. Class strings move with their values; differences between callers
  become typed variants, or the callers stay apart and the map says why.
- Give the component its props by the variant rule: content, callbacks and typed variants in;
  `className?: never` on atoms; layout stays on each caller in a wrapper.
- Replace the old markup in the callers inside the task's area. Callers elsewhere keep working
  through the old code, labelled as a compatibility seam, until their own task reaches them.
- Tokenise the styles you moved. Leave neighbouring styles as they are.

## Record it

- Remove the marker only when its extraction is complete; otherwise narrow it.
- Update the map: the component in "Shared UI", a row in "Done", the marker row removed or
  narrowed, and anything deferred with its reason.

## Verify

Run the repo's gates (type check and build at least, plus lint and tests where they exist). Look at
each changed screen once in the running app: same look, same keyboard path, same responsive layout.
A step is done only when all pass.
