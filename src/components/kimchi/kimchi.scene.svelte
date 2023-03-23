<script>
	import { fade } from 'svelte/transition';
	import { swipe } from 'svelte-gestures';
	
	// determines which scene to show
	export let chapter;
	export let chapterTracker = Number(chapter);
	export let hoverHints;
	export let year;
	
	let sceneOffset = "rightSide";
	let modalShown = false;
	let allClicked = false;
	let hed, words, type, image, alt;
	let fullWidth = 2200/100;
	
	let direction;
	
	function swipeHandler(event) {
		if (event.detail.direction == "left") {
			sceneOffset = "leftSlide";
		} else {
			sceneOffset = "rightSide";
		}
	}
	  
	function showModal(e) {
		hed = e.target.getAttribute("hed");
		words = e.target.getAttribute("words");
		type = e.target.getAttribute("type");
		image = "scene" + chapter + "/" + e.target.getAttribute("image");
		alt = e.target.getAttribute("alt");
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
	
	let clickedPos = {x:0,y:0};
	function getPosition(e) {
		clickedPos.x = e.offsetX/e.srcElement.width*100;
		clickedPos.y = e.offsetY/e.srcElement.height*100;
	}
	
	$: {
		modalShown = modalShown;
		hed = hed;
		words = words;
		type = type;
		image = image;
		allClicked = allClicked;
		chapterTracker = chapterTracker;
		sceneOffset = sceneOffset;
	}
</script>	
	<div class="sceneInside {sceneOffset}" on:click={getPosition} on:keydown={getPosition}  transition:fade="{{duration: 500}}" use:swipe={{ timeframe: 300, minSwipeDistance: 100 }} on:swipe={swipeHandler} >
		<img class="sceneImage" alt="scene of grandma making kimchi" src="assets/kimchi/scene{chapter}/background.png" on:click={closeModal} on:keydown={closeModal} />
		
		{#each hoverHints as hint}
			{#if hint.width > 500}
				<img class="hoverHint pulse bigItem {hint.addclass}" alt="{hint.alt}" src="assets/kimchi/scene{chapter}/{hint.image}" image={hint.image} hed={hint.hed} words={hint.words} type={hint.type} style="width:{hint.width/fullWidth}%; left:{hint.x}%; top:{hint.y}%; pointer-events: {hint.notouch};" on:click={showModal} on:keydown={showModal}/>
			{:else}
				<img class="hoverHint pulse smallItem {hint.addclass}" alt="{hint.alt}" src="assets/kimchi/scene{chapter}/{hint.image}" image={hint.image} hed={hint.hed} words={hint.words} type={hint.type} style="width:{hint.width/fullWidth}%; left:{hint.x}%; top:{hint.y}%; pointer-events: {hint.notouch};" on:click={showModal} on:keydown={showModal}/>
			{/if}
		{/each}
	</div>
	<div class="yearLabel">{year}</div>
	<div class="panButton">PAN</div>
	<!-- {#if allClicked} -->
		<button class="button" on:click={nextChapter} on:keydown={nextChapter}>Eat kimchi</button>
	<!-- {/if} -->
	{#if modalShown}
		<div class="modal {type}" transition:fade="{{duration: 200}}" on:click={closeModal} on:keydown={closeModal}>
			<button class="closeModal" on:click={closeModal} on:keydown={closeModal}>x</button>
			<!-- {#if type == "bigImage"}
			<div class="modalWords">
				<span class="hed">{hed}</span>
				<span>{words}</span>
			</div>
			<img alt="{alt}" src="assets/kimchi/{image}" />
			{:else} -->
			<div class="modalWords">
				<img alt="{alt}" src="assets/kimchi/{image}" />
				<span class="hed">{hed}</span>
				<span>{words}</span>
			</div>
			<!-- {/if} -->
		</div>
	{/if}
<style>
.sceneImage {
	/* opacity: 0.3; */
}
.sceneInside {
	background: black;
}
.debugger {
	position: fixed;
	right: 10px;
	bottom: 10px;
	color: black;
}
.hoverHint {
	position: absolute;
	cursor: pointer;
	background: none;
	/* pointer-events: none; */
}
.hoverHint.clicked {
	/* border: 2px solid #777; */
	/* background: rgba(0,0,0,0.2); */
}
.bigItem:hover {
	transform: scale(1.008);
}
.smallItem:hover {
	margin-top: -2px;
}
.sidehover.smallItem:hover {
	margin-top: 0px;
	margin-left: -4px;
}
.hoverHint.clicked:hover {
	/* background: rgba(0,0,0,0.2); */
} 
.updown {
	animation: updown-animation 3s infinite;
}
.updown2 {
	animation: updown-animation2 3s infinite;
}
.snoozing {
	animation: snoozing-animation 2.5s infinite;
}
@keyframes updown-animation {
  0% {
	height: 15%;
  }
 
	20% {
	  height: 17%;
	}
	
	40% {
	  height: 15%;
	}
	
	60% {
	  height: 20%;
	}
	80% {
	  height: 21%;
	}
  100% {
	height: 15%;
  }
}
@keyframes updown-animation2 {
  0% {
	margin-top: 0%;
	height: 50%;
  }
  20% {
	  margin-top: -0.3%;
	height: 50.3%;
  }
  40% {
	  margin-top: 0.3%;
	  height: 49.7%;
	}
	60% {
		margin-top: -0.3%;
	  height: 50.3%;
	}
	80% {
		margin-top: 0.3%;
	  height: 49.7%;
	}
  100% {
	  margin-top: 0%;
	  height: 50%;
	}
}
@keyframes snoozing-animation {
	0% {
		margin-top: 0%;
		height: 13%;
	}

	40% {
		margin-top: -0.4%;
		height: 13.4%;
	}

	100% {
		height: 13%;
		margin-top: 0%;
	}
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
	max-height: 95%;
	z-index: 5;
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
  /* animation: pulse-animation 2s infinite; */
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
/* .modal.smallImage img {
	max-width: 120px;
	float: left;
	margin-right: 10px;
}
.modal.smallImage .modalWords {
	position: relative;
	left: 0px;
	width: calc(100% - 180px);
} */

/* BIG IMAGE */
/* .modal.bigImage img {
	max-width: 600px;
	width: 100%;
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
} */

.modal img {
	max-width: 120px;
	float: left;
	margin-right: 10px;
}
.modal .modalWords {
	position: relative;
	left: 0px;
	width: calc(100% - 180px);
}
</style>
