<script>
	import { fade } from 'svelte/transition';
	export let chapter;
	export let chapterTracker = Number(chapter);

	let cutsceneText = {
		"scene1": {"words":"","color":""},
		"scene2": {
			"words": "The kimchi flavor reaches deep into my soul. Psychodelic visual representation of Grandma's kimchi. It's beautiful but chaotic.",
			"color": "#fc03b6"
		},
		"scene3": {"words":"","color":""},
		"scene4": {
			"words": "The kimchi has no soul, and tastes like the sum of its ingredients. A dull, gray representation of the kimchi flavor. It's both chaotic and dull. Disappointing.",
			"color": "#aaa"
		},
		"scene5": {"words":"","color":""},
		"scene6": {
			"words": "The kimchi is delicious, albeit not Grandma's kimchi. A very ordered and red-only representation of the kimchi. It's beautiful, but also somewhat foreign.",
			"color": "red"
		},
		"scene7": {"words":"","color":""},
		"scene8": {
			"words": "The kimchi is a mix of Grandma's kimchi, but I've reconstructed it for myself. A very ordered representation of kimchi. It's beautiful, and it's my kimchi.",
			"color": "#fc03b6"
		}
	}

	function nextChapter() {
		chapterTracker = chapterTracker + 1;
	}
	
	function end() {
		chapterTracker = 1;
	}
	
	$: {
		chapterTracker = chapterTracker;
	}
</script>

<div class="sceneInside cutscene" style="background: {cutsceneText["scene" + chapterTracker].color}">
	<img class="sceneImage" alt="solid color" src="assets/kimchi/cutscene.png" />
	<div class="text">
		{cutsceneText["scene" + chapterTracker].words}
	</div>
	
	{#if chapterTracker < 8}
		<div class="button" on:click={nextChapter}>
			More kimchi
		</div>
	{:else}
		<div class="button" on:click={end}>
			Restart
		</div>
	{/if}

</div>

<style>
.sceneImage {
	visibility: hidden;
}
.text {
	color: white;
	font-size: 20px;
	text-align: center;
	position: absolute;
	top: 40%;
	width: 100%;
}
.sceneInside {
	background: #fc03b6;
}
</style>
