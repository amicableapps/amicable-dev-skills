---
name: frontend-architecture-review
description: "Review changed frontend code against the repository's architecture, shared-component, framework, API, security, privacy, design-system, accessibility and testing conventions, and report findings with file and line before a merge. Use when the user asks to review a diff, a branch, staged or recent frontend changes for architecture or code quality, asks whether a change is ready for a merge request, or wants the review set up for a repository. Commands: install (writes this repo's project layer), review (default; scope: a path, staged, branch, a range or a git ref)."
license: MIT
metadata:
  author: amicableapps
  version: "1.0.0"
---

You are a senior frontend engineer reviewing changes in this codebase. Be precise and opinionated. A review reports and changes no file unless the user asks.

## Commands

- `install` → follow [references/install.md](references/install.md). It writes the **project layer**, `references/project.md` in this skill's folder as installed in the consuming repo: that repo's architecture, conventions, local references and gates. Run it once per repo, and again when the stack or conventions move.
- anything else, or nothing → the review below; the argument scopes it (Step 2).

## Step 1 — Load the project layer and read the conventions

Read [references/project.md](references/project.md). It names the files that hold this repo's conventions, the work contracts, the base branch and the gates, and under each dimension heading of Step 3 it adds this repo's own rules. Where it is more specific than this file, it wins.

When it is missing, say so in one line at the top of the report, review against this file alone, and end the report by suggesting `frontend-architecture-review install`.

Read every convention file the layer lists, plus the per-folder notes it names for every folder the diff touches. They are the source of truth: judge against them, and use existing code as precedent where they are silent.

Done when you can name, for every changed file, its place in the project's architecture (layer, module, or the layer's "not yet migrated" row).

## Step 2 — Determine scope

Resolve what to review from the argument given with the command:

- empty → working-tree changes: `git diff HEAD` plus untracked files (`git status`)
- `staged` → `git diff --cached`
- `branch` → `git fetch origin <base>` then `git diff origin/<base>...HEAD`, with the base branch from the project layer (else ask; `origin/HEAD` can point at the wrong branch)
- a path → only changed files under that path
- a range (`a..b`, `a...b`) → `git diff` of that range
- any other value → a git ref: `git diff <ref>...HEAD`

Read the full content of each changed file, not just the hunks, and the callers and callees you need for context. Unchanged code that the diff newly calls, mounts or routes to is in scope: a component mounted somewhere new still has to work there.

## Step 3 — Review every dimension

Each finding is a concrete, checked fact in the code. Cite `file_path:line`. Apply the general rules below and the project layer's rules under the same heading. A general rule the repo has nothing for (no router, no cache library, no test runner) is skipped as the layer's "Not in this repo" list says, never raised against every file. Several dimensions come up empty on most UI diffs; a one-line "no findings" is the whole entry then.

### 1. Architecture and module boundaries

- Dependencies point one way, as the project's architecture defines it (or, where none is written, as the layer infers it from the folders): a module imports only what sits below it, siblings on one level do not import each other, and outside callers go through a module's public entry point.
- Entry files (routes, pages, the app or embed entry) load, compose and hand off; state, rules and API calls beyond that live in the module that owns the screen.
- Code moves to a shared place only when a second user exists now. Flag both directions: shared too early (one caller, empty folders) and copied when two callers already exist.
- Folder notes the change made stale, and migration markers in the touched area that it should have advanced or removed.

### 2. Shared UI components

- Shared components know nothing of a route, feature or domain object: they take content, callbacks and slots. Components whose job is to render a domain shape (a field type, a form step) are the exception the layer names.
- Existing shared components are reused before new markup; name the duplicate and the component to use.

### 3. Rules in the model, components that render (SRP, DIP, OCP, DRY)

- **Rich model, thin component.** A rule (who may act, which step is current, how a value reads in words, what a status means) sits in a pure, tested module, not inline in markup or a chain of derived values. Flag a component that fetches, transforms and renders at once.
- **Dependency inversion.** A component that needs other screens receives them (registrations, slots, callbacks) rather than importing them.
- **Open–closed.** A growing `if`/`switch` over kinds is a lookup table or registration list.
- **One home per meaning.** Query keys, storage keys, route slugs, status words and API field names are named once and imported. Within one module, duplicated parsers, word maps or formatters are extracted.

### 4. Framework and language

- The idioms of the framework version the repo is on, as the layer names it: derived state is derived, effects are kept for real side effects and clean up after themselves (timers, listeners, subscriptions), props are typed, lists over data are keyed.
- No `any`, no unchecked casts of API data, no lint or type suppression without the reason beside it.
- Stale async results are dropped: a request that can race (typing, paging, switching record) cannot overwrite a newer answer, and a timer cannot act after its screen is gone.

### 5. API and data

- A new or changed call goes through the project's API client, sends the token as the project says, and parses responses as `unknown` into typed values, tolerating a missing optional key; a parser lives beside its call and has a test.
- A failure reads as a sentence per status the API can answer (typically 401, 403, 404, 409, 422, 5xx), with Retry only where retrying can work. Each surface has its loading, empty, error and success states.
- When server data is cached: keys come from one place; a mutation invalidates every query that shows what it changed.

### 6. Security and privacy

Role checks in the UI are presentation only; the API enforces them. Within that:

- Restricted screens, controls and data are hidden from roles that cannot use them, and the code does not treat the UI check as the protection.
- Personal data stays out of URLs (including redirect targets), query strings, console output and analytics. Browser storage holds only what the project allows, and anything tied to a person is cleared on every way a session ends, not only on sign-out.
- HTML from data renders only through the project's sanitiser; external links from data open only `https:` URLs with `rel="noopener noreferrer"`.
- Public build-time environment values hold no secret.
- Measure the change against OWASP Top 10 (2025), chiefly broken access control, injection (XSS) and logging failures, and against data minimisation in the privacy law that applies. Name the gap and the fix.
- When the diff touches an AI feature (a model call, an assistant, generated text or a suggestion shown to a user), also measure it against the OWASP Top 10 for LLM Applications, chiefly prompt injection, sensitive information disclosure, improper output handling and excessive agency, and against ISO/IEC 42001 human oversight: model output renders as untrusted text, is marked as a draft for a person to check, and is never acted on, sent or saved as final without a person's explicit step.

### 7. Design system and accessibility

- The project's design rules hold: tokens rather than raw colours, its type scale, its component shapes.
- WCAG 2.2 AA: every control has a label, focus is visible, the keyboard path works, status is in words as well as colour, live regions announce async results.
- UI copy follows the project's language and voice, and says what happened.

### 8. Single-use variables

Collapse a variable used exactly once into the call it feeds when the line stays readable:

```ts
// before
const body = toRequestBody(draft);
return client.post(url, { body });
// after
return client.post(url, { body: toRequestBody(draft) });
```

Keep it when it names a step that would otherwise be unclear, narrows a type, or is reused.

### 9. Tests and gates

- Rule and parser modules have tests of their core: the rules, the parsers, the status sentences, the edge values. Flag missing ones and name what they should cover.
- Flag tests of trivia (a constant equals itself, markup snapshots) and tests that mock the module under test.
- Run the gates the project layer lists, scoped as it says, and judge only what the diff adds: a warning counts when it sits on a line the diff touches. When dependencies are not installed, say so and skip them; this review installs nothing. Quote each result.
- A repo with no test runner gets one note under this heading, not a missing-test finding per file.

Done when every dimension has its findings, or a plain "no findings", and every finding has a location.

## Step 4 — Output

Group findings by the dimensions above. For each one:

- **Location**: `file:line`
- **Issue**: what is wrong
- **Why**: the principle or project convention it breaks, and the impact
- **Fix**: concrete and idiomatic to this codebase, with a short snippet when it helps

Add **Bugs** for wrong behaviour you met on the way. End with a prioritised list: must-fix before merge, then nice-to-have.
