# svelte-masonry

A lightweight and dependency-free Masonry component for Svelte, based on the [article by Ana Tudor (@anatudor) on CSS Tricks](https://css-tricks.com/a-lightweight-masonry-solution).

Originally coded by [janzheng](https://github.com/janzheng) and updated by [edde746](https://github.com/edde746).

## Installation

```bash
npm install @edde746/svelte-masonry
```

## Usage

```javascript
import Masonry from "@edde746/svelte-masonry";
```

```html
<Masonry update={itemCount} stretchFirst>
  {#each data.slice(0, itemCount) as o}
    <div class="card">
      <header>
        <h3>{o.name}</h3>
      </header>
      <section>
        <p>{o.text}</p>
      </section>
    </div>
  {/each}
</Masonry>
```

**Note:** Make sure to update the `itemCount` variable whenever you want to update the layout.

## Props

| Prop          | Type   | Default   | Description                                                    |
|---------------|--------|-----------|----------------------------------------------------------------|
| stretchFirst  | bool   | `false`   | Whether the first item should stretch across all grid columns. |
| gridGap       | string | `"0.5em"`  | The gap between grid items.                                   |
| colWidth      | string | `"minmax(min(20em, 100%), 1fr)"` | The width of the grid columns.          |
| update        | any    |           | A variable to trigger layout updates.                          |
| rearrange     | bool   | `false`   | Whether to rearrange the grid items to best fill vertical space. |

## Functions

### `refreshLayout()`

Use this function to manually trigger a layout refresh. This might be useful in cases when the content of the grid items changes.