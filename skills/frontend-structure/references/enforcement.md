# Enforcement

Hand-drawn boundaries are artificial: anyone can import across them with a full path. Tools make
them real. Choose by what the repo already runs; add no tool the team will not keep running.

| Tool                                   | Best for                                                    | Checks                                                                  |
| -------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------------------- |
| `eslint-plugin-boundaries`             | Any ESLint repo, any structure                              | Element types by path, allowed imports between them, private internals |
| `import/no-restricted-paths` (`eslint-plugin-import`) | A few simple zones                          | "Nothing in `shared` imports `pages`", one rule per zone                |
| `steiger`                              | Feature-Sliced Design                                       | Layers, same-layer imports, public API, insignificant slices            |
| `@nx/enforce-module-boundaries`        | Nx monorepos (per domain)                                   | Tags on projects (`scope:`, `type:feature/ui/data-access/util`)         |
| `dependency-cruiser`                   | Rules as data, graphs, CI reports                           | Paths, cycles, orphans, any custom rule                                 |
| `madge`                                | A quick cycle check                                         | Circular dependencies, a graph image                                    |

## Patterns

- **Layers by path.** Declare each layer by a glob (`src/lib/pages/*`, `src/lib/shared/*`,
  `src/components/atoms/*`) and allow only the downward pairs.
- **No sideways.** Same element type, different capture group (`pages/a` → `pages/b`) is denied.
- **Public API.** Allow importing `<slice>/index` only; deny `<slice>/**` from outside the slice.
- **Framework files.** Exclude what the framework places itself (SvelteKit `src/routes`, Next.js
  `app/`); treat it as the top (`app` in FSD) that may import everything below.
- **Svelte.** ESLint rules apply to `.svelte` files through `eslint-plugin-svelte`; check that the
  plugin's parser runs before relying on import rules there.

## Rolling out in a live repo

Run once, count, and record. Prefer "warn plus markers" or an allow-list of today's violations
that may only shrink, over a red build nobody can fix in one change.
