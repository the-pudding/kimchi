<script>
	import { fade } from 'svelte/transition';
	import { swipe } from 'svelte-gestures';
	export let chapter;
	export let chapterTracker = Number(chapter);
	export let w;
	export let h;
	export let maxWidth;
	export let soundon;
	export let eventClicked;
	
	function start() {
		chapterTracker = chapterTracker + 1;
		eventClicked = 1;
	}
	
	function soundtoggle() {
		soundon = !soundon;
	}
	
	function credits() {
		chapterTracker = 13;
		eventClicked = 1;
	}
	
	function onKeyDown(e) {
		if (e.keyCode == 13 || e.keyCode == 49) {
			 start();
		 }
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
<svelte:window bind:innerWidth={w} on:keydown|preventDefault={onKeyDown}/>
	<div class="mainImage">
		{#if w > 640}
			<img alt="a marker drawing of kimchi leaves" src="assets/kimchi/universal/main-desktop.jpg"/>
		{:else}
			<img alt="a marker drawing of kimchi leaves" src="assets/kimchi/universal/main-mobile.jpg"/>
		{/if}
		<div class="buttonContainer">
		<button class="start" on:click={start} on:keydown={start}>
			Start
			<div class="button_hint">Press <span>Enter</span></div>
		</button>
				<button class="sound" on:click={soundtoggle} on:keydown={soundtoggle}>
			{#if soundon}
			Sound: on
			{:else}
			Sound: off
			{/if}
			<div class="button_hint">Press <span>Spacebar</span></div>
		</button>
		
		<button class="credits" on:click={credits} on:keydown={credits}>Credits</button>
	</div>
		
	</div>

	
<style>
	.mainImage {
		position: absolute;
		left: 0;
		top: 0;
		width: 100%;
		height: 100%;
		user-select: none;
	}
	.mainImage img {
		position: absolute;
		left: 0;
		top: 0;
		width: 100%;
		height: 100%;
	}
	.buttonContainer {
		z-index: 9999;
		position: absolute;
		left: 50%;
		transform: translateX(-50%);
		top: 50%;
		user-select: none;
	}
	.mainImage button {
		display:block;
		width: 150px;
		border: 2px solid #000;
		background: white;
		font-weight: bold;
		text-transform: uppercase;
		font-size: 20px;
		margin: 0px;
		margin-bottom: 10px;
		box-shadow:4px 4px 0px #000;
		color: black !important;
	}
	.mainImage button:active {
		box-shadow: 0px 0px 0px #000;
		transform: translate(4px, 4px);
	} 
	.mainImage button:hover {
		background: #eee;
	}
	@media screen and (max-width: 620px) {
		.sound {
			display: none !important;
		}
	}
</style>
