<script>
	import { fade } from 'svelte/transition';
	import Intro from "$components/kimchi/kimchi.intro.svelte";
	import Scene from "$components/kimchi/kimchi.scene.svelte";
	import CutScene from "$components/kimchi/kimchi.cutscene.svelte";
	import PreScene from "$components/kimchi/kimchi.prescene.svelte";
	import Sound from "$components/kimchi/kimchi.sound.svelte";
	import Progress from "$components/kimchi/kimchi.progress.svelte";
	export let copy;
	let loaded = false;
	let currentChapter = 11;
	let sceneHeight = 700;
	let windowWidth = 400;
	let maxWidth = 1200;
	let cutSceneMode = true;
	let soundon = false;
	let eventClicked = 0;
	let insideWidth;
	setInterval(function() {
		windowWidth = insideWidth;
		if (windowWidth >= maxWidth) {
			sceneHeight = maxWidth * 7/11;
		} else if (windowWidth > 620) {
			sceneHeight = (windowWidth) * 7/11;
		} else {
			sceneHeight = (windowWidth) * 14/11;
		}
		loaded = true;
	},100);
	
	let scrolled = false;
	let scrolledY = false;
	let scrollAmount = 0;
	let scene;
	function parseScroll() {
		scrollAmount = scene.scrollLeft;
		if (scene.scrollLeft > 10) {
			scrolled = true;
		}
		
	}
	
	function parseScrollY() {
		scrolledY = true;
	}
	// 1100 x 700
	$: {
		currentChapter = currentChapter;
		windowWidth = windowWidth;
		sceneHeight = sceneHeight;
		cutSceneMode = cutSceneMode;
	}
	
</script>
<svelte:window on:scroll={parseScrollY}/>
{#if loaded}
<Sound bind:chapter={currentChapter} bind:eventClicked={eventClicked} bind:mode={cutSceneMode} bind:soundon={soundon}/>
<div class="scene" bind:this={scene} bind:clientWidth={insideWidth}  style="height:{sceneHeight}px;" on:scroll={parseScroll} on:mousemove={parseScroll}>
	<!-- Intro -->
	{#if currentChapter == 0}
		{#key currentChapter}
		<Intro chapter="0" bind:eventClicked={eventClicked} bind:chapterTracker={currentChapter} w={windowWidth} h={sceneHeight} maxWidth={maxWidth} bind:soundon={soundon}/>
		{/key}
	{:else if [1,4,7,10,13].includes(currentChapter)}
		{#key currentChapter}
		<PreScene bind:chapter={currentChapter} bind:eventClicked={eventClicked} cutsceneText={copy["scene"+currentChapter]} bind:chapterTracker={currentChapter} w={windowWidth} h={sceneHeight} maxWidth={maxWidth}/>
		{/key}
	{:else if [2,5,8,11].includes(currentChapter)}
		{#key currentChapter}
		<Scene chapter={currentChapter} bind:eventClicked={eventClicked} hoverHints={copy["scene"+currentChapter]} bind:chapterTracker={currentChapter} bind:scrolled={scrolled} bind:scrolledY={scrolledY} bind:scrollAmount={scrollAmount}/>
		{/key}
	{:else if [3,6,9,12].includes(currentChapter)}
		{#key currentChapter}
		<CutScene chapter={currentChapter} bind:eventClicked={eventClicked} cutsceneText={copy["scene"+currentChapter]} bind:chapterTracker={currentChapter} w={windowWidth} h={sceneHeight} maxWidth={maxWidth}/>
		{/key}	
	{/if}
</div>
<Progress bind:eventClicked={eventClicked} bind:chapter={currentChapter}/>
{/if}
<style>
.scene {
	max-width: 1200px;
	width: 100%;
	margin: 0 auto 0px;
	overflow-x: hidden;
	overscroll-behavior-x: none;
	position: relative;
	box-sizing: border-box;
	border: 2px solid #000;
	background: black;
	box-sizing: content-box; 
	scrollbar-width: none; 
	scroll-snap-stop: always;
}
.scene::-webkit-scrollbar { 
	display: none;  /* Safari and Chrome */
}
@media screen and (max-width: 620px) {
	.scene {
		overflow-x: scroll;
	}
}
</style>
