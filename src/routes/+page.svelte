<script lang="ts">
	type PointsArray = Array<{ x: number; y: number }>;
	let points: PointsArray = [];

	type PathsArray = Array<PointsArray>;
	let paths: PathsArray = [];

	let drawing = false;
	const emoji = '🌟';
	const emojiDataUri =
		'data:image/png;base64,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';
	const nokiaImg =
		'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAFkAAACACAMAAABX5DV6AAADAFBMVEUJDQoKEw0NGBINGBIVGhYVGhYVGhYVGhYVGhYYJRsYJRsYJRsYJRsdKiIdKiIdKiIbMiQbMiQiKh8iKSMiKSMiKSMiKSMmNCkmNCkmNCkmNCkmNCkrPDErPDE1Ojc1Ojc1Ojc1OjcrQC4rRTYrRTYrRTYrRTYrRTY0Rzo0Rzo0Rzo0Rzo0Rzo2UD47RUE7RUE5VUY5VUY5VUY5VUY5VUY7YU49ZFJFSklFSklFSklFSklDWEtDWEtDWEtDWEtDWEtJWFFJWFFJWFFVWlhVWlhVWlhVWlhaX2BLZlVLZlVLZlVLZlVLZlVLZlVTaVxTaVxTaVxTcV9MdWJbZmFbZmFbZmFYd2dYd2dYd2dYd2dYd2dYd2dka2hka2hka2hka2hndG1ndG1ndG1tdXFtdXFtdXFwcG50enh0enh0enh0enhcgW5cgW5dhXNdhXNgg25niHZniHZniHZniHZniHZniHZniHZokX1okX15gn15gn15gn1ujIBulIB9hIF9hIF0mYZ0mYZ0mYZ0mYZ0mYZ/nJFtp5B4o4x4o4x4o4x7qJF7qJF7qJF7qJF/sZZ/sZZwpKyEiYaEiYaEiYaEiYaKkY2KkY2NlJGNlJGNlJGUmpeUmpeUmpeUmpeFqpeFqpeFqpeFqpeFqpeJs5yJs5yJs5yaoZ2aoZ2aoZ2Qs56LuKKLuKKLuKKLuKKdpaGdpaGdpaGdpaGVuaaVuaaVuaaVuaaVuaaVuaalqqelqqelqqelqqeqsq2qsq2qsq2qsq2qsq2ttLCttLCttLCttLC1u7e1u7e1u7e1u7e1u7eNucKQusS7v8CPwKiXw6uXw6uXw6uax7Gax7Gax7GkxrOkxrOkxrOj0bm7wb27wb27wb27wb2WwMusztu+w8C+w8C+w8C+09bAwr/EycbEycbEycbEycbOz9DL0c3L0c3L0c3L0c3O0tDO0tDW2dfW2dfW2dfW2dfW2dfH3ePH3ePc4N3e4eDg4d7n6ejr7/Dt8O7u8PDw7+7w8O/39/cAAAAuYu7hAAABAHRSTlP///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////8AU/cHJQAADy9JREFUaIHtmj9o48q+x89ZtklhOGwKNyHFTZMmIc05KdIEXMSVwdfgyoVBsCrCEndBkBVp9gZcLE+FEEzhJiBIChWpVKiZYpr7soHAwqp4V3A9hacQ0xykjOVI+34jy4mTdRLlcG5z751lt1jYT377nd//0U/f/1Xnp/+S/+3JWXYbcZFm2Z9MztJoSHzK+c3tK9nPk2/D4WVAQ86jOBZg959EzmIPuXSUc5NExCL+k8gpNu0gDAEcJ6ByNomEeA36KXI2NMzLUIIjeXvypK9DLyRnGTV0zEJGGRdJAc6yJIpfIfUicsYNxWEhDSR4nKR36Dh6hdQ/kjPhqRYNqU8CFoNLzJGzKC6vxw9k4WrIDynBJAi58LBIkskdOn0F+hE54y4CgX3s4oCBV/gkHAP6zmhRXo8H5CyhQwgMRlwH+5SBX0xiP543OotLGz1PziIfLm1EiWN7JCdzwUwqLzG710Mk5dBz5JQRoLEA28gFlSkDOSIe+NF43mgRx+kryekwkFYSF1k2kH2aG51kOHjgeVnZeLkjp9IXooggSwcyqBHk5DhCLhdz4QI5VYjXkDOKZU4LLMNUc/IdWgSBJN9bDbmpjB4zsgA3ozRwVc0GsuPhKRn+Gxl1oj+ix4xMpZkEo07X1UzkuFNy7h62w8DoOT2SUv4xI/uuh+HY3ZZrGUCWckzvMMriIBbSP+7zRxmjZ+ThlOzpLQThfSdHbjS3QvEoyEsYPSOHniQTgloq0U1bygEuPVWaYSCP5+NFlIjEGTkGm0Fo4nXfe0gv5JjeIeR+SEQimfD4Dn2TvIiekVPXm6K1joXv5ZgqLZOp4ER371JTEr+Yme78GU/JPupoxJByQLAULi3RHCuup/NXeN5cpMzkUFwn9477O4wjz+hYCKIxvRP6RaXvyLwgE71jER3ZhUvnuZS6hq0gx44Lx+M04C8afZc3Es+T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';
	const ipodImg =
		'data:image/png;base64,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';
	const nokiaUrl = 'https://i.ibb.co/nBGtpNT/signal-2024-05-03-161145-003.png';
	const ipodUrl = 'https://i.ibb.co/BTLy0FC/signal-2024-05-03-161145-002.jpg';

	let currentCursorImg = nokiaImg;
	let currentCursorImgUrl = nokiaUrl;

	function startDrawing(event) {
		event.preventDefault();
		drawing = true;
		addPoint(event);
	}

	function draw(event) {
		if (drawing) {
			event.preventDefault();
			addPoint(event);
		}
	}

	function stopDrawing(event) {
		event.preventDefault();
		paths = [...paths, points];
		points = [];
		drawing = false;
	}

	function addPoint(event) {
		const newPoint = { x: event.clientX, y: event.clientY };
		points = [...points, newPoint];
	}
</script>

<!-- {paths.length} -->
<div style="height: calc(100vh - 2rem); width: 100vw">
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
				<image x={point.x - 16} y={point.y} width="89" height="128" href={currentCursorImgUrl}
				></image>
			{/each}
		{/each}

		{#each points as point, index (point.x + '-' + point.y + '-' + index)}
			<!-- <text x={point.x} y={point.y} font-size="20">{emoji}</text> -->
			<image x={point.x - 16} y={point.y} width="89" height="128" href={currentCursorImgUrl}
			></image>
		{/each}
	</svg>
	<button on:click={() => (paths = paths.slice(0, paths.length - 1))}>↩️</button>
	<button on:click={() => (paths = [])}>clear</button>
	<button
		on:click={() => {
			currentCursorImg = nokiaImg;
			currentCursorImgUrl = nokiaUrl;
		}}>nokia</button
	>
	<button
		on:click={() => {
			currentCursorImg = ipodImg;
			currentCursorImgUrl = ipodUrl;
		}}>ipod</button
	>
</div>

<style>
	svg {
		touch-action: none;
		border: 1px solid #ccc;
	}

    :global(body) {
        margin: 0;
        padding: 0;
        overflow: hidden;
    }

</style>
