<script>
  import { getContext } from "svelte"
  import Scatterplot from './Scatterplot.svelte';
	import data from './data.js';

  // export let text
  export let dataProvider;
  export let dataProvider2;
  export let xaxis;
  export let yaxis;
  export let title;

  const { styleable } = getContext("sdk")
  const component = getContext("component")
</script>

<div use:styleable={$component.styles}>

  <div class="chart">
    <h1>{title}</h1>
  <Scatterplot points={dataProvider} points2={dataProvider2} xaxis={xaxis}  yaxis={yaxis}/>
  
  <!-- <Scatterplot points={data.b}/>
  <Scatterplot points={data.c}/>
	<Scatterplot points={data.d}/>  -->
</div>
</div>


<style>
	.chart {
		width: 100%;
		max-width: 640px;
		height: calc(100% - 4em);
		min-height: 480px;
		max-height: 480px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
		margin: 0 auto;
	}
  /* div {
    background: red;
  } */
</style>
