# Implementation Prompt

Produce one self-contained prompt that lets a later executor (a fresh AI session, another agent, or a human) implement the planned feature without this conversation. Offer it exactly once, after the doc pack is delivered. It is a document; producing it is not a license to implement.

Save it beside the doc pack as `implementation-prompt.md`.

## Anatomy

The pattern that works (distilled from bolt.new's artifact protocol and Convex Chef's layered prompts):

```text
identity + environment constraints + project context + fixed decisions + plan + output protocol + verification + forbidden states
```

1. **Identity**: one sentence — what the executor is building and for whom.
2. **Environment constraints**: stack, versions, package manager, repo layout, and the real quality-gate commands. From the project layer or discovery; never invented.
3. **Project context**: embed or link the doc pack (`spec.md`, `design.md`, `tasks.md`, relevant ADRs). Embed when the executor may not share the filesystem; link when it does.
4. **Fixed decisions**: everything already decided, stated as fixed — framework, placement, naming, auth model. Give the executor "the right decisions to make," not all decisions: list explicitly where creative freedom remains (typically UI detail) and where it does not.
5. **Plan**: the ordered steps from `tasks.md`, dependency-ordered — dependencies before files that import them, files before commands that run them.
6. **Output protocol**: how the executor should work (see below).
7. **Verification**: which commands must pass and which manual checks close the loop. Instruct the executor to treat typecheck/test failures as feedback and fix before proceeding.
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

## Rules For Any Protocol

- Think holistically before writing the prompt: every file the feature touches, every dependency, every ordering constraint. An executor cannot recover context you dropped.
- Unresolved open questions from the spec become explicit decision points in the prompt — the executor must ask, never guess.
- The prompt must stand alone: a reader with only this file and the repo can implement the feature.
- End the prompt with the doc pack's verification section and an instruction to update the docs if implementation diverges from the plan.
