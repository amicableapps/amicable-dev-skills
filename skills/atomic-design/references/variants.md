# Variants and props over className

The kit's appearance API is a closed set of typed variants. This file holds the patterns per
framework and the checklist `review` applies to a diff.

## Why

- A class prop lets every caller repaint the component, so the kit stops being the single source of
  its look, and a token change no longer reaches every screen.
- Variants are named decisions (`danger`, `compact`) that design and code share; a class string is
  an undocumented one.
- Typed variants fail the type check when a caller asks for a look that does not exist.

## Patterns

Svelte 5:

```svelte
<script lang="ts">
  type Variant = 'primary' | 'secondary';
  let {
    variant = 'primary',
    children,
    ...rest
  }: {
    variant?: Variant;
    children: import('svelte').Snippet;
    /** Layout belongs to the parent. */
    className?: never;
    class?: never;
  } & Omit<import('svelte/elements').HTMLButtonAttributes, 'class' | 'style'> = $props();

  const styles: Record<Variant, string> = {
    primary: 'bg-accent text-on-accent',
    secondary: 'border border-rule text-ink'
  };
</script>

<button {...rest} class="h-9 rounded-md px-3 {styles[variant]}">{@render children()}</button>
```

React:

```tsx
type Variant = 'primary' | 'secondary';
type Props = Omit<React.ButtonHTMLAttributes<HTMLButtonElement>, 'className' | 'style'> & {
  variant?: Variant;
  className?: never;
  style?: never;
};

const styles: Record<Variant, string> = { primary: 'btn-primary', secondary: 'btn-secondary' };

export function Button({ variant = 'primary', ...rest }: Props) {
  return <button {...rest} className={`btn ${styles[variant]}`} />;
}
```

Vue: declare `inheritAttrs: false`, bind only the attributes you allow (`v-bind="allowedAttrs"`),
and type `variant` as a union in `defineProps`. A `class` or `style` on the component is then
dropped rather than merged.

A lookup table from variant to classes keeps the mapping in one place; a growing `if` over variants
is the same table written badly.

## Layout on the parent

```svelte
<!-- before: the atom is told how to sit -->
<Button className="ml-auto shrink-0">Save</Button>

<!-- after: the parent owns its layout -->
<div class="ml-auto shrink-0"><Button>Save</Button></div>
```

## Review checklist

For every component the diff adds or changes in the kit:

1. An atom declares `className?: never` (and `class`/`style` where the framework passes them), and
   no rest spread, slot prop or wrapper lets a caller class or style through.
2. Appearance is chosen by a typed variant, and every variant value has a caller now.
3. No differently named escape hatch (`extraClass`, `wrapperClass`, `sx`, `css`, `styles`).
4. Classes come from tokens, not raw colours or one-off values.
5. Callers that needed a different look got a variant; callers that needed placement got a
   wrapper.
6. Imports point down, and the kit imports no route, feature, domain type, store or API.
7. Legacy class props that remain are labelled as a seam with an `ATOMIC-TODO`, and no new caller
   uses them.

Report each miss with `file:line`, the rule, and the fix.
