<script>
  export let data
  export let scale

  let val
  $: {
    let nv = data.nvinf / data.nvtot
    let v = data.vinf / data.vtot
    val = (1 - v / nv) * 100
  }
</script>

<!-- <div style={`transform: scale(${scale}, ${scale})`}> -->
<div class="label">
  <div class="eq__lead">Efficacy:</div>
  <div class="eq">
    <div class="big1">1-&nbsp;</div>
    <div class="divider">
      <div class="num">
        <span class="triangle-up">({data.vinf}/{data.vtot})</span>
      </div>
      <div class="den">
        <span class="triangle-up">({data.nvinf}/{data.nvtot})</span>
      </div>
    </div>
    <div class="efficacy">
      = <span class="bigsign">{val.toPrecision(3)}%</span>
    </div>
  </div>
</div>

<style lang="scss">
  .label {
    flex: 1;
  }
  .eq {
    // transform: scale(0.8, 0.8);
    font-family: serif;
    font-size: 1.5em;
    display: flex;
    gap: 0.3em;
    align-items: center;
    opacity: 1;
    //transition-delay: 2000ms;
    div {
      flex: 1;
    }
    .big1 {
      font-size: 2.2em;
      // flex: 0 0 100%;
    }
    .efficacy {
      font-size: 2.2em;
      font-family: serif;
      flex: 0 0 100%;
    }
    .num {
      border-bottom: 2px solid black;
      color: green;
    }
    .den {
      color: red;
    }
  }

  .eq__lead {
    font-size: 3.3em;
    font-family: serif;
  }

  .triangle-up::before {
    content: '';
    width: 0;
    height: 0;
    border-left: 0.3em solid transparent;
    border-right: 0.3em solid transparent;
    border-bottom: 0.6em solid green;
    right: 100%;
    top: 25%;
    position: absolute;
    clear: both;
  }

  .den .triangle-up::before {
    border-bottom: 0.6em solid red;
  }

  .triangle-up {
    position: relative;
  }
</style>
