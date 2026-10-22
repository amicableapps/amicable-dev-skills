# Assess

Describe the structure the code actually has, and where it breaks its own rules. Read-only: no file
moves, no config changes.

## Steps

1. **Name the structure.** Look at the top two levels of `src/` (or `libs/` and `apps/` in a
   monorepo). Match it to per type, per feature, per domain, FSD, Atomic Design, or a mix
   ([architectures.md](architectures.md)). Say "mostly per feature, with a per type `lib/`", not an
   aspiration.
2. **Draw the flow.** List the top-level areas and, for each, which areas it imports. Use the real
   imports (`rg "from '\$lib/..."` or the project's alias), counted. Show it as a short table or an
   arrow list. Mark two-way arrows.
3. **Find the breaks.**
   - Sideways imports: one feature, slice or domain importing another on the same level.
   - Upward imports: a lower area importing a higher one (for example a shared API module importing
     a page's file).
   - Boundary leaks: imports into another folder's internals instead of its `index.ts`.
   - Cycles: run the project's tool if it has one (`npx madge --circular src`, `npx depcruise`), or
     follow the two-way arrows by hand.
   - Overgrown `shared`: files there with a single user.
4. **Check the agent files.** Compare with the structure sections in `AGENTS.md` and their maps:
   markers that exist, steps the map lists as done, rules the code already breaks.
5. **Report** in this order: the structure in one sentence; the flow; the breaks, each with file
   and line and the rule it breaks; the three smallest useful next steps, each one the size of a
   marker. If the repo has no structure section yet, offer `install`.

Keep the report short. Numbers beat adjectives ("14 imports from workflows into case-metadata",
not "heavily coupled").
