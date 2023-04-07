<script>
	import { fade } from 'svelte/transition';
	import Intro from "$components/kimchi/kimchi.intro.svelte";
	import Scene from "$components/kimchi/kimchi.scene.svelte";
	import CutScene from "$components/kimchi/kimchi.cutscene.svelte";
	import PreScene from "$components/kimchi/kimchi.prescene.svelte";
	import Sound from "$components/kimchi/kimchi.sound.svelte";
	export let copy;
	let loaded = false;
	let currentChapter = 0;
	let sceneHeight = 700;
	let windowWidth = 400;
	let maxWidth = 1200;
	let cutSceneMode = true;
	let soundon = false;
	setInterval(function() {
		windowWidth = windowWidth;
		if (windowWidth >= maxWidth) {
			sceneHeight = maxWidth * 7/11;
		} else if (windowWidth > 620) {
			sceneHeight = (windowWidth+4) * 7/11;
		} else {
			sceneHeight = (windowWidth+4) * 14/11;
		}
		loaded = true;
	},100);
	
	let scrolled = false;
	let scrolledY = false;
	let scene;
	function parseScroll() {
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
<svelte:window bind:innerWidth={windowWidth} on:scroll={parseScrollY}/>
{#if loaded}
<Sound bind:chapter={currentChapter} bind:mode={cutSceneMode} bind:soundon={soundon}/>
<div class="scene" bind:this={scene}  style="height:{sceneHeight}px;" in:fade on:scroll={parseScroll} on:mousemove={parseScroll}>
	<!-- Intro -->
	{#if currentChapter == 0}
	<Intro chapter="0" bind:chapterTracker={currentChapter} w={windowWidth} h={sceneHeight} maxWidth={maxWidth} bind:soundon={soundon}/>
	{/if}
	
	<!-- Pre scenes -->
	{#if [1,4,7,10,13].includes(currentChapter)}
		<PreScene chapter={currentChapter} cutsceneText={copy["scene"+currentChapter]} bind:chapterTracker={currentChapter} w={windowWidth} h={sceneHeight} maxWidth={maxWidth}/>
	{/if}
	
	<!-- Main scenes -->
	{#if [2,5,8,11].includes(currentChapter)}
		<Scene chapter={currentChapter} hoverHints={copy["scene"+currentChapter]} bind:chapterTracker={currentChapter} bind:scrolled={scrolled} bind:scrolledY={scrolledY} />
	{/if}
		
	<!-- Cut scenes -->
	{#if [3,6,9,12].includes(currentChapter)}
		<CutScene chapter={currentChapter} cutsceneText={copy["scene"+currentChapter]} bind:chapterTracker={currentChapter} w={windowWidth} h={sceneHeight} maxWidth={maxWidth}/>
	{/if}
</div>
{/if}
<style>
.scene {
	max-width: 1200px;
	width: 100%;
	margin: 0 auto 50px;
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
