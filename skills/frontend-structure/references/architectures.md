# Architectures

Five structures this skill knows, from the simplest to the most demanding, and how they combine. Any
of them can build the same screen; they differ in how well they scale and how much discipline they
need.

## Per type

```text
src/
  assets/
  components/
  consts/
  containers/
  services/
  utils/
```

Files grouped by what kind of thing they are.

- **Flow (when done deliberately).** `containers` → `components` and `services`; `components` →
  `assets` and `utils`; `services` → `utils`; `utils` → `consts`. `components` and `services` never
  import each other, nor do `assets` and `utils`. This is not a strict layer stack: some folders sit
  side by side, so the arrows must be written down explicitly.
- **Rules.** Files directly in a top-level folder do not import each other. To let two services
  share code, put them in a grouping folder (`services/account/`), and inside it they may. Do not
  create grouping folders just to dodge the rule; they should name a real cluster.
- **Good for.** Small apps, short-lived MVPs, inexperienced teams.
- **Costs.** Stops scaling fast. A feature is scattered over six folders, so its edges are hard to
  see and hard to cut out. Without written arrows the flow becomes two-way.

## Per feature

```text
src/
  features/
    dashboard/          (inside: components, services, utils … or Atomic Design, not both)
    settings/
      details/
      list/
  shared/
    components/
    utils/
```

A feature is a finished unit, usually a page or module, with its own views, state and helpers.

- **Flow.** `features` → `features/<group>/shared` → `shared/components` → `shared/utils`.
- **Rules.** Features never import each other. Code two or three features need goes into a group:
  `features/<group>/{feature-a, feature-b, shared}`, not into the global `shared`. The global
  `shared` stays small; it ships with every page.
- **Placement.** Start next to the use. Move to `shared` only when many features use it.
- **Good for.** Most product apps; often a new folder per new page is enough.
- **Costs.** Responsibilities blur, especially in `shared`. When two features read the same backend
  data it tends to turn into a global store, which hurts code splitting and brings back unclear
  edges. Regrouping sometimes means moving whole trees.

## Per domain (Nx style)

```text
libs/ or src/
  <domain>/
    feature-<name>/     a page or flow: composes ui, adds routing
    ui-<name>/          a piece of a page that may fetch and hold its own state
    data-access-<name>/ backend calls, mapping to internal types, caching, domain logic
    utils-<name>/       helpers, auth, navigation pieces
  shared/
    ui/
    utils/
```

- **Flow.** `feature` → `ui` → `data-access` → `utils`, inside one domain.
- **Rules.** Domains do not import each other at any layer. `shared` mirrors the layer types; a
  domain's `ui` does not import `shared/ui` unless an extra layer is defined for it.
- **feature vs ui.** A feature is the whole page (or a few pages) and mostly composes; a ui element
  is one part of it. Small parts can stay inside the feature; a part with a lot of code becomes its
  own `ui-` library.
- **Domains.** The hard part. Event Storming helps when the people and an experienced facilitator
  are available; often they are not. Start with the best current guess and move code when the
  domains become clear: with explicit dependencies and good tooling a move is one command.
- **Good for.** Long-lived products, several teams, a later move to micro-frontends or separate
  apps.
- **Costs.** Hard to do well, needs correct domains, unfamiliar to newcomers. Shared reads (two
  domains needing the same list) force a choice: duplicate the call, move it to a common domain, or
  aggregate in a BFF.

## Feature-Sliced Design (v2.1)

```text
src/
  app/        initialisation, providers, routing
  pages/      one slice per screen; owns its logic first
  features/   user actions reused on several pages
  entities/   business objects several features read
  shared/     no business rules: ui kit, api client, lib, config
```

Inside a slice: segments (`ui`, `model`, `api`, `lib`, `config`) and an `index.ts`.

- **Flow.** Strictly down the layers. Slices on the same layer never import each other (the `@x`
  notation is the only narrow exception).
- **Extraction rule.** Code starts in `pages` and moves down only when a second user exists now, it
  changes for its own reason, and it has one job. `widgets` is discouraged.
- **Relation to the splits above.** FSD is per feature with explicit layers: `pages` ≈ features,
  `entities`/`features` ≈ the domain's `data-access` and reusable `ui`, `shared` ≈ shared. Slice
  segments ≈ the Nx prefixes inside one slice.

## Atomic Design (for shared UI)

```text
components/
  atoms/ molecules/ organisms/ templates/
```

A layered architecture for UI parts: higher levels import lower ones, never the reverse.

- **With FSD.** Atomic Design organises `shared/ui` only. Feature code follows FSD; the UI kit
  follows Atomic Design. Do not reshape shared UI into slices, and do not put business logic in
  atoms.
- **With per feature.** Inside one feature you may use Atomic Design or per type, but not both in
  the same feature.

## Making your own layered structure

Two things are enough: the list of layers, and the direction. Name the layers, decide whether higher
imports lower or the reverse, write it in the map, and never flip it later.

## Moving between structures

- From per type to per feature, or per feature to per domain (or FSD): try it on a side project or
  one narrow module first, agree it with the business, and say it will be slow at the start.
- In a live repo, use `install` and the scout rule: one area per task, markers for the rest.
