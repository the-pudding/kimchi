<script>
	import { fade } from 'svelte/transition';
	import { swipe } from 'svelte-gestures';
	export let chapter;
	export let chapterTracker = Number(chapter);
	export let w;
	export let h;
	export let maxWidth;
	function start() {
		chapterTracker = chapterTracker + 1;
	}
	$: {
		w = w;
		if (w >= maxWidth) {
			h = maxWidth * 7/11;
		} else if (w > 620) {
			h = (w-40) * 7/11;
		} else {
			h = (w-40) * 14/11;
		}
	}
</script>	
<svelte:window bind:innerWidth={w}/>
<div class="mainImage">
	{#if w > 640}
		<img src="assets/kimchi/universal/main-desktop.jpg"/>
	{:else}
		<img src="assets/kimchi/universal/main-mobile.jpg"/>
	{/if}
	<button on:click={start} on:keydown={start}>Start</button>
</div>
	

	
<style>
	.mainImage {
		position: absolute;
		left: 0;
		top: 0;
		width: 100%;
		height: 100%;
	}
	.mainImage img {
		position: absolute;
		left: 0;
		top: 0;
		width: 100%;
		height: 100%;
	}
	.mainImage button {
		z-index: 9999;
		position: absolute;
		left: 50%;
		transform: translateX(-50%);
		bottom: 40%;
		border: 2px solid #000;
		background: white;
		font-weight: bold;
		font-size: 20px;
	}
	.mainImage button:hover {
		background: #eee;
	}
</style>
