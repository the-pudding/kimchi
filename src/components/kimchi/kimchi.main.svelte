<script>
	import Scene from "$components/kimchi/kimchi.scene.svelte";
	import CutScene from "$components/kimchi/kimchi.cutscene.svelte";
	let currentChapter = 1;
	let hoverHints = {
		"scene1": [
			{"id": 0, "type": "smallImage", "hed": "", "x": 26, "y": 58, "words": "♥ ♥ ♥ ♥", "image": "grandma.png", "clicked": false},
			{"id": 1, "type": "smallImage", "hed": "Grandma:", "x": 28, "y": 85, "words": "No. Boys, kimchi, bad.", "image": "grandma.png", "clicked": false},
			{"id": 2, "type": "smallImage", "hed": "Grandma:", "x": 18.4, "y": 88, "words": "Go play.", "image": "grandma.png", "clicked": false},
			{"id": 3, "type": "smallImage", "hed": "Grandma:", "x": 7.5, "y": 88, "words": "No. Boys, kimchi, bad.", "image": "grandma.png", "clicked": false},
			{"id": 4, "type": "smallImage", "hed": "Grandma:", "x": 39, "y": 88, "words": "No. Boys, kimchi, bad.", "image": "grandma.png", "clicked": false},
			{"id": 5, "type": "smallImage", "hed": "Grandma:", "x": 47.8, "y": 84.5, "words": "No. Boys, kimchi, bad.", "image": "grandma.png", "clicked": false},
			{"id": 6, "type": "bigImage", "hed": "Koreas were poor and hungry:", "x": 40.8, "y": 34.5, "words": "Chart", "image": "chart.jpg", "clicked": false},
			{"id": 7, "type": "bigImage", "hed": "Korean immigration to US and when we came:", "x": 39.2, "y": 21.5, "words": "Chart", "image": "chart.jpg", "clicked": false},
			{"id": 8, "type": "bigImage", "hed": "Photo with kimchi:", "x": 59.2, "y": 15.5, "words": "Family photo", "image": "chart.jpg", "clicked": false},
			{"id": 9, "type": "bigImage", "hed": "Photo with kimchi:", "x": 75.2, "y": 14.5, "words": "Family photo", "image": "chart.jpg", "clicked": false},
			{"id": 10, "type": "bigImage", "hed": "Photo with kimchi:", "x": 69.2, "y": 52.5, "words": "Family photo", "image": "chart.jpg", "clicked": false},
			{"id": 11, "type": "bigImage", "hed": "Kimjang book:", "x": 53.7, "y": 53.5, "words": "History", "image": "chart.jpg", "clicked": false}
		],
		"scene3": [
			{"id": 0, "type": "smallImage", "hed": "", "x": 26, "y": 60, "words": "♥ ♥ ♥ ♥", "image": "grandma.png", "clicked": false}
		],
		"scene5": [
			{"id": 0, "type": "smallImage", "hed": "", "x": 26, "y": 60, "words": "♥ ♥ ♥ ♥", "image": "grandma.png", "clicked": false}
		],
		"scene7": [
			{"id": 0, "type": "smallImage", "hed": "", "x": 26, "y": 60, "words": "♥ ♥ ♥ ♥", "image": "grandma.png", "clicked": false}
		]
	}
	$: {
		currentChapter = currentChapter;
		console.log(currentChapter);
	}
</script>

<div class="scene">
	<!-- 1996 -->
	{#if currentChapter == 1}
	<Scene chapter="1" year="1996" hoverHints={hoverHints.scene1} bind:chapterTracker={currentChapter}/>
	{/if}
	{#if currentChapter == 2}
	<CutScene chapter="2" bind:chapterTracker={currentChapter}/>
	{/if}
	
	<!-- 2006 -->
	{#if currentChapter == 3}
	<Scene chapter="3" year="2006" hoverHints={hoverHints.scene3} bind:chapterTracker={currentChapter}/>
	{/if}	
	{#if currentChapter == 4}
	<CutScene chapter="4" bind:chapterTracker={currentChapter}/>
	{/if}	
	
	<!-- 2016 -->
	{#if currentChapter == 5}
	<Scene chapter="5" year="2016" hoverHints={hoverHints.scene5} bind:chapterTracker={currentChapter}/>
	{/if}	
	{#if currentChapter == 6}
	<CutScene chapter="6" bind:chapterTracker={currentChapter}/>
	{/if}
	
	<!-- 2022 -->
	{#if currentChapter == 7}
	<Scene chapter="7" year="2022" hoverHints={hoverHints.scene7} bind:chapterTracker={currentChapter}/>
	{/if}	
	{#if currentChapter == 8}
	<CutScene chapter="8" bind:chapterTracker={currentChapter}/>
	{/if}
</div>

<style>

</style>
