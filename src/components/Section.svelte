<script>
  import { slide } from 'svelte/transition';
  export let title = '';
  export let close = () => {};
</script>

<style>
  .section {
    border-bottom: 1px solid #65b6ca;
  }
  .inner {
    position: relative;
    padding: 1em;
    margin: 0 auto;
    max-width: 100ch;
    min-height: 200px;
  }
  h2 {
    font-weight: 700;
    font-size: 3em;
  }
  .close {
    position: absolute;
    bottom: 2em;
  }
</style>

<div class="section" transition:slide>
  <div class="inner">
    <h2>{title}</h2>
    <button on:click={close} class="close">Close</button>
  </div>
</div>
