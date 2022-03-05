<script>
  import { LayerCake, Svg } from 'layercake'

  import SimpsonsGrid from './SimpsonsGrid.svelte'

  export let controls = false
  export let groupedByAge = false

  let cHeight

  $: bottom = cHeight > 600 ? 120 : cHeight > 400 ? 100 : 75

  let animated = false
  let x = {
    NV: { O50: [1, 17], U50: [107, 4] },
    V: { O50: [188, 29], U50: [349, 1] },
  }

  let data = []
  $: {
    let tot = 0
    let appendGroup = (group, num) => {
      for (let i = 0; i < num; i++) {
        data.push({ ...group, id: tot++ })
      }
    }
    appendGroup({ vaccinated: false, infected: false, age: 'O50' }, 1)
    appendGroup({ vaccinated: false, infected: true, age: 'O50' }, 17)
    appendGroup({ vaccinated: false, infected: false, age: 'U50' }, 107)
    appendGroup({ vaccinated: false, infected: true, age: 'U50' }, 4)
    appendGroup({ vaccinated: true, infected: false, age: 'O50' }, 188)
    appendGroup({ vaccinated: true, infected: true, age: 'O50' }, 29)
    appendGroup({ vaccinated: true, infected: false, age: 'U50' }, 349)
    appendGroup({ vaccinated: true, infected: true, age: 'U50' }, 1)
    console.log(data)
  }

  let bigGrid = true
  setInterval(() => {
    if (!animated) return
    if (bigGrid) {
      bigGrid = false
      console.log('Big grid: ', bigGrid)
      switchContext(minGrids)
    } else {
      bigGrid = true
      console.log('Big grid: ', bigGrid)
      switchContext(majGrids)
    }
  }, 10000)
</script>

<div class="chart__wrapper" bind:clientHeight={cHeight}>
  <div class="chart-container">
    <LayerCake
      padding={{ top: 20, bottom: bottom, left: 0, right: 0 }}
      yDomain={[0, null]}
      {data}
    >
      <SimpsonsGrid {animated} {groupedByAge} />
    </LayerCake>
  </div>
  {#if controls}
    <div class="chart__controls">
      <div class="animate">
        <button class:active={animated} on:click={() => (animated = !animated)}
          >{animated ? 'pause' : 'resume'}</button
        >
      </div>
    </div>
  {/if}
</div>

<style lang="scss">
  .chart__wrapper {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    .chart-container {
      width: 100%;
      height: 100%;
    }
    .chart__controls {
    }
  }

  @media (min-width: 992px) {
    .chart_wrapper {
      height: 100%;
    }
  }
</style>
