# Backend Lens

Use for schema, API endpoints, server functions, auth, background jobs, data migrations, and other work with little or no UI.

## Required Context

Before planning backend work:

1. Read the backend guidelines this project ships, if any — generated AI guidelines, contributor docs, or the project layer's Skill Routing entries. If the project layer maps a backend skill, that skill owns implementation details; this plan documents the decisions it will execute.
2. Read the repo's existing schema/API definitions for the entities this feature touches. Plan against what exists, not what the README implies.

## Backend Brief

Define:

- **Entities**: tables/collections/models involved, new fields, and indexes or access paths implied by expected queries.
- **Entry points**: endpoints, functions, or handlers to add or change.
- **Identity and roles**: what authentication is required, which roles may call what.
- **Ownership rules**: which records each actor may read or write, stated per entry point.
- **Validation and error shape**: what input is rejected, and what errors callers see.
- **Realtime/async needs**: subscriptions, jobs, webhooks, retries — and expected fanout or contention if high-frequency.
- **Migration/backfill**: if existing data changes shape, the plan includes a migration step and its rollback story.

## Defaults

- Server-side authorization is mandatory for role- or ownership-sensitive data. The spec lists each rule; the tasks include verifying at least one allowed and one denied path.
- The server validates even when the client also validates.
- Indexes and access paths follow expected query patterns; the design notes say which queries drove them.
- Derived or high-frequency data (counters, stats, events) gets an explicit read/write amplification note.

## Decisions To Ask First

Ask (or record as open questions) rather than assume, whenever relevant:

- Role model shape when it is not settled (separate role vs. permission flag).
- Snapshot vs. live reference when one record is "assigned" to another.
- Soft delete vs. hard delete, and retention expectations.
- Whether users may edit records after a submit/approve boundary.
- Multi-tenancy or visibility boundaries the product has not confirmed.

The project layer's Open Decisions registry extends this list and always wins.

## Verification (for design.md / tasks.md)

Codegen/typecheck/test commands from the project layer or discovery. For authorization-sensitive work, one allowed and one denied path per boundary, through tests or targeted review.
