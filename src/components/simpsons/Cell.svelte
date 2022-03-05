<script>
  import { interpolate } from 'd3-interpolate'
  import { tweened } from 'svelte/motion'
  import { cubicInOut, sineInOut } from 'svelte/easing'

  export let duration = 5000
  export let shape = 'rect'
  export let x
  export let y
  export let width
  export let height
  export let fill = 'red'
  export let stroked = false
  export let fillOpacity = 1
  export let id
  export let attrs

  const tweenedDimensions = tweened(
    {
      x,
      y,
      width,
      height,
    },
    {
      duration,
      interpolate: interpolate,
      // easing: sine// cubicInOut
    }
  )

  function setTween(data) {
    tweenedDimensions.set(data)
  }
  $: setTween({
    x,
    y,
    width,
    height,
  })

  function _attrs() {
    return { style: '' }
    // let style = stroked ? 'stroke: black; stroke-width: 1px' : 'stroke: none'
    // return {
    //   style,
    // }
  }

  // $: console.log('x: ', x)
</script>

<g
  class="cell"
  transform={`translate(${$tweenedDimensions.x}, ${$tweenedDimensions.y})`}
>
  {#if shape === 'rect'}
    <rect
      {..._attrs()}
      height={$tweenedDimensions.height}
      width={$tweenedDimensions.width}
      {fill}
      fill-opacity={fillOpacity}
    />
  {:else}
    <path
      {..._attrs()}
      d={shape}
      transform={`scale(${$tweenedDimensions.width})`}
      {fill}
    />
  {/if}
  {#if stroked}
    <circle
      r={$tweenedDimensions.width / 6}
      cx={width / 2}
      cy={width / 2}
      fill="black"
    />
  {/if}
</g>
