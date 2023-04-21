<script>
	import { onMount } from 'svelte';
	import { scaleLinear } from 'd3-scale';
	import chroma from 'chroma-js';

	export let points;
	export let points2;
	export let xaxis;
	export let yaxis;
	export let timeField;
	export let xaxis2;
	export let yaxis2;
	export let timeField2;

	let svg;
	let width = 500;
	let height = 500;

	let maxX = Number.NEGATIVE_INFINITY;
	let maxY = Number.NEGATIVE_INFINITY;
	let minX = 0;
	let minY = 0;
	let tMax = new Date(points.rows[0][timeField]);
	let tMin = new Date(points.rows[0][timeField]);
	let t2Max = new Date(points2.rows[0][timeField2]);
	let t2Min = new Date(points2.rows[0][timeField2]);
	let xRange = [];
	let yRange = [];


	for (let item of points.rows) {
		maxX = item[xaxis] > maxX ? item[xaxis] : maxX;
		maxY = item[yaxis] > maxY ? item[yaxis] : maxY;
		minX = item[xaxis] < minX ? item[xaxis] : minX;
		minY = item[yaxis] < minY ? item[yaxis] : minY;
		if (!!item[timeField]) {
				const time = new Date(item[timeField]);
				tMax = time > tMax ? time : tMax;
				tMin = time < tMin ? time : tMin;
		}
	}

	if (!!points2.rows) {
		for (let item of points2.rows) {
			if (!!item[xaxis2] && !!item[xaxis2]) {
				maxX = item[xaxis2] > maxX ? item[xaxis2] : maxX;
				maxY = item[yaxis2] > maxY ? item[yaxis2] : maxY;
				minX = item[xaxis2] < minX ? item[xaxis2] : minX;
				minY = item[yaxis2] < minY ? item[yaxis2] : minY;
				if (!!item[timeField]) {
				const time = new Date(item[timeField2]);
				t2Max = time > t2Max ? time : t2Max;
				t2Min = time < t2Min ? time : t2Min;
		}
			}
		}
	}

	for (let i = minX; i <= maxX; i=i+ (maxX/4)) {
		xRange.push(i);
	}

	for (let i = minY; i <= maxY; i=i+(maxY/4)) {
		yRange.push(i);
	}

	const timeDiff = Math.abs(tMax - tMin);

	const pointColor = (time) => {
		if (!time) {
			return "black"
		}
		const f = chroma.scale(['yellow', 'red', 'black']);
		
		const t = new Date(time);
		const fraction =  Math.abs(t - tMin)/timeDiff
		return f(fraction).toString() 
	}

	const pointColor2 = (time) => {
		const f = chroma.scale(['rgba(2,0,36,1)', 'rgba(9,9,121,1)', 'rgba(0,212,255,1)']);
		if (!time) {
			return "tomato"
		}
		const t = new Date(time);
		const fraction =  Math.abs(t - tMin)/timeDiff
		return f(fraction).toString() 
	}

	const padding = { top: 20, right: 40, bottom: 40, left: 40 };

	$: xScale = scaleLinear()
		.domain([minX, maxX])
		.range([padding.left, width - padding.right]);

	$: yScale = scaleLinear()
		.domain([minY, maxY])
		.range([height - padding.bottom, padding.top]);

	$: xTicks = 
		xRange;

	$: yTicks = 
		yRange;

	onMount(resize);

	function resize() {
		({ width, height } = svg.getBoundingClientRect());
	}

</script>

<svelte:window on:resize='{resize}'/>
{#if !!points && !! points.rows && timeField}
	<div class="grad"><div style="color: black;">{tMin.getSeconds()}s</div><div style="color: yellow;">{tMax.getSeconds()}s</div></div>
{/if}

{#if !!points2 && !! points2.rows && timeField2}
	<div class="grad2"><div style="color: rgba(0,212,255,1);">{t2Min.getSeconds()}s</div><div style="color: rgba(2,0,36,1);">{t2Max.getSeconds()}s</div></div>
{/if}

<svg bind:this={svg}>

	<!-- y axis -->
	<g class='axis y-axis'>
		{#each yTicks as tick}
			<g class='tick tick-{tick}' transform='translate(0, {yScale(tick)})'>
				<line x1='{padding.left}' x2='{xScale(maxX + 10)}'/>
				<text x='{padding.left - 8}' y='+4'>{parseFloat(tick).toFixed(2)}</text>
			</g>
		{/each}
	</g>

	<!-- x axis -->
	<g class='axis x-axis'>
		{#each xTicks as tick}
			<g class='tick' transform='translate({xScale(tick)},0)'>
				<line y1='{yScale(minY)}' y2='{yScale(maxY + 10)}'/>
				<text y='{height - padding.bottom + 16}'>{parseFloat(tick).toFixed(2)}</text>
			</g>
		{/each}
	</g>

	<!-- data -->
	{#each points.rows as point}
		<circle cx='{xScale(point[xaxis])}' cy='{yScale(point[yaxis])}' r='5' fill={pointColor(point[timeField])}/>
	{/each}

		
	{#each points2.rows as point}
		{#if !!points2 && !! points2.rows && !!xaxis2 && !!yaxis2 && point[yaxis2] && point[xaxis2]}
			<circle cx='{xScale(point[xaxis2])}' cy='{yScale(point[yaxis2])}' r='5' fill={pointColor2(point[timeField2])}/>
		{/if}
	{/each}


	
</svg>

	<div>Hello  {points2.rows[0][xaxis2]} {points.rows[0][xaxis]}</div>
<!-- <div style="display: flex;">{Object.keys(points.datasource)}</div>
<div>{Object.keys(points.datasource.fields)}</div> -->

<style>
	.grad {
		background: rgb(2,0,36);
		background: linear-gradient(90deg, yellow 0%, red 50%, black 100%); 
		height: 19px; 
		width:40%;
		display: flex;
		justify-content: space-between;
		margin: 10px;
	}
	.grad2 {
		background: rgb(2,0,36);
		background: linear-gradient(90deg, rgba(2,0,36,1) 0%, rgba(9,9,121,1) 50%, rgba(0,212,255,1) 100%); 
		height: 19px; 
		width:40%;
		display: flex;
		justify-content: space-between;
		margin: 10px;
	}

	svg {
		width: 90%;
		height: 90%;
		float: left;
	}

	circle {
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