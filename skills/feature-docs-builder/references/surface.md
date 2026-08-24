# Surface Lens

Use for routes, pages, screens, dashboards, settings areas, onboarding flows, and other UI-heavy feature entry points. This lens defines what the plan must pin down; it does not style or implement anything.

## Surface Brief

Establish, from context files and the repo before asking:

- **Actor**: which persona uses this surface (project layer defines personas; otherwise derive or ask).
- **Job**: the one thing the user is trying to complete here.
- **Device posture**: where and how this is used — mobile on the move, dense desktop work, glanceable display. If the project layer assigns a posture per persona, use it.
- **Data states**: loading, populated, empty, error, unauthorized, disabled, success. The spec must say what each state shows.
- **Primary action**: the single action this surface makes easiest.
- **Secondary actions**: filters, edits, drill-downs, navigation, save/cancel, export.

## Planning Rules

- The working surface is the first screen. Do not plan a marketing-style landing page for an app feature unless the feature *is* a marketing page.
- Every metric or number shown needs a label, unit, timeframe, and comparison or context — put that in the spec, not in a reviewer's head.
- Entry via existing navigation: state where this surface hangs in the current information architecture, or raise it as an open question.
- Accessibility is a requirement line, not a polish task: labels, focus order, keyboard path for the primary action.
- Responsive behavior is part of the design notes whenever more than one device posture is plausible.

## Design Direction

- If the `impeccable` skill is available, use it at planning time to shape the visual/UX direction (its `shape` command fits here) and record the chosen direction in `design.md`. Implementation-time UI work is handed to it later — reference that handoff in `tasks.md`.
- If `impeccable` is not available, do not improvise a design system. Record design direction as open questions or as decisions the user confirms, and suggest `feature-docs-builder adapt` to register what owns design direction in this project.

## Implementation Shape (for design.md)

Plan the structure the executor should follow, using this repo's actual conventions (project layer or discovery):

- The route/page binds data, auth, and high-level states.
- Components start local to the surface; extraction into shared locations is planned only when reuse is already known, and flagged as a decision otherwise.
- List the states from the brief as concrete UI: skeleton/pending, empty with a next action, error with recovery, disabled and validation states for forms.

## Verification (for design.md / tasks.md)

Name the repo's real quality gates. For UI surfaces, include a manual browser check across the device postures the brief names.
