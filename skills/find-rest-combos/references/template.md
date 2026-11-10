# Combos document template

Plain words, short sentences, tables for lists of combos. Write for a product owner first and a
developer second. Keep the product's own names for its objects and screens.

```markdown
# Combos: <product>

<Follow-up to <earlier document>, if any> | Date: <YYYY-MM-DD>

**Source:** `<frontend repo>` @ `<commit>`, `<API repo or spec>` @ `<commit or version>`.

> **What is a combo?** A place where one screen needs something from another screen, and the user
> can get it **without leaving**. Either they create the missing thing on the spot, or one action
> does two things at once.
>
> 🔥 must do · 👍 strong · 🤔 needs more thought

---

## In 1 minute

1. <The pattern the user's example shows, and how far it reaches.>
2. <How many places have the same gap.>
3. <The single build pattern that fixes most of them.>
4. <The other kinds found, in one line each.>

---

## Since the last run

(Only on a re-run.)

| Earlier combo | Now (built, partly built, not built, unverified) | Evidence |
|---------------|--------------------------------------------------|----------|

## Your example: <screen> → <missing thing>

(Only when the user gave one.)

**Today** (`<file>`, `<component>`): <what the user has to do now, step by step>.

**Combo:** <what changes>. <What is prefilled.> <What happens after saving.>

**API:** `<METHOD> <path>` (<roles>); <prefilled fields it accepts>; <gap, if any>.

**Why it matters:** <one or two sentences>.
**Effort:** <Low | Medium | High>. **Score:** <🔥 | 👍 | 🤔>

---

## Combo 1: "Pick or create"

| # | Where | Picks | Today | Combo | API | Roles | Effort | Score |
|---|-------|-------|-------|-------|-----|-------|--------|-------|

## Combo 2: "What next?" after saving

| After saving | Suggestion | Uses | API | Roles | Effort | Score |
|--------------|------------|------|-----|-------|--------|-------|

## Combo 3: one action, two effects

| Action | And at the same time | API | Roles | Effort | Score |
|--------|----------------------|-----|-------|--------|-------|

## Combo 4: dead end → fix in place

| Where | Blocked by | Fix in place | API | Roles | Effort | Score |
|-------|------------|--------------|-----|-------|--------|-------|

## Combo 5: act where you see it

| Item | Shown on | Action | API | Roles | Effort | Score |
|------|----------|--------|-----|-------|--------|-------|

(Drop a section that found nothing.)

---

## API gaps

| Combo | Needs | Today the API |
|-------|-------|---------------|

## Recommended order

### Do now
1. <combo>: <why first>

### Do next
1. …

### Later
1. …

---

## How to build it (for developers)

1. **The existing editor in a dialog, not a new form.** <Which editors exist, and what each needs
   for a dialog mode.>
2. **After saving: refresh the query, select the new item, keep the draft.**
3. **Only for those who are allowed.** <Roles per combo.> The API must refuse it as well (OWASP
   A01); hiding a button is not protection.

Details:
- <Unique keys, archived items, conflicts, audit, accessibility (focus returns to the picker with
  the new item selected).>

---

## Questions

- **Assumption:** <what was assumed, and what changes if it is wrong>.
- **Q:** <what only the team can answer>.
```
