# Principles: flow, boundaries, rules

Three ideas make any folder structure hold. Each one alone is not enough.

## 1. Dependency flow

As soon as one file imports another there is a flow of dependencies, whether anyone defined it or
not. Defining it means saying which way imports may point.

- **Direction.** Pick one direction and keep it: higher layers import lower ones, never the other
  way. In a layered stack of A over B over C, A may import B and C, B may import C, C imports none
  of them. The opposite convention (lower imports higher) also works; what breaks a codebase is
  having both.
- **Dimensions.** A flow can run along more than one axis. The usual two are vertical (layers:
  `app` → `shared`) and horizontal (elements on the same level: one button importing another).
  Think of each axis as one dimension of an array. Every extra dimension makes the structure harder
  to hold in your head; keep as few as the product needs, usually one or two.
- **One-way arrows.** A two-way arrow between two elements is where cycles come from. If two things
  must know each other, one of them is in the wrong place, or the shared part belongs in a third
  element below both.

## 2. Boundaries

A boundary says how far an element's responsibility reaches and what of it others may see.

- **Public API.** A folder that others use exposes one entry, usually `index.ts`, exporting only
  what outsiders need. Styles, local types, helpers and sub-components stay private. Importing a
  file inside another folder (`thing/internal/helper`) crosses its boundary.
- **Base and variants.** When variants share a base (a base button and primary, secondary, icon
  buttons), the base sits in its own folder below the variants. Outsiders get the variants; whether
  they may also use the base is a decision to write down.
- **Groups.** Unrelated families (buttons and links) sit in separate groups so neither can reach
  into the other.

Boundaries set visibility. They do not, by themselves, stop one element from importing another
through its public API. That is what rules are for.

## 3. Rules

Rules are the constraints on who may import whom. A practical minimum, which together make the
structure hold:

1. **No sideways imports.** Elements on the same level do not import each other.
2. **Down only.** A higher-order element imports only lower-order ones (a primary button may import
   the base button; the base button never imports the primary).
3. **Groups are closed.** An element of one group does not import an element of another group,
   even a lower one (an icon button does not import the base link).
4. **Grouping folders open a door.** Inside a grouping folder (a folder directly under a top-level
   folder), its members may share code. Top-level folders themselves stay closed to each other.
5. **Shared code needs a home, not a global.** When two features need the same code, create a group
   for them with its own `shared` inside, rather than pushing the code into the global `shared`.

Write the rules for the actual repo (`rules` command) and enforce them with a tool
([enforcement.md](enforcement.md)). Rules nobody checks drift within months.

## 4. Cycles

A cycle (A imports B imports C imports A) has no root: no element can be called the base. Three
files are easy to untangle; twenty are not. Cycles come from two-way flow. Prevention is the
one-direction rule; a cycle found in the wild is fixed by moving the shared part below the members
of the cycle, never by adding another import.

## 5. Architecture is a set of hard-to-change decisions

The folder structure is architecture because changing it later is expensive. Decide it
deliberately, write the reason down (ADR), and change it only when a real need appears, with the
cost stated. A good structure keeps the time to add a feature flat over the life of the project,
makes it obvious where a new file goes, shortens onboarding to "here are the rules", and keeps code
that does not belong out of the app.
