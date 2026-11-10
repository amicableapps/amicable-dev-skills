# Combo patterns

How to find each kind of combo in a frontend, how to check it against the REST API, and the rules a
team follows when it builds one. The examples are signals to search for; read every hit before
calling it a combo.

## Pick or create

**Signals in the frontend**

- Pickers, selects, comboboxes and autocompletes whose options come from an API list
  (`*Picker`, `*Select`, `<select>`, a query that lists a resource and feeds options).
- Empty or "missing" states in those pickers: "No … yet", "Add them in …", "then come back",
  a link to another admin page.
- Fixed shortcuts that create one hard-coded item ("Create the status fields") where users need
  others like it.
- Values typed by hand that the API could list (ids, keys, template ids), when a list endpoint
  exists.

**Check against the API**

- A create operation exists for the picked resource, and the fields a picker could prefill (type,
  parent, defaults) are accepted by it.
- Who may create it. A role that may pick but not create sees "ask an admin", not the button.
- Unique keys: archived or soft-deleted items may still hold a key; the dialog validates the way
  the API does.
- When the thing lives in another system (an email provider, a payments dashboard), there is no
  create here: the empty state says where to create it, not "come back".

**Build rules**

1. The existing editor in a dialog, not a new form. An editor that navigates after saving needs a
   dialog mode that does not.
2. After saving: refresh the list query, select the new item, keep the parent form's draft.
3. Only for roles the API allows; the API refuses the rest.
4. Accessibility: the dialog traps focus and returns it to the picker with the new item selected.

## What next?

**Signals in the frontend**

- Success states after a save: a toast, a redirect to a list, "Saved".
- Recipes, templates or presets that already exist elsewhere (workflow recipes, task templates)
  and take the just-saved item as an input.
- Help text that tells the user what to do next on another screen.

**Check against the API**

- The next step's create operation accepts the saved item's id or key.
- The suggestion is filled from what the API returned, not from the form's local state.

**Build rules**

- One suggestion, the most common one, with the form prefilled. Dismissing it costs nothing.
- The suggestion opens the next screen or a dialog; it never creates anything without a click.

## One action, two effects

**Signals in the frontend**

- Actions on a record (reject, approve, save a meeting, add a note) followed, in the product's
  normal flow, by an action in another section on the same record (create a task, send a message,
  schedule a follow-up).
- Product docs or earlier sessions that describe that sequence.

**Check against the API**

- Both operations exist and can be called in order from the client, or the API offers one
  operation that does both. Name which.
- What happens when the second call fails after the first succeeded: the user sees which part
  happened, and can retry the second.
- Both are already audited, or the combo needs an audit entry of its own.

**Build rules**

- The second effect is offered, prefilled and on by default only when the user would almost always
  want it; otherwise off by default.
- The result says both outcomes in words.

## Dead end → fix in place

**Signals in the frontend**

- Error and "not set up" states that block a task: a missing setting, a missing field, a missing
  template.
- Guards that disable a control with a reason pointing at configuration.

**Check against the API**

- The configuration has a create or update operation, and the roles that may call it.

**Build rules**

- A user allowed to fix it gets the fix in place; others see who can fix it and what to ask for.

## Act where you see it

**Signals in the frontend**

- Read-only lists and views of items (notes, documents, history, list rows) whose actions live on
  another screen: a link to "open" the item, then the action there.
- Items that carry everything an action needs (a note's author and field, a document's owner).

**Check against the API**

- The action's operation accepts what the item already holds (its id, parent, author), and the
  roles that may call it from here.

**Build rules**

- The action sits on the item, prefilled from it; the full screen stays the place for anything
  more.

## Across all kinds

- **AI output.** When a combo starts from AI-generated content (a suggested value, a drafted
  message), it stays a draft a person checks before anything is created or sent.
- **Personal data.** A combo that carries a customer's data into another resource (a reason into a
  task, a note into a message) copies only what that resource needs.
- **Audit.** Creating through a combo is the same API call as creating on the full screen; it needs
  no extra audit unless it adds a second effect the API does not record.
