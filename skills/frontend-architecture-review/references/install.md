# Install

Make the review specific to the repo under review without editing the skill's base files. The output is one file, `references/project.md` beside this one in the skill as installed in that repo: the project layer the review loads in Step 1. `SKILL.md` and this file stay generic and update-safe; everything the repo needs lives in the layer.

Run when the skill first lands in a repo, when the stack, architecture or conventions move, or when a review reported the layer missing or stale. A re-run replaces the file: carry forward what is still true, drop what went stale, and say what changed.

Detection is read-only: change no file but the layer, switch no branch, install nothing, and run no command that writes outside caches.

## Step 1 — Detect

Recover everything the repo can answer before asking anything. Read the files themselves; a rule goes into the layer only when a file or the code shows it. Where the repo has nothing for a target (no router, no cache library, no test runner, no design file), record that under "Not in this repo" rather than guessing a substitute.

- **Stack**: framework and the version the lockfile resolves (a major version decides which idioms are current), language, router, data-fetching and cache library, styling, test runner, package manager. When two lockfiles disagree, the one CI installs from wins, and the disagreement goes to Step 2. One line each, only what changes a review.
- **Architecture**: the method the code follows (layers, slices, feature folders, atomic levels…) and the docs that define it. Name the real folders and the real import rules, including the lint rule that enforces them and what it cannot see. When no method is written down, describe what the folders show, mark it inferred, and confirm it in Step 2. When the docs keep a "not yet migrated" placement (a table, a folder, a list) and marker words (`*-TODO[...]`), the layer's "Done when" line is built from them; otherwise it is the list of folders.
- **Entry points**: the routes, or for an app without a router the app or embed entry (mount points, the props or attributes it is configured by, style scoping), and data-defined parts (field types from a CMS or JSON, and the component switch they feed). Runtime dependencies on another repo's bundle or API, with the ADR that records them.
- **Legacy**: code the conventions tolerate as it is (oversized files, calls that predate the API client) and the step that will move it, so a review notes it rather than flagging it.
- **Convention files**: `AGENTS.md`, `CLAUDE.md`, `DESIGN.md`, `PRODUCT.md`, the architecture docs, and per-folder notes (`AGENTS.md`, `temp-agents.md`, `_docs/`, ADRs). Note what each one authoritatively answers, whether it is tracked, which folders have notes and which lack them, the language of code comments and agent replies versus the UI copy, and docs that name code that does not exist or contradict it (they go to Step 2, not into the layer).
- **Work contracts and repo skills**: start files, specs or tickets (for example `.agents/starts/`), whether they are tracked or live only in the main checkout (the first entry of `git worktree list`), how one is matched to the work under review (a `Branch:` line, else its title), and which of their lines bind a review. Skills the convention files route a question to (for example where a file belongs) are named too.
- **Shared UI**: where shared components live, their levels, their prop rules (for example a ban on caller classes), the table or index that lists them, and the domain renderers that are allowed to know a domain shape.
- **Model and ports**: where business rules live, and any planned port or registration list the docs name as the target shape, with its marker id and status.
- **API**: the client module new calls must use, how the token travels, how statuses become sentences (the helper, and which statuses it already handles), legacy call sites that are tolerated, and the query-key home when there is a cache.
- **Security and privacy**: what browser storage holds today (every key the code writes) and where each is cleared; every `{@html}` call site and what it renders through; how configuration reaches the browser (an environment prefix, a bundler `define`, a gitignored config file) and that it holds no secret; role helpers; the privacy law and standards that apply (organisation instructions count). AI features: where the code calls a model or shows generated content, and how that output is marked and confirmed by a person; say plainly when an assistant-looking feature calls no model.
- **Design system and accessibility**: the design file's named rules and tokens (and theme or branding rules such as dark mode), the accessibility target, dialog and focus rules, the UI language and voice.
- **Gates**: the real commands for typecheck, lint, format and tests, from the package scripts and CI, scoped as a review should run them. For each: whether it covers component files (a plain `tsc` does not check `.svelte` or `.vue`), whether it writes files (a review skips those), whether it runs only in CI, its duration (a gate slower than about two minutes is scoped to touched folders in a review), and its baseline: errors and warnings measured on the checkout you are in, with its commit named, and rules downgraded to warnings. A review judges only what a diff adds. Note the known workarounds (for example line endings on Windows making the format check fail everywhere, or generated folders the linter scans). First check that dependencies are installed; when they are not, the layer still names the gates, and Step 4 says they were not run.
- **Base branch**: the branch recent merge requests were merged into (read the merge commits, `git log --merges`), present on the remote (`git branch -r`). It can differ from the branch CI deploys and from `origin/HEAD`; trust the merges, and ask in Step 2 when they disagree.

## Step 2 — Ask only what the repo cannot answer

One batched round, framed as "here is what I detected, correct me": standing policies a review must always enforce, rules the team follows that no file writes down, inferred rules from Step 1, and anything Step 1 found contradictory. Skip the round when nothing is open.

## Step 3 — Write the layer

Write `references/project.md` from the template. Rules:

- Write deltas: what this repo adds to or sharpens in `SKILL.md`. A general rule the repo follows as written stays out.
- Every rule names the real path, module, helper or marker, so a reviewer can check it without searching. A rule a doc holds is cited, in one line that says what to check (a named design rule gets its name and its test, not its prose); a rule no file writes down is written out.
- Keep the dimension headings and numbers of `SKILL.md` Step 3, so each rule sits beside the general one it sharpens. A heading with even one rule stays; a heading the repo adds nothing to goes.
- "Not in this repo" lists each general rule of `SKILL.md` that has nothing to apply to here, so a review skips it instead of flagging every file.
- The layer holds rules, not findings. A bug or gap met while detecting goes into the Step 4 report to the user.
- Date the facts that will move (a planned port, a migration under way, a baseline count).

```md
# Project layer: <repo name>

Written by `frontend-architecture-review install` on <YYYY-MM-DD>. Re-run it when the stack or conventions move.

## Stack

- <framework and version / language / data library / styling / test runner / package manager>

## Conventions to read

- Always: <files>
- When the diff changes <what>: <file>
- For every folder the diff touches: <its notes>
- Work contracts: <where, how one is matched, which lines bind the review>
- Done when: <how every changed file is placed>

## Base branch

`<branch>`

## Not in this repo

- <general rule>: <why it has nothing to apply to>

## Dimension rules

### 1. <architecture name>

- <rule with paths>

### 2. <shared UI name>

…

### 9. Tests and gates

- Gates: `<command>` (<scope>; baseline <count> on <date>), …
- <workarounds>
```

## Step 4 — Verify

- Every path, module and helper the layer names exists (Glob or Grep it). Every gate command runs, or the layer says why it was not run.
- Read the layer as a fresh session would, beside `SKILL.md`: every line changes what the review checks or where it looks. Delete the lines that do neither.
- Report to the user what was detected, what they decided, what the layer changes about future reviews, and any bug or gap met on the way.
