---
name: feature-docs-builder
description: Use when the user wants to plan a feature before implementation. Triggers on requests for feature specs, design notes, task plans, ADRs, feature breakdowns, planning artifacts, durable product/architecture context, implementation contracts, or AI-ready handoff prompts ("plan this feature", "write a spec for", "break this down", "prepare docs for", "make an implementation prompt"). Not for implementing features end-to-end; it plans, then optionally produces one implementation prompt for a later executor.
license: MIT
metadata:
  author: amicableapps
  version: "1.0.0"
---

# Feature Docs Builder

Turn a feature idea into a small, durable documentation pack that a human or AI agent can implement later. The output is documents: a spec, design notes, a task plan, ADRs when a decision deserves one, and optionally a single implementation prompt.

## Hard Boundaries

- **This skill plans; it never implements.** Do not write feature code, scaffold apps, install dependencies, or run dev servers as part of this skill.
- **One-shot app generation is out of scope.** The deliverable is a plan another session or agent executes.
- **The only code-shaped output is the implementation prompt**, and only when the user says yes to it. It is still a document.
- Create docs only when they preserve decisions, reduce implementation ambiguity, or help future AI/human work. A trivial change needs no doc pack.

## Commands

| Command | Description | Reference |
|---|---|---|
| `plan [feature]` (default) | Plan a feature into a doc pack | Workflow below |
| `prompt [feature]` | Build an implementation prompt from an existing plan | [references/implementation-prompt.md](references/implementation-prompt.md) |
| `adapt` | Tune this skill to the current project | [references/adapt.md](references/adapt.md) |

Routing: no argument → ask what feature to plan. First word matches a command → load its reference; the rest is the target. Anything else → treat the full argument as the feature to plan.

## Preflight

Before any planning decision:

1. **Project layer.** If `references/project.md` exists in this skill (created by `adapt`), load it and obey its overrides: stack, personas, doc placement, quality gates, skill routing, standing policies. It wins over the generic defaults below.
2. **Project context files.** Read whichever exist at the repo root (or where the project layer points): `PRODUCT.md`, `DESIGN.md`, `AGENTS.md` / `CLAUDE.md`, `README`. Do not invent product facts these files could answer.
3. **Codebase shape.** If `graphify-out/GRAPH_REPORT.md` exists, read it before any broad architecture or multi-file investigation. Otherwise explore only as much of the repo as the feature touches: where similar features live, naming conventions, existing quality gates (typecheck, tests, lint).
4. If no project layer exists, proceed with generic defaults and, once the plan is delivered, suggest running `feature-docs-builder adapt` once so future runs know this project.

## Companion Skills

Exactly two companion skills may be referenced by this base skill, both conditionally:

- **impeccable** — when the feature is UI-heavy, use it at planning time to shape design direction and record the outcome in `design.md`. Implementation-time UI work belongs to it, not to this skill.
- **graphify** — when the codebase is large or unfamiliar and no graph report exists, suggest running it before deep architecture planning.

If either is not installed, do not emulate it. Note the gap and propose `feature-docs-builder adapt`, which records what this project uses instead. Any other skill routing (backend frameworks, architecture conventions, design systems) belongs in the project layer, never here.

## Workflow

1. **Classify** the request and load the matching lens:
   - `surface`: a route, page, screen, dashboard, settings area, onboarding flow, or other UI entry point → [references/surface.md](references/surface.md)
   - `capability`: a full product capability crossing UI and data (create/manage/assign/track something) → [references/capability.md](references/capability.md)
   - `backend`: schema, API, server functions, auth, migrations, or data work with little or no UI → [references/backend.md](references/backend.md)
   - `hardening`: improving an existing feature — edge cases, states, coupling, production readiness. Use the lens matching what the feature originally was, and plan against current behavior: document what exists before proposing changes.
2. **Draft the feature brief** the lens defines. Fill it from repo and context files first; only what genuinely cannot be recovered becomes a question.
3. **Surface open decisions early.** Batch product- or architecture-significant questions into one round. Never resolve a decision the project layer or context files list as open; ask, or record it as an open question in the spec.
4. **Write the doc pack** per [references/spec-driven-docs.md](references/spec-driven-docs.md). Only the files that earn their keep. Placement: feature-adjacent when code ownership is known, central planning folder marked temporary when it is not (details in the reference; project layer overrides both).
5. **Report the plan** concisely: what was decided, what stays open, where the docs live.
6. **Ask exactly once:** *"Do you want an implementation prompt too?"* If yes, load [references/implementation-prompt.md](references/implementation-prompt.md) and produce it. If no, stop.
7. **Before finishing**, check [references/definition-of-done.md](references/definition-of-done.md) and report anything unmet.

## Defaults When No Project Layer Exists

- Personas: derive actors from context files or the feature request itself; if roles are unclear and role-sensitive, ask.
- Security posture: any multi-role or user-owned data feature must state where authorization is enforced server-side. UI checks alone never satisfy a spec.
- States: every planned surface names loading, empty, error, and success behavior.
- Verification: name the project's real quality-gate commands in `design.md`; if none are discoverable, say so rather than inventing them.
