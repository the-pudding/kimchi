<script>
	import { fade } from 'svelte/transition';
	import Scene from "$components/kimchi/kimchi.scene.svelte";
	import CutScene from "$components/kimchi/kimchi.cutscene.svelte";
	export let copy;
	let currentChapter = 1;
	let sceneHeight = 700;
	let windowWidth = 400;
	let maxWidth = 1500;
	
	// 1100 x 700
	$: {
		currentChapter = currentChapter;
		windowWidth = windowWidth;
		if (windowWidth >= maxWidth) {
			sceneHeight = maxWidth * 7/11;
		} else if (windowWidth > 570) {
			sceneHeight = (windowWidth-40) * 7/11;
		} else {
			sceneHeight = (windowWidth-40) * 14/11;
		}
		sceneHeight = sceneHeight;
	}
	
</script>
<svelte:window bind:innerWidth={windowWidth}/>
<div class="scene" style="height:{sceneHeight}px;">
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
.scene {
	max-width: 1500px;
	width: 100%;
	margin: 0 auto 50px;
	overflow: hidden;	
	position: relative;
	box-sizing: border-box;
	border: 2px solid #000;
}
</style>
