<script>
	import { fade } from 'svelte/transition';
	export let chapter;
	export let chapterTracker = Number(chapter);

	function nextChapter() {
		chapterTracker = chapterTracker + 1;
	}
	
	$: {
		chapterTracker = chapterTracker;
	}
</script>

<div class="scene">
	<div class="text">Psychodelic visual representation of the taste of this chapter's kimchi...</div>
	<div class="button" on:click={nextChapter}>
	{#if chapterTracker < 8}
		More kimchi
	{:else}
		End
	{/if}
	</div>
</div>

<style>
.text {
	color: white;
	font-size: 20px;
	text-align: center;
	position: absolute;
	top: 40%;
	width: 100%;
}
.scene {
	background: red;
}
</style>
