# Definition Of Done

Check before the final response of any planning run. The deliverable is a plan, so done means the plan is implementable — not that anything was implemented.

## Spec

- Every user story has an independent test and GIVEN/WHEN/THEN acceptance criteria.
- Edge cases cover at least: empty, error, permission-denied (when role-sensitive), and one boundary condition.
- Out of scope is explicit; open questions are listed, not silently resolved.
- Nothing in the spec contradicts the project layer's standing policies or context files.

## Design

- Ownership/placement is stated in this repo's actual conventions, or raised as an open question — never invented.
- Data model, flow, and state behavior are concrete enough that two competent executors would build roughly the same thing.
- Authorization says where each rule is enforced server-side, for every role-sensitive path.
- Verification names real, discoverable quality-gate commands; if none were found, that is stated.

## Tasks

- Tasks use exact file paths and are grouped by independently testable story.
- Order respects dependencies: foundation before stories, files before commands that use them.
- Every story group ends with a verification task.

## Process

- Only docs that earn their keep were created; placement follows the project layer, or feature-adjacent/central rules with central packs marked temporary.
- Open decisions from the project registry were asked or recorded — never assumed.
- No feature code was written, no scaffolding, no dependency installs.
- The user was asked exactly once whether they want an implementation prompt; if yes, it exists beside the doc pack and stands alone.
- If no project layer exists, running `feature-docs-builder adapt` was suggested once.

## Final Report

State plainly: what was planned, where the docs live, what stays open, and anything above that is unmet with the reason.
