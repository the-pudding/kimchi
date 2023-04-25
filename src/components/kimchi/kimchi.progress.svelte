<script>
	export let chapter;
	export let eventClicked;
	// let chapters = ["1996","2005","2015","2022"];
	// let chapters = [0,1,2,3,4,5,6,7,8,9,10,11,12,13];
	let chapters = [0,1,4,7,10];
	let chapterTitles = {
		0: "Intro",
		1: "1996",
		4: "2005",
		7: "2015",
		10: "2022"
	}
	let progressWidth = 0;
	function chapterClick(e) {
	 	chapter = Number(e.target.getAttribute("ch"));
		eventClicked = 3;
	}
	
	function getProgressWidth(ch) {
		if (chapters.indexOf(ch) != -1) {
			return (chapters.indexOf(ch)+1)/chapters.length*100;
		} else {
			let chapterProgress = 0;
			for (let i = 0; i < chapters.length; i++) {
				if (Number(ch) > chapters[i]) {
					chapterProgress = chapters.indexOf(chapters[i]) + 1;
				}
			}
			return (chapterProgress)/chapters.length*100;
		}
	}
	
	$: {
		progressWidth = getProgressWidth(chapter);
		chapter = chapter;
	}
</script>	
	
<div class="progressContainer">
	<div class="progressBackground" style="width: {progressWidth}%;"></div>
	{#each chapters as ch}
		{#if chapters.includes(ch)}
			{#if ch <= chapter}
			<button class="chapterItem selected" ch={ch} on:keydown={chapterClick} on:click={chapterClick}>
				{chapterTitles[ch]}
			</button>
			{:else}
			<button class="chapterItem" ch={ch} on:keydown={chapterClick} on:click={chapterClick}>
				{chapterTitles[ch]}
			</button>
			{/if}
		{/if}
	{/each}
</div>
	
<style>
	.progressContainer {
		position: relative;
		font-family:  var(--sans);
		width: calc(100% + 4px);
		text-align: center;
		background: black;
		border-bottom: 2px solid black;
		/* position: absolute; */
		top: 100%;
		user-select: none;
		padding: 0 !important;
		margin: 0 auto 100px;
		max-width: 1204px;
	}
	button.chapterItem {
		position: relative;
		display: inline-block;
		/* padding: 0 5px; */
		cursor: pointer;
		color: rgba(200,200,200,0.4);
		width: 20%;
		/* max-width: 80px; */
		z-index: 2;
		background: none;
		padding: 0;
		border-right: rgba(200,200,200,0.4) 1px solid;
	}
	button.chapterItem:hover {
		color: white;
	}
	button.chapterItem:last-child {
		border: none;
	}
	.chapterItem.selected {
		color: white;
		font-weight: bold;
	}
	.progressContainer .progressBackground {
		position: absolute;
		left: 0;
		bottom: 0;
		height: 100%;
		background: #444;
		z-index: 0;
		transition: all 300ms cubic-bezier(0.420, 0.000, 0.580, 1.000); /* ease-in-out */
		transition-timing-function: cubic-bezier(0.420, 0.000, 0.580, 1.000); /* ease-in-out */
	}
</style>
