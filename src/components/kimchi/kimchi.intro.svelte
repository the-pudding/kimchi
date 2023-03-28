<script>
	import { fade } from 'svelte/transition';
	import { swipe } from 'svelte-gestures';
	export let chapter;
	export let chapterTracker = Number(chapter);
	
	function start() {
		chapterTracker = chapterTracker + 1;
	}
</script>	

<h1>Kimchi</h1>
<button on:click={start} on:keydown={start}>Start</button>
	
<style>
	
</style>
