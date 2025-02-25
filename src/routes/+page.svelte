<script lang="ts">
	import ipodCursor from '$lib/assets/ipod-cursor.png';
	import nokiaCursor from '$lib/assets/nokia-cursor.png';
	import ipodLarge from '$lib/assets/ipod-large.jpeg';
	import nokiaLarge from '$lib/assets/nokia-large.png';

	type PointsArray = Array<{ x: number; y: number }>;
	let points: PointsArray = [];

	type PathsArray = Array<PointsArray>;
	let paths: PathsArray = [];

	let drawing = false;
	let currentCursorImg = nokiaCursor;
	let currentImg = nokiaLarge;
	
	// Add CRT effect toggle
	let crtEffect = true;

	function startDrawing(event: PointerEvent) {
		event.preventDefault();
		drawing = true;
		addPoint(event);
	}

	function draw(event: PointerEvent) {
		if (drawing) {
			event.preventDefault();
			addPoint(event);
		}
	}

	function stopDrawing(event: PointerEvent) {
		event.preventDefault();
		paths = [...paths, points];
		points = [];
		drawing = false;
	}

	function addPoint(event: PointerEvent) {
		const newPoint = { x: event.clientX, y: event.clientY };
		points = [...points, newPoint];
	}
</script>

<!-- {paths.length} -->

<div class="menu-wrapper">
	<button on:click={() => (paths = paths.slice(0, paths.length - 1))}>↩️</button>
	<button on:click={() => (paths = [])}>clear</button>
	<button
		on:click={() => {
			currentCursorImg = nokiaCursor;
			currentImg = nokiaLarge;
		}}>nokia</button
	>
	<button
		on:click={() => {
			currentCursorImg = ipodCursor;
			currentImg = ipodLarge;
		}}>ipod</button
	>
	<button on:click={() => (crtEffect = !crtEffect)}>
		{crtEffect ? 'CRT: ON' : 'CRT: OFF'}
	</button>
</div>
<div class="svg-wrapper">
	<!-- CRT scan lines overlay -->
	{#if crtEffect}
		<div class="crt-overlay"></div>
	{/if}
	
	<svg
		width="100%"
		height="100%"
		style="cursor: url({currentCursorImg}), auto; margin-top: 0; height: 100%; width: 100%;"
		on:pointerdown={startDrawing}
		on:pointermove={draw}
		on:pointerup={stopDrawing}
	>
		{#if points.length > 1}
			<polyline
				fill="none"
				stroke="none"
				stroke-width="2"
				points={points.map((p) => `${p.x},${p.y}`).join(' ')}
			/>
		{/if}
		{#if paths.length >= 1}
			{#each paths as savedPath}
				<polyline
					fill="none"
					stroke="none"
					stroke-width="2"
					points={savedPath.map((p) => `${p.x},${p.y}`).join(' ')}
				/>
			{/each}
		{/if}
		{#each paths as savedPath}
			{#each savedPath as point, index (point.x + '-' + point.y + '-' + index)}
				<!-- <text x={point.x} y={point.y} font-size="20">{emoji}</text> -->
				<image x={point.x - 20} y={point.y} width="128" height="128" href={currentImg}></image>
			{/each}
		{/each}
		{#each points as point, index (point.x + '-' + point.y + '-' + index)}
			<!-- <text x={point.x} y={point.y} font-size="20">{emoji}</text> -->
			<image x={point.x - 20} y={point.y} width="128" height="128" href={currentImg}></image>
		{/each}
	</svg>
</div>

<style>
	svg {
		touch-action: none;
		border: 1px solid #ccc;
	}

	.svg-wrapper {
		height: calc(100dvh);
		width: 100dvw;
		position: relative;
	}

	.menu-wrapper {
		position: absolute;
		margin: 0.5rem;
		z-index: 10;
	}

	button {
		border: 2px solid grey;
		background-color: antiquewhite;
		font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
		font-size: 1.125rem;
		margin: 0.25rem;
	}

	/* CRT scan lines effect */
	.crt-overlay {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background: linear-gradient(
			rgba(18, 16, 16, 0) 50%, 
			rgba(0, 0, 0, 0.25) 50%
		);
		background-size: 100% 2px;
		z-index: 9;
		pointer-events: none; /* Allows clicks to pass through to the SVG */
		opacity: 0.8;
		animation: flicker 0.15s infinite;
	}

	/* Add subtle CRT flicker effect */
	@keyframes flicker {
		0% { opacity: 0.8; }
		50% { opacity: 0.75; }
		100% { opacity: 0.8; }
	}

	:global(body) {
		margin: 0;
		padding: 0;
		overflow: hidden;
		background-color: #fff; /* Dark background for CRT effect */
	}
</style>