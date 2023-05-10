<script lang="ts">
  import { browser } from '$app/environment';
  import { tick } from 'svelte';
  export let stretchFirst = false,
    gridGap = '0.5em',
    colWidth = 'minmax(min(20em, 100%), 1fr)',
    update: any;

  interface Grid {
    _el: HTMLElement;
    gap: number;
    items: HTMLElement[];
    ncol: number;
    mod: number;
  }

  let grids: Grid[] = [],
    masonryElement: HTMLDivElement;

  export const refreshLayout = async () => {
    grids.forEach(async (grid) => {
      let ncol = getComputedStyle(grid._el).gridTemplateColumns.split(' ').length;

      grid.items.forEach((c: HTMLElement) => {
        let new_h = c.getBoundingClientRect().height;

        if (new_h !== +c.dataset.h) {
          c.dataset.h = new_h;
          grid.mod++;
        }
      });

      if (grid.ncol !== ncol || grid.mod) {
        grid.ncol = ncol;
        grid.items.forEach((c) => c.style.removeProperty('margin-top'));

        if (grid.ncol > 1) {
          grid.items.slice(ncol).forEach((c, i) => {
            let prev_fin = grid.items[i].getBoundingClientRect().bottom,
              curr_ini = c.getBoundingClientRect().top;

            c.style.marginTop = `${prev_fin + grid.gap - curr_ini}px`;
          });
        }

        grid.mod = 0;
      }
    });
  };

  const calcGrid = async (_masonryArr: HTMLElement[]) => {
    await tick();
    if (_masonryArr.length && getComputedStyle(_masonryArr[0]).gridTemplateRows !== 'masonry') {
      grids = _masonryArr.map((grid) => {
        return {
          _el: grid,
          gap: parseFloat(getComputedStyle(grid).rowGap),
          items: [...grid.childNodes].filter(
            (c) => c.nodeType === 1 && +getComputedStyle(c as Element).gridColumnEnd !== -1
          ) as HTMLElement[],
          ncol: 0,
          mod: 0,
        };
      });
      refreshLayout();
    }
  };

  $: browser && (masonryElement || update) && calcGrid([masonryElement]);
</script>

<svelte:window on:resize={refreshLayout} />

<div
  bind:this={masonryElement}
  class="masonry-grid"
  class:stretch-first={stretchFirst}
  style="--grid-gap:{gridGap};--col-width:{colWidth}"
>
  <slot />
</div>

<style>
  .masonry-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, var(--col-width));
    grid-template-rows: masonry;
    justify-content: center;
    grid-gap: var(--grid-gap);
    padding: var(--grid-gap);
  }
  :global(.masonry-grid > *) {
    align-self: start;
  }
  :global(.masonry-grid.stretch-first > *:first-child) {
    grid-column: 1/ -1;
  }
</style>
