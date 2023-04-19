<script>
	import { onMount } from 'svelte';
	import { scaleLinear } from 'd3-scale';

	export let points;
	export let points2;
	export let xaxis;
	export let yaxis;

	let svg;
	let width = 500;
	let height = 500;

	let maxX = Number.NEGATIVE_INFINITY;
	let maxY = Number.NEGATIVE_INFINITY;
	let xRange = [];
	let yRange = [];

	for (let item of points.rows) {
		maxX = item.x > maxX ? item.x : maxX;
		maxY = item.y > maxY ? item.y : maxY;
	}

	for (let item of points2.rows) {
		maxX = item.px > maxX ? item.px : maxX;
		maxY = item.py > maxY ? item.py : maxY;
	}

	maxX = maxX + maxX%4;
	maxY = maxY + maxY%4;

	for (let i = 0; i <= maxX; i=i+4) {
		xRange.push(i);
	}

	for (let i = 0; i <= maxY; i=i+4) {
		yRange.push(i);
	}

	// let sGapX = maxX / 4 + ;
	// let sGapY = maxX / 4 + 1;
	// let lGapX = maxX / 4 + 1;
	// let lGapY = maxY / 10 

	const padding = { top: 20, right: 40, bottom: 40, left: 20 };

	$: xScale = scaleLinear()
		.domain([0, maxX])
		.range([padding.left, width - padding.right]);

	$: yScale = scaleLinear()
		.domain([0, maxY])
		.range([height - padding.bottom, padding.top]);

	$: xTicks = width > 180 ?
		xRange :
		[0, 10, 20];

	$: yTicks = width > 180 ?
		yRange :
		[0, 10, 20];

	onMount(resize);

	function resize() {
		({ width, height } = svg.getBoundingClientRect());
	}

	// const sets = [];
	// if (!!points) {
	// 	for (let point of points.rows) {
	// 		sets.push(<circle cx='{xScale(point.x)}' cy='{yScale(point.y)}' r='5' fill="#ccc"/>)
	// 	}
		
	// }

</script>

<svelte:window on:resize='{resize}'/>

<svg bind:this={svg}>

	<!-- y axis -->
	<g class='axis y-axis'>
		{#each yTicks as tick}
			<g class='tick tick-{tick}' transform='translate(0, {yScale(tick)})'>
				<line x1='{padding.left}' x2='{xScale(22)}'/>
				<text x='{padding.left - 8}' y='+4'>{tick}</text>
			</g>
		{/each}
	</g>

	<!-- x axis -->
	<g class='axis x-axis'>
		{#each xTicks as tick}
			<g class='tick' transform='translate({xScale(tick)},0)'>
				<line y1='{yScale(0)}' y2='{yScale(22)}'/>
				<text y='{height - padding.bottom + 16}'>{tick}</text>
			</g>
		{/each}
	</g>

	<!-- data -->

	{#each points.rows as point}
		<circle cx='{xScale(point[xaxis])}' cy='{yScale(point[yaxis])}' r='5' fill="#ccc"/>
	{/each}

	{#each points2.rows as point}
		<circle cx='{xScale(point.px)}' cy='{yScale(point.py)}' r='5' fill="tomato"/>
	{/each}

</svg>

<div>Hello {points.rows[0].x}</div>
<!-- <div style="display: flex;">{Object.keys(points.datasource)}</div>
<div>{Object.keys(points.datasource.fields)}</div> -->

<style>

	svg {
		width: 50%;
		height: 50%;
		float: left;
	}

	circle {
		/* fill: red; */
		fill-opacity: 0.6;
		stroke: rgba(0,0,0,0.5);
	}

	.tick line {
		stroke: #ddd;
		stroke-dasharray: 2;
	}

	text {
		font-size: 12px;
		fill: #999;
	}

	.x-axis text {
		text-anchor: middle;
	}

	.y-axis text {
		text-anchor: end;
	}
</style>