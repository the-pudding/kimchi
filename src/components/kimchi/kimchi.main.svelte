<script>
	import { fade } from 'svelte/transition';
	import Intro from "$components/kimchi/kimchi.intro.svelte";
	import Scene from "$components/kimchi/kimchi.scene.svelte";
	import CutScene from "$components/kimchi/kimchi.cutscene.svelte";
	import Sound from "$components/kimchi/kimchi.sound.svelte";
	export let copy;
	
	let cutsceneText = copy.cutscenes[0];
	let currentChapter = 0;
	let sceneHeight = 700;
	let windowWidth = 400;
	let maxWidth = 1200;
	let cutSceneMode = true;
	
	// 1100 x 700
	$: {
		currentChapter = currentChapter;
		windowWidth = windowWidth;
		if (windowWidth >= maxWidth + 20) {
			sceneHeight = maxWidth * 7/11;
		} else if (windowWidth > 620) {
			sceneHeight = (windowWidth-20) * 7/11;
		} else {
			sceneHeight = (windowWidth-20) * 14/11;
		}
		sceneHeight = sceneHeight;
		cutSceneMode = cutSceneMode;
	}
	
</script>
<svelte:window bind:innerWidth={windowWidth}/>

<div class="scene" style="height:{sceneHeight}px;">
	<!-- 1996 -->
	{#if currentChapter == 0}
	<Intro chapter="0" bind:chapterTracker={currentChapter} bind:introShown={cutSceneMode} cutsceneText={cutsceneText} w={windowWidth} h={sceneHeight} maxWidth={maxWidth}/>
	{/if}
	<!-- 1996 -->
	{#if currentChapter == 1}
	<Scene chapter="1" year="1996" hoverHints={copy.scene1} bind:introShown={cutSceneMode} bind:chapterTracker={currentChapter} cutsceneText={cutsceneText} />
	{/if}
	
	<!-- 2006 -->
	{#if currentChapter == 3}
	<Scene chapter="3" year="2006" hoverHints={copy.scene3} bind:introShown={cutSceneMode} bind:chapterTracker={currentChapter} cutsceneText={cutsceneText} />
	{/if}	

	<!-- 2016 -->
	{#if currentChapter == 5}
	<Scene chapter="5" year="2016" hoverHints={copy.scene5} bind:introShown={cutSceneMode} bind:chapterTracker={currentChapter} cutsceneText={cutsceneText} />
	{/if}	

	<!-- 2022 -->
	{#if currentChapter == 7}
	<Scene chapter="7" year="2022" hoverHints={copy.scene7} bind:chapterTracker={currentChapter} cutsceneText={cutsceneText} />
	{/if}	
	
	<!-- Cut scenes -->
	{#if [2,4,6,8].includes(currentChapter)}
		<CutScene chapter={currentChapter} cutsceneText={cutsceneText} bind:chapterTracker={currentChapter} w={windowWidth} h={sceneHeight} maxWidth={maxWidth}/>
	{/if}
	<Sound bind:chapter={currentChapter} bind:mode={cutSceneMode}/>
</div>

<style>
.scene {
	max-width: 1200px;
	width: 100%;
	margin: 0 auto 50px;
	overflow: hidden;	
	position: relative;
	box-sizing: border-box;
	border: 2px solid #000;
}
</style>
