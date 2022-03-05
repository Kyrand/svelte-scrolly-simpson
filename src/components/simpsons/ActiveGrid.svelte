<script>
  export let cells
  export let width
</script>

{#each cells as cell}
  <g>
    {#if cell.shape == 'rect'}
      <rect {width} height={width} x={cell.x} y={cell.y} fill={cell.color} />
    {/if}
  </g>
{/each}
