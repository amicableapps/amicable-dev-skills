# Spec-Driven Feature Docs

The doc pack is executable context: requirements shape design, design shapes tasks, tasks verify implementation. Keep it small enough to read, adjacent to the code that will own it, and traceable to implementation.

## The Pack

- `spec.md` — what behavior must exist. Create for any non-trivial feature.
- `design.md` — how this repo will implement it. Create when architecture, data flow, state lifecycle, or UI structure is non-obvious.
- `tasks.md` — incremental implementation and verification checkpoints. Create when work spans multiple files, actors, or phases.
- `adr/*.md` — durable decisions with rejected alternatives. Create only when a decision is hard to infer later, costly to reverse, or shapes future features.

Create only the files that earn their keep. A simple feature may need only `spec.md`; a tiny change needs nothing. Do not create docs for every button.

## Location

Project layer placement rules win. Otherwise:

- **Ownership known**: put the pack beside the owning code, e.g. `<owning-module>/_docs/`. Never expose `_docs` through a module's public API.
- **Ownership not yet known** (feature planned before code exists): use a central planning folder, e.g. `docs/features/<feature-name>/`, and mark the pack as temporary at the top of `spec.md`. Move the essential docs beside the owning code once implementation starts.

## spec.md Template

```md
# <Feature Name> Spec

## Purpose

One short paragraph: user value and scope.

## Actors

- <actor>: <what this feature means for them>

## User Stories

### US1: <short title>

Priority: P1

As a <actor>, I want <capability>, so that <outcome>.

Independent test: <how to prove this story works alone>.

Acceptance:

- GIVEN <state>, WHEN <action>, THEN <result>.

## Requirements

- FR-001: The system must <specific behavior>.

## Edge Cases

- <boundary, error, permission, empty, or concurrent state>.

## Out Of Scope

- <explicit non-goal>.

## Open Questions

- [ ] <question needing a product/architecture decision>
```

## design.md Template

```md
# <Feature Name> Design

## Summary

Short technical approach in this repo's terms.

## Ownership

- Entry point (route/page/handler):
- Feature module:
- Domain model:
- Persistence/API:
- Shared code touched:

## Data Model

Entities, fields, validation, derived data, indexes/access paths.

## Flow

1. <main user/system step>

## Authorization

- <actor>: <what they may do, and where it is enforced server-side>

## States

- Loading / Empty / Error / Disabled / Success: <behavior for each that applies>

## Verification

- <real quality-gate commands from the project layer or discovery>
- <manual checks: happy path, one failure state, one denied path if role-sensitive>
```

## tasks.md Template

```md
# <Feature Name> Tasks

## Foundation

- [ ] T001 <blocking setup, schema, or dependency task>

## US1: <title>

- [ ] T010 <implementation task with exact file path>
- [ ] T011 <verification task>

## Polish

- [ ] T900 <accessibility, responsive, or edge-state task>
```

Task rules: exact file paths; group by independently testable user story; mark parallel-safe tasks `[P]` only when they touch different files with no dependency; keep tasks outcome-based.

## ADR Template

```md
# ADR NNNN: <Decision>

Date: YYYY-MM-DD

## Status

Accepted

## Context

What forced the decision.

## Decision

What we chose.

## Alternatives Considered

- <alternative>: rejected because <reason>.

## Consequences

- Positive: / Negative: / Follow-up:
```

## When To Ask First

Ask the user before a spec resolves anything the project layer's Open Decisions registry lists, anything context files mark as unsettled, or any ambiguous architecture placement. Otherwise recorded assumptions go under Open Questions, not silently into requirements.

## Maintenance

- Docs update in the same PR/turn as behavior changes.
- Completed tasks are marked complete with implementation notes; delete them only when they stop helping traceability.
- One short ADR beats rationale hidden in code comments.
- When docs and code disagree, that is implementation drift: reconcile before planning more on top of it.
