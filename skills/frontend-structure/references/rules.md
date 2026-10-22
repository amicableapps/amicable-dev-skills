# Rules

Write the import rules of this repository and make a tool check them. Rules nobody checks drift.

## Step 1: State them

From the chosen structure ([architectures.md](architectures.md)) and the principles
([principles.md](principles.md)), write for this repo, with its real folder names:

1. The layers, top to bottom, and the direction (higher imports lower).
2. What may not import sideways (slices, features, domains, top-level folders on one level).
3. The public entry of each importable folder (`index.ts`) and that internals are private.
4. The exceptions, each with its reason (FSD's `@x`, a grouping folder, framework files such as
   SvelteKit routes that must stay where the framework expects them).

Put them in the `Rules` section of the map (`docs/architecture/<structure>.md`). If an
`AGENTS.md` structure section exists, it links the map; do not copy the rules into it.

## Step 2: Enforce them

Pick the tool the repo can run in its existing lint step ([enforcement.md](enforcement.md)), add
the config, and run it once. Expect violations in a live repo: do not fix them all. Either set the
rule to warn and record each existing violation as a marker, or add the rule with an explicit
allow-list of today's violations that only shrinks.

## Step 3: Report

The rules, the tool and where its config lives, the violations found (count and the worst three),
and the markers added for them.
