<script>
  import { getContext } from 'svelte'
  import { fade } from 'svelte/transition'
  const { data, containerWidth, width, height, padding } =
    getContext('LayerCake')
  import { Svg, Html } from 'layercake'
  import { scaleBand, scaleLinear } from 'd3-scale'
  import { range } from 'd3-array'
  import ActiveGrid from './ActiveGrid.svelte'
  import Cell from './Cell.svelte'
  import Eq from './SimpsonEq.svelte'
  import Legend from './SimpsonsLegend.svelte'

  export let columns = 10
  export let colors = { NV: 'red', V: 'green' }
  export let columnWidth = 0.03
  export let gridPad = 10
  export let cellPadding = 0.1
  export let labels = []
  export let animated = false
  export let duration = 2000
  export let groupedByAge = false

  let shapes = {
    triDown: 'M 0 0 L 1 0 L 0.5 0.866 z',
    triUp: 'M 0 0.933 L 1 0.933 L 0.5 0.067 z',
  }
  let loaded = false

  $: context = { labels: [] }
  $: desktopFlag = $containerWidth > 600
  $: if (groupedByAge) {
    console.log('Switching to minor grids')
    switchContext(minGrids)
  } else {
    console.log('Switching to major grids')
    switchContext(majGrids)
  }

  function makeColumns(
    data,
    group = 'vaccinated',
    columns = 10,
    width = 600,
    offsetX = 0,
    offsetY = 0,
    padding = 0.25
  ) {
    let cells = []
    let majScale = scaleBand()
      .domain([0, 1])
      .range([0, width])
      .paddingOuter(0.1)
      .paddingInner(0.1)
    let gridScale = scaleBand()
      .domain(range(columns))
      .range([0, majScale.bandwidth()])
      .padding(0.1)
    let heightScale = scaleLinear()
      .domain([0, $height])
      .range([$height - gridScale.bandwidth(), 0])
    let calcAttrs = (cell, i, group) => {
      let x = majScale(group) + gridScale(i % columns) + offsetX
      let y = gridScale.step() * parseInt(i / columns) + offsetY
      y = heightScale(y)
      return {
        x,
        y,
        width: gridScale.bandwidth(),
        height: gridScale.bandwidth(),
        id: cell.id,
        attrs: { ...cell },
        fill: cell.vaccinated ? 'green' : 'red',
        shape: cell.infected ? shapes.triUp : 'rect',
        stroked: cell.age === 'U50' ? true : false,
      }
    }

    let tot = 0
    let _cells = data
      .filter((d) => d.vaccinated)
      .sort((a, b) => (a.infected === b.infected ? 0 : a.infected ? 1 : -1))

    _cells.forEach((c) => {
      cells.push(calcAttrs(c, tot++, 0))
    })

    tot = 0
    _cells = data
      .filter((d) => !d.vaccinated)
      .sort((a, b) => (a.infected === b.infected ? 0 : a.infected ? 1 : -1))

    _cells.forEach((c) => {
      cells.push(calcAttrs(c, tot++, 1))
    })

    // cells = cells.sort((a, b) =>
    //   a.attrs.infected === b.attrs.infected ? 0 : a.attrs.infected ? -1 : 1
    // )

    return cells
  }

  function getGroupStats(cells) {
    let s = {}
    s.vtot = cells.filter((c) => c.vaccinated).length
    s.vinf = cells.filter((c) => c.vaccinated).filter((c) => c.infected).length
    s.nvtot = cells.filter((c) => !c.vaccinated).length
    s.nvinf = cells
      .filter((c) => !c.vaccinated)
      .filter((c) => c.infected).length
    console.log('Groups stats: ', s)
    return s
  }

  let cells = [],
    tdata

  let majGrids, minGrids
  $: {
    majGrids = {
      cells: makeColumns($data, 'vaccinated', 20, $width),
      labels: [],
    }

    majGrids.labels = [
      {
        x: $width / 2,
        y: $padding.bottom,
        stats: getGroupStats($data),
        scale: 0.5,
        width: $width * 0.7,
      },
    ]

    let labels = []
    tdata = $data.filter((d) => d.age === 'O50')
    labels.push({
      x: (3 * $width) / 4,
      y: $padding.bottom,
      stats: getGroupStats(tdata),
      scale: 0.3,
      width: $width * 0.35,
    })
    let o50 = makeColumns(tdata, 'vaccinated', 10, $width / 2, $width / 2)
    tdata = $data.filter((d) => d.age === 'U50')
    labels.push({
      x: $width / 4,
      y: $padding.bottom,
      stats: getGroupStats(tdata),
      scale: 0.3,
      width: $width * 0.35,
    })
    let u50 = makeColumns(tdata, 'vaccinated', 10, $width / 2)
    minGrids = {
      cells: o50.concat(u50),
      labels: labels,
    }
    //console.log('Data: ', cells)
    //cells = majGrids.cells
    switchContext(majGrids)
  }

  let switchContext = (_context) => {
    context = _context
    console.log('Context: ', context)
    if (context.cells) {
      cells = context.cells
    }
  }

  // let bigGrid = true
  // setInterval(() => {
  //   if (!animated) return
  //   if (bigGrid) {
  //     bigGrid = false
  //     console.log('Big grid: ', bigGrid)
  //     switchContext(minGrids)
  //   } else {
  //     bigGrid = true
  //     console.log('Big grid: ', bigGrid)
  //     switchContext(majGrids)
  //   }
  // }, 10000)

  $: console.log('Context changed: ', context, $width, $containerWidth)
</script>

<!-- <ActiveGrid {cells} width={cellWidth} /> -->
{#if $containerWidth > 100}
  <Svg>
    {#each cells as cell (cell.id)}
      <g>
        <Cell {...cell} {duration} />
      </g>
    {/each}
  </Svg>
  <Html>
    {#if context}
      <!-- <div class="labels"> -->
      {#each context.labels as label}
        <div
          class="label__wrapper"
          style={`left: ${label.x}px; bottom: -${label.y}px; _width: ${label.width}px`}
        >
          <Eq data={label.stats} scale={label.scale} />
        </div>
      {/each}
      <!-- </div> -->
    {/if}
    <!-- {#if desktopFlag}
      <Legend x={$width / 2} y={$height - $padding.top} />
    {/if} -->
  </Html>
{/if}

<style>
  .label__wrapper {
    position: absolute;
    transform: translateX(-50%);
    display: flex;
    font-size: min(1vh, 12px);
  }
</style>
