# Levels and placement

Use this to answer `place <thing>`: which level, which folder, which props.

## Deciding the level

Ask in order; the first yes decides.

1. Is it a raw value (a colour, a spacing step, a duration)? → **quark**, in the token file. Never a
   second scale beside an existing one.
2. Is it one element with its own states, meaningful only as a whole (a button, an input, a badge,
   a surface)? → **atom**.
3. Is it a few atoms that do one job together, with no section of its own (a labelled field, a
   search box, a notice with a retry control)? → **molecule**.
4. Is it a section that stands on its own and could move between screens (a table with its header,
   a dialog shell, a navigation bar)? → **organism**.
5. Is it the arrangement of a page, with slots and no real content? → **template**.
6. Does it know a route, a domain type, a store or an API? → it is **not shared UI**. It stays with
   the feature or page that owns it, and composes kit components.

When it is between two levels, pick the lower one and keep the API small; promote it later when a
real second shape appears.

## Where it lives

- The kit's folders (`components/{atoms,molecules,organisms,templates}`, or the repo's own names in
  its Atomic Design section). Tokens in the repo's token file.
- A component that knows the product (a case, a customer, a payment) stays in the feature or
  integration layer, even when it looks generic. The map lists such components with the level they
  would have if they moved.
- One component per file; the file name is the component name.

## Props

- Content: text, children, snippets or slots.
- Behaviour: callbacks (`onclick`, `onchange`), `disabled`, `loading`.
- Appearance: typed variants only (see [variants.md](variants.md)).
- Accessibility: the label or `aria-*` the element needs, required when there is no visible label.
- Nothing domain-shaped: pass `label: string`, not `customer: Customer`.

## Composition rules

- Imports point down only. An atom never imports another atom's internals; a molecule never imports
  an organism.
- Nothing in the kit imports routes, features, domain types, stores, API clients or i18n bundles
  with product copy. Copy comes from the caller.
- No business logic in the kit: no fetching, no permissions, no status mapping. A feature wrapper
  maps a domain status onto a kit `tone` (an order status wrapper renders a `Badge` with `tone="warning"`).

## Pitfalls

- **Over-atomising.** A molecule that wraps one atom and adds nothing, or an atom per Tailwind
  class. Merge it back.
- **Under-atomising.** The same markup copied in three screens. Extract it when the second copy
  exists and the copies change for the same reason.
- **Look-alikes.** Two components that look similar but differ in motion, element or spacing on
  purpose. Keep them apart and say why in the map; do not normalise one onto the other.
- **Generic in name only.** A "shared" component with a `case` prop or a route import. It is a
  feature component; move it back or invert the dependency.
