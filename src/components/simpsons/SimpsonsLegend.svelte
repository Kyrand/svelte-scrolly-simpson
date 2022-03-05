<script>
  export let x
  export let y
</script>

<div class="legend" style={`left: ${x}px; bottom: ${y}px`}>
  <div class="legend__grid">
    <div class="symbol vac square">&nbsp;Vaccinated</div>
    <div class="symbol nvac square">&nbsp;Unvaccinated</div>
    <div class="symbol triangle">&nbsp;Severe infection</div>
    <div class="symbol circle">&nbsp;Under 50</div>
  </div>
</div>

<style lang="scss">
  .legend {
    position: absolute;
    transform: translate(-50%, 50%);
  }

  .legend__grid {
    display: flex;
    flex-direction: column;
    gap: 0.25em;
    font-size: 0.8em;
    .vac::before {
      background: green;
    }
  }

  .symbol {
    line-height: 1;
  }

  .symbol::before {
    content: ' ';
    background-color: gray;
    height: 1em;
    width: 1em;
    right: 100%;
    position: absolute;
    clear: both;
  }

  .triangle::before {
    content: '';
    width: 0;
    height: 0;
    border-left: 0.5em solid transparent;
    border-right: 0.5em solid transparent;
    border-bottom: 1em solid gray;
    background-color: transparent;
    right: 100%;
    position: absolute;
    clear: both;
  }

  .square {
  }

  .circle::before {
    background-color: black;
    border-radius: 50%;
    height: 0.3em;
    width: 0.3em;
    margin: 0.3em;
  }

  .vac::before {
    background-color: green;
  }

  .nvac::before {
    background-color: red;
  }
</style>
