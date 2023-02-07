<script>
	import { fade } from 'svelte/transition';
	
	// determines which scene to show
	export let chapter;
	export let chapterTracker = Number(chapter);
	export let hoverHints;
	export let year;
	
	let modalShown = false;
	let allClicked = false;
	let hed, words, type, image;
	
	
	function showModal(e) {
		hed = e.target.getAttribute("hed");
		words = e.target.getAttribute("words");
		type = e.target.getAttribute("type");
		image = e.target.getAttribute("image");
		e.target.classList.add("clicked");
		e.target.classList.remove("pulse");
		let num = Number(e.target.getAttribute("num"));
		hoverHints[num].clicked = true;
		modalShown = true;
	}
	
	function closeModal() {
		modalShown = false;
		checkAllClicked();
	}
	
	// Checking if all modals have been clicked
	function checkAllClicked() {
		allClicked = true;
		hoverHints.forEach(function(d) {
			if (!d.clicked) { allClicked = false; }
		})
	}
	
	function nextChapter() {
		chapterTracker = chapterTracker + 1;
	}
	
	$: {
		modalShown = modalShown;
		hed = hed;
		words = words;
		type = type;
		image = image;
		allClicked = allClicked;
		chapterTracker = chapterTracker;
	}
</script>

	<div class="sceneInside">
		<img class="sceneImage" alt="scene of grandma making kimchi" src="assets/kimchi/{chapterTracker}.png" on:click={closeModal} />
		<div class="yearLabel">{year}</div>
		{#each hoverHints as hint}
			<div class="hoverHint pulse" num={hint.id} hed={hint.hed} words={hint.words} type={hint.type} image={hint.image} style="left:{hint.x}%; top:{hint.y}%;" on:click={showModal}></div>
		{/each}
		{#if modalShown}
			<div class="modal {type}" transition:fade="{{duration: 200}}" on:click={closeModal}>
				<div class="closeModal" on:click={closeModal}>x</div>
				<div class="modalWords">
					<span class="hed">{hed}</span>
					<span>{words}</span>
				</div>
				<img src="assets/kimchi/{image}" />
			</div>
		{/if}
		<!-- {#if allClicked} -->
			<div class="button" on:click={nextChapter}>Eat kimchi</div>
		<!-- {/if} -->
		<div class="panButton">PAN</div>
	</div>

<style>
.hoverHint {
	position: absolute;
	width: 40px;
	height: 40px;
	margin: -20px 0 0 -20px;
	border-radius: 50%;
	background: rgba(240,130,40,0.5);
	border: 2px solid orange;
	cursor: pointer;
}
.hoverHint.clicked {
	border: 2px solid #777;
	background: rgba(0,0,0,0.2);
}
.hoverHint:hover {
	background: rgba(240,130,40,0.8);
}
.hoverHint.clicked:hover {
	background: rgba(0,0,0,0.2);
} 
/* MODALS */
.modal {
	font-size: 21px;
	position: absolute;
	left: 20px;
	bottom: 20px;
	background: white;
	color: black;
	border: 3px solid #000;
	box-shadow: 3px 3px 0px 3px #000;
	padding: 20px;
	width: calc(100% - 40px);
	max-height: 100%;
}
@media screen and (max-width: 570px) {
	.modal {
		width: calc(50% - 40px);
	}
}
.modal .modalWords { pointer-events: none; }
.modal .modalWords span {
	font-weight: 100;
	opacity: 0.6;
	pointer-events: none;
}
.modal .modalWords .hed {
	font-weight: 500;
	opacity: 1;
}
.closeModal {
	position: absolute;
	right: 20px;
	top: 20px;
	cursor: pointer;
}
.pulse {
  animation: pulse-animation 2s infinite;
}

@keyframes pulse-animation {
  0% {
	box-shadow: 0 0 0 0px rgba(0, 0, 0, 0.2);
  }
  100% {
	box-shadow: 0 0 0 20px rgba(0, 0, 0, 0);
  }
}

/* WORDS ONLY */
.modal.words img {
	display: none;
}

/* SMALL IMAGE */
.modal.smallImage img {
	max-width: 120px;
}
.modal.smallImage .modalWords {
	position: absolute;
	left: 160px;
	width: calc(100% - 180px);
}

/* BIG IMAGE */
.modal.bigImage img {
	/* width: 70%; */
	max-width: 600px;
	margin: 0 auto;
	max-height: 90%;
}
.modal.bigImage .modalWords {
	position: absolute;
	width: 80%;
	left: 50%;
	transform: translateX(-50%); 
	max-width: 600px;
	text-align: center;
}
</style>
