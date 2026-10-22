# Adapt

Make the skill fit one repository without editing its base files. The output is
`references/project.md` in this skill. Re-runs replace it, keeping entries that are still true.

## Detect first

- The structure in use and the target (from `AGENTS.md` sections, `docs/architecture/` maps, ADRs).
- Layer folders, their aliases (`$lib`, `@/`), and framework-placed folders (routes).
- Marker prefixes already in use and their maps.
- The gates: type check, build, lint, tests, and any boundary tool.
- Companion skills installed (`feature-sliced-design`, `atomic-design-fundamentals`, others).

Ask only for what the repo cannot tell, in one round.

## project.md template

```markdown
# frontend-structure: <repo>

- Structure: <e.g. FSD v2.1 for feature code, Atomic Design for shared UI>
- Direction: <top to bottom, the layer list>
- Layer folders: <layer → path, alias>
- Framework-placed: <e.g. src/routes stays; route files are thin>
- Public entry: <index.ts per slice>
- Markers: <PREFIX-TODO[ID]> → <map path>; <second prefix> → <map path>
- Gates per step: <commands>
- Enforcement: <tool and config path, or "none yet">
- Companion skills: <name → what it owns here>
- Standing exceptions: <each with its reason>
```

Record only what differs from the base skill's defaults. Convert relative statements ("we are
moving to X") into dated facts.
