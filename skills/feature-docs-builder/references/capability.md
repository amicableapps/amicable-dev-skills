# Capability Lens

Use for complete product capabilities that cross UI and data: creating, managing, assigning, tracking, or reviewing something. A capability is a verb phrase a user completes, not a screen.

## Capability Brief

Define:

- **Actor(s)**: who performs and who is affected.
- **Capability**: the verb phrase ("create an invoice", "assign a task", "review submissions").
- **Data owned**: records created, read, updated, deleted, or derived.
- **Permissions**: who can do what, and where that is enforced server-side. UI checks alone never satisfy the spec.
- **UI surfaces**: list, detail, create/edit, review, confirmation — only those the capability needs.
- **State lifecycle**: e.g. draft → saved → submitted → reviewed → archived. Name transitions and who triggers them.
- **Open decisions**: anything the project layer's registry or context files mark as unresolved.

## Vertical Slice Order (for tasks.md)

Default implementation order for a new full-stack capability — adjust to the stack, keep the shape:

1. Domain model and validation shape.
2. Persistence and API entry points, with server-side authorization.
3. Entry-point structure with loading/error/empty states.
4. Forms and interaction flow.
5. Reusable extraction only where reuse is already proven.
6. Docs updated to match what was decided during implementation.
7. Quality gates and manual verification.

For UI-only work, skip the backend steps only after confirming the data already exists or is intentionally mocked — state which in the spec.

## Architecture Placement

Follow the repo's existing pattern for where capabilities live (project layer, or find two existing features and copy their shape). Do not invent an architecture in a planning doc. If the repo has no clear convention, placement is an open question for the user, and the chosen answer becomes an ADR.

Keep first versions local to their entry point when ownership is unclear; plan extraction as a later task with an explicit trigger ("second consumer appears"), not as speculation.

## Data and Security

- Authorization rules live with the data access layer, not only in the UI. The spec names each rule per actor.
- Users see only what their role and ownership allow; write one acceptance criterion per boundary that matters.
- Validation is specified at both the input boundary and the server.

## Verification (for design.md / tasks.md)

Quality gates from the project layer or discovery, plus: the happy path, at least one empty/error state, and at least one permission-denied path when the capability is role-sensitive.
