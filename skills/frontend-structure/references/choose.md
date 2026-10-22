# Choose

Pick a structure from architectural drivers, the facts a structural decision should rest on, and
record the decision. There is no single right split; there is a split that fits these drivers.

## The drivers

Ask only what the repo and the user's earlier messages cannot answer, in one round.

| Driver                     | Points towards                                                                                                         |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| How long will it live?     | Throw-away MVP → per type. Unknown future → per feature, find domains as they appear. Years of development → per domain or FSD |
| Team experience            | No one to lead it, mostly juniors → the simplest split. DDD or FSD experience in the team → per domain or FSD             |
| One platform or many?      | Web only → one app. Web and mobile that differ a lot → separate apps sharing a narrow core (domain logic)                 |
| Team size                  | One team → one app. Several teams on one product → domains, then monorepo or multirepo, maybe micro-frontends           |
| The client                 | Their organisation can decide it: separate deployments or separate teams on their side change the answer               |

Monorepo or multirepo, when several teams are involved:

- **Monorepo.** Everyone sees all code and can fix it; a fix lands everywhere; no versioning of the
  shared parts. But the amount of code needs firm ownership lines, releases are joint (feature flags
  help), and every app always gets the newest shared code.
- **Multirepo.** Independent releases, less code per person. But changes to shared code must be
  communicated, and a shared look drifts while teams update at different times.
- **Micro-frontends.** Only for a narrow set of needs: teams that must scale beyond one domain each,
  independent releases where one broken part must not stop another, or serving parts of the app to
  different audiences. A well-split per-domain app is the starting point.

## Recommend

State the recommendation, the two drivers that decided it, and the cost the team accepts. Offer the
next step down as the fallback. In a live repo, recommend the incremental route (`install` with
markers), not a rewrite; a team trying a more demanding split should try it on one module or a side
project first, agreed with the business.

## Record it: ADR

Write `docs/architecture/decisions/NNNN-<slug>.md` (next free number):

```markdown
# NNNN. <Structure> for the frontend

- Status: accepted
- Date: <YYYY-MM-DD>

## Context

<the drivers, as facts about this project and team>

## Decision

<the structure, the direction of dependencies, the layers>

## Consequences

<what gets easier, what gets harder, what the team commits to (rules, lint, scout rule)>

## Alternatives considered

<each with the reason it lost>
```

A later change of structure is a new ADR that supersedes this one, not an edit.
