# Implementation Prompt

Produce one self-contained prompt that lets a later executor (a fresh AI session, another agent, or a human) implement the planned feature without this conversation. Offer it exactly once, after the doc pack is delivered. It is a document; producing it is not a license to implement.

Save it beside the doc pack as `implementation-prompt.md`.

## Anatomy

The pattern that works (distilled from bolt.new's artifact protocol and Convex Chef's layered prompts):

```text
identity + environment constraints + project context + fixed decisions + plan + output protocol + verification + forbidden states
```

1. **Identity**: one sentence — what the executor is building and for whom.
2. **Environment constraints**: stack, versions, package manager, repo layout, and the real quality-gate commands. From the project layer or discovery; never invented. Include runtime capabilities and the Integration Contract from `design.md` when present, including setup prerequisites and version-specific evidence.
3. **Project context**: embed or link the doc pack (`spec.md`, `design.md`, `tasks.md`, relevant ADRs). Embed when the executor may not share the filesystem; link when it does.
4. **Fixed decisions**: everything already decided, stated as fixed — framework, placement, naming, auth model. Give the executor "the right decisions to make," not all decisions: list explicitly where creative freedom remains (typically UI detail) and where it does not.
5. **Plan**: the ordered steps from `tasks.md`, dependency-ordered — dependencies before files that import them, files before commands that run them.
6. **Output protocol**: how the executor should work (see below).
7. **Verification**: which commands must pass and which observable story outcomes close the loop. Separate static checks from runtime or integration checks and name what evidence the executor should report. Apply the verification and repair rules below.
8. **Forbidden states**: files or areas the executor must not touch (auth config, generated files, unrelated features), and the standing policies from the project layer.

Add one or two short examples of this repo's idioms (an existing similar component, endpoint, or test) when the pattern is not obvious from the plan — correct examples improve executor output more than extra rules.

## Output Protocols

**Default — native-tools executor** (Claude Code, Codex, or similar with file/shell tools): a markdown contract. The executor uses its own tools; the prompt gives order, boundaries, and verification. Do not wrap output in a DSL.

**Artifact DSL — generator pipelines** (bolt/Chef-style streamed generation where output is parsed and executed): use the XML-like contract:

```xml
<featureArtifact id="short-kebab-id" title="Human title">
  <plan>
    <step>One short implementation step.</step>
  </plan>
  <action type="file" path="src/example.tsx">
    Full file contents.
  </action>
  <action type="shell">
    npm run typecheck
  </action>
  <action type="verify">
    What must be checked after execution.
  </action>
  <action type="decision">
    Unresolved decision that blocks safe implementation.
  </action>
</featureArtifact>
```

Action types: `file` (complete contents — never "rest unchanged"), `edit` (targeted patch description), `shell`, `start` (dev server), `verify`, `decision`.

DSL rules: one artifact per coherent feature; stable kebab-case ids; explicit relative paths; dependency/package actions before files that import them; prefer `decision` over guessing on open questions; keep payloads deterministic. Prefer JSON over XML tags only when output is fully machine-consumed, unstreamed, and strictly validated.

## Executor Preflight And Repair

Include these instructions in the handoff, scaled to the feature:

- Before editing, compare the plan with the current owning files, relevant manifests/lockfile, runtime, and available quality gates. Reconcile incidental drift in the docs; raise changes that invalidate a fixed decision or require a new product/architecture choice before dependent implementation. Preserve unrelated working changes.
- If an API is unfamiliar or implicated in a failure, inspect current repo usage, version-matched declarations/source, or authoritative documentation before proposing a fix. Preserve explicitly required libraries and services; a substitute requires an explicit decision.
- Verify a coherent change with the relevant checks, and verify the final state before reporting completion. A result from an earlier code or configuration state is not evidence for the changed state.
- When a failure repeats, record its diagnostic and the failed approach, gather new relevant evidence, and change the hypothesis before retrying. If the same failure and state recur with no new evidence or viable repair, stop that repair loop and report the blocker and pending checks. Do not weaken acceptance criteria or remove required behavior to make a check pass.
- Report changed files, checks actually run and their outcomes, and anything unverified. Mark completion only when applicable acceptance checks pass; distinguish a verification environment failure from an implementation failure.

## Rules For Any Protocol

- Think holistically before writing the prompt: every file the feature touches, every dependency, every ordering constraint. An executor cannot recover context you dropped.
- Unresolved open questions from the spec become explicit decision points in the prompt — the executor must ask, never guess.
- The prompt must stand alone: a reader with only this file and the repo can implement the feature.
- End the prompt with the doc pack's verification section and an instruction to update the docs if implementation diverges from the plan.

## Start Contract Checks

Use this when Preflight found an open start contract. The executor reads this prompt, not the contract, so the checks travel inside it.

In the prompt's verification, copy every Ready when line verbatim with its Check under it, in the contract's shape:

```markdown
- <Ready when line>
  - Check: agent `<command>` | human
```

A line with no Check is copied as `Check: human`. Copy the contract's Do not touch into the forbidden states. Name the contract's Branch (its Base when Branch is `none`) as the branch to work on, and tell the executor to commit only this start's work there.

Write these instructions into the prompt:

- Run every `Check: agent` command once, from the repo root, after the change exists and before reporting done. Paste each command's pass or fail line into the report. Done means every agent check has a passing line.
- A `Check: agent` that names a test file not yet in the repo is test-first: write that test first, run it and paste its failing line, then make the change and paste its passing line. In the plan, the task that writes the test comes before the task that makes it pass.
- List each `Check: human` line in the report as left for the user, unchecked.
- When the work stops or is ready for review, write the report to `.agents/starts/<slug>.report.md` in the repo's main checkout, next to the contract, replacing any older one. Use the fields of a-new-start's Report, and spell them out in the prompt, since the executor may not have that skill: Written (date, tool), Branch @ sha and Base, Changed (diff stat), Agent checks (command: pass/fail line, or `not run`), Human checks left, Do not touch (untouched, or the paths touched), Risks, Questions. Paste no chat summary in place of this file.

When no line has `Check: agent`, say so at the top of the prompt: an unattended run cannot implement this start.
