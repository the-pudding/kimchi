<script>
	import { fade } from 'svelte/transition';
	import Scene from "$components/kimchi/kimchi.scene.svelte";
	import CutScene from "$components/kimchi/kimchi.cutscene.svelte";
	export let copy;	
	let currentChapter = 1;

	$: {
		currentChapter = currentChapter;
	}
</script>

<div class="scene">
	<!-- 1996 -->
	{#if currentChapter == 1}
	<Scene chapter="1" year="1996" hoverHints={copy.scene1} bind:chapterTracker={currentChapter}/>
	{/if}
	{#if currentChapter == 2}
	<CutScene chapter="2" bind:chapterTracker={currentChapter}/>
	{/if}
	
	<!-- 2006 -->
	{#if currentChapter == 3}
	<Scene chapter="3" year="2006" hoverHints={copy.scene3} bind:chapterTracker={currentChapter}/>
	{/if}	
	{#if currentChapter == 4}
	<CutScene chapter="4" bind:chapterTracker={currentChapter}/>
	{/if}	
	
	<!-- 2016 -->
	{#if currentChapter == 5}
	<Scene chapter="5" year="2016" hoverHints={copy.scene5} bind:chapterTracker={currentChapter}/>
	{/if}	
	{#if currentChapter == 6}
	<CutScene chapter="6" bind:chapterTracker={currentChapter}/>
	{/if}
	
	<!-- 2022 -->
	{#if currentChapter == 7}
	<Scene chapter="7" year="2022" hoverHints={copy.scene7} bind:chapterTracker={currentChapter}/>
	{/if}	
	{#if currentChapter == 8}
	<CutScene chapter="8" bind:chapterTracker={currentChapter}/>
	{/if}
</div>

<style>

</style>
