<script>
	import Typewriter from 'svelte-typewriter';
	import { fade } from 'svelte/transition';
	import { swipe } from 'svelte-gestures';
	
	// determines which scene to show
	export let chapter;
	export let chapterTracker = Number(chapter);
	export let hoverHints;
	let cutsceneStage = 0;
	let delayNumber = 800;
	let sceneOffset = "rightSide";
	let displayMode = "display";
	let modalShown = false;
	let allClicked = false;
	let readyForNext = false;
	let hed, words, type, image, alt;
	let fullWidth = 2200/100;
	let bounce = "bounce";
	let swipeInteracted = false;
	export let scrolled;
	export let scrolledY;
	  
	let selectedHint = null;
	let nextHint = null;
	let hintResponse = null;
	function showModal(e) {
		hed = e.target.getAttribute("hed");
		words = e.target.getAttribute("words");
		type = e.target.getAttribute("type");
		image = "scene" + chapter + "/" + e.target.getAttribute("image");
		alt = e.target.getAttribute("alt");
		selectedHint = Number(e.target.getAttribute("num"));
		hoverHints[selectedHint].clicked = true;
		if (type == "bigImage") {
			scrolledY = false;
			modalShown = true;
		}
		// if the right hint is clicked, then show hearts from grandma
		if (selectedHint == nextHint && !allClicked) {
			responseVisibility = true;
			hintResponse = nextHintResponse;
		}
		// then in 2 second, hide the quotes
		setTimeout(function() {
			if (responseVisibility) {
				permaQuoteVisibility = false;
			}
			responseVisibility = false;
		}, 5000);
		
		/// then in 4 seconds, show the picture again
		setTimeout(function() {
			permaQuoteVisibility = true;
			if (allClicked) {
				readyForNext = true;
				bounce = "";
			}
		}, 8000);
		checkAllClicked();
	}
	
	function closeModal() {
		modalShown = false;
		selectedHint = null;
		checkAllClicked();
	}
	
	let nextHintResponse = null;
	let hintImage = null;
	let hintPrompt = null;
	let responseVisibility = false;
	let permaQuoteVisibility = true;
	// Checking if all modals have been clicked
	function checkAllClicked() {
		allClicked = true;
		hoverHints.forEach(function(d) {
			if (d.clicked == "false") {
				hintImage = d.image;
				hintPrompt = d.prompt;
				nextHintResponse = d.response;
				allClicked = false;
				nextHint = d.num;
			}
		});
		
		if (allClicked) {
			hintImage = "kimchi.png";
			nextHintResponse = ["Almost ready. Explore while you wait.","Kimchi coming soon. Look around!","You can try kimchi soon. Try clicking other things!","Kimchi is nearly done!","Kimchi is coming. Click around for a bit!","Give the kimchi a few more seconds.","Be patient! Kimchi coming soon."].randFromArray();
			hintPrompt = ["Kimchi is ready!","Kimchi ready! Click the button.","Let's eat kimchi!","Let's try some kimchi now.","You want to try some kimchi now?","Taste some kimchi!","Let's try the kimchi."].randFromArray();
			hintResponse = nextHintResponse;
		}
	}
	
	function nextChapter() {
		chapterTracker = chapterTracker + 1;
		displayMode = "none";
	}
	
	let clickedPos = {x:0,y:0};
	function getPosition(e) {
		clickedPos.x = e.offsetX/e.srcElement.width*100;
		clickedPos.y = e.offsetY/e.srcElement.height*100;
	}
	
	Array.prototype.randFromArray = function(){
	  return this[Math.floor(chapter/13*this.length)];
	}
	
	$: {
		cutsceneStage = cutsceneStage;
		if (chapterTracker == chapter) {
			displayMode = "block";
		}
		modalShown = modalShown;
		hed = hed;
		words = words;
		type = type;
		image = image;
		allClicked = allClicked;
		chapterTracker = chapterTracker;
		sceneOffset = sceneOffset;
		checkAllClicked();
	}
</script>
	<div class="sceneInside {sceneOffset}" style="display:{displayMode}" on:click={getPosition} on:keydown={getPosition} in:fade>
		<img class="sceneImage" alt="scene of grandma making kimchi" src="assets/kimchi/scene{chapter}/background.png" on:click={closeModal} on:keydown={closeModal} draggable="false"/>
		
		{#each hoverHints as hint}
			<div class="hintContainer {hint.addclass}" class:selected="{selectedHint === hint.num}" style="width:{hint.width/fullWidth}%; left:{hint.x}%; top:{hint.y}%; pointer-events: {hint.notouch};" on:click={showModal} on:keydown={showModal} hed={hint.hed} words={hint.words} type={hint.type} num={hint.num} image={hint.image}>
			{#if hint.image != undefined}
				{#if hint.width > 500}
					<img class="hoverHint bigItem" alt="{hint.alt}" src="assets/kimchi/scene{chapter}/{hint.image}"   draggable="false"/>
				{:else}
					<img class="hoverHint smallItem" alt="{hint.alt}" src="assets/kimchi/scene{chapter}/{hint.image}" draggable="false"/>
				{/if}
			{/if}
			{#if hint.addclass == "speaker" || hint.addclass == "updown2 speaker"}
				{#if permaQuoteVisibility}
				
				<div class="quotebox perma {bounce} {hint.xPos}" transition:fade>
					{#if responseVisibility}
						<span in:fade>{hintResponse}</span>
					{:else}
					<img src="assets/kimchi/scene{chapter}/{hintImage}" alt={hint.alt} in:fade/>
					{hintPrompt}
					{/if}
				</div>
				{/if}
			{:else if hint.type != "bigImage"}
				<div class="quotebox {hint.xPos} {hint.yPos}" transition:fade>
					{hint.words}
				</div>
			{/if}
			
		</div>
		{/each}
	</div>
	{#if readyForNext && [2,5,8,11].includes(chapter)}
	<div class="buttonWrapper bounce">
		<button class="button" on:click={nextChapter} on:keydown={nextChapter}>Eat kimchi</button>
	</div>
	{/if}
	{#if modalShown && !scrolledY}
		<div class="modal {type}" transition:fade="{{duration: 200}}" on:click={closeModal} on:keydown={closeModal}>
			{#if type == "bigImage"}
				<img class="mobileImage" alt="{alt}" src="assets/kimchi/mobile/{image}" draggable="false"/>
				<img class="desktopImage" alt="{alt}" src="assets/kimchi/desktop/{image}" draggable="false"/>
			{/if}
			<div class="closeModal">Tap to exit</div>
		</div>
	{/if}
	{#if !scrolled}
		<div class="swipeHint" out:fade></div>
	{/if}
<style>
	.sceneInside {
		background: black;
		font-family: var(--sans);
		user-select: none;
	}
	.debugger {
		position: fixed;
		right: 10px;
		bottom: 10px;
		color: black;
	}
	.hintContainer {
		position: absolute;
		cursor: pointer;
	}
	.hoverHint {
		/* position: absolute; */
		pointer-events: none;
		background: none;
		left: 0;
		top: 0;
		width: 100%;
		height: 100%;
		/* pointer-events: none; */
		-webkit-touch-callout: none;
		-webkit-user-select: none; 
	 	-khtml-user-select: none; 
	   	-moz-user-select: none; 
			-ms-user-select: none; 
				user-select: none;
	}
	.hintContainer:hover  {
		margin-top: -2px;
	}
	.hintContainer.sidehover:hover  {
		margin-top: 0px !important;
		margin-left: -4px !important;
	}
	
	.quotebox {
		background: rgba(235,204,253,1);
		font-size: 15px;
		position: absolute;
		bottom: calc(100% + 15px);
		left: 50%;
		width: 170px;
		color: #480073;
		padding: 5px 7px;
		z-index: 999;
		display: none;
		text-align: left;
		line-height: 17px;
		box-shadow: 0px 0px 20px #35283D;
	}
	.quotebox.top {
		bottom: auto;
		top: calc(100% + 15px);
	}
	.quotebox.bottom {
		bottom: calc(100% + 15px);
		top: auto;
	}
	.quotebox.left {
		left: 10px;
	}
	.quotebox.right {
		left: auto;
		right: 10px;
	}
	.hintContainer.selected .quotebox {
		display: block;
	}
	.hintContainer .quotebox.perma {
		display: block !important;
		background: white;
		color: black;
		width: 160px;
		padding: 9px;
		box-shadow: 0px 0px 45px #000;
		pointer-events: none;
	}
	.bounce {animation: bounce 3s infinite;}
	.hintContainer .quotebox.perma img {
	margin-right: 5px;
	}
	.quotebox.perma .response {
		display: none;
	}
	.quotebox .quoteText {
		display: inline-block;
		vertical-align: center;
	}
	.quotebox img {
		float: left;
		width: 40px;
		margin-right: 7px;
	}
	@media screen and (max-width: 620px) {
		.quotebox img {
			width: 30px;
			margin-right: 4px;
		}
	}
	.quotebox::before {
		content: "";
		position: absolute;
		top: 100%;
		left: calc(50% - 10px);
		right: 10px;
		bottom: 10px;
		width: 0;
		height: 0;
		border-style: solid;
		border-width: 10px 10px 0 10px;
		border-color: rgba(235,204,253,1) transparent transparent transparent;
	}
	.quotebox.top::before  {
		border-width: 0px 10px 10px 10px;
		border-color: transparent transparent rgba(235,204,253,1) transparent;
		bottom: 100%;
		top: auto;
	}
	.quotebox.left::before { right: auto; left: 10px; }
	.quotebox.right::before { left: auto; right: 10px; } 
	.hintContainer .quotebox.perma::before {
		border-color: white transparent transparent transparent;
	}
	
	/* animations */
	.updown {
		animation: updown-animation 3s infinite;
	}
	.updown2 {
		animation: updown-animation2 3s infinite;
	}
	.snoozing {
		animation: snoozing-animation 2.5s infinite;
	}
	@keyframes bounce {
	  0% {
		margin-bottom: 0px;
	  }
	 
		5% {
		  margin-bottom: 10px;
		}
		
		10% {
		  margin-bottom: 0px;
		}
		
		15% {
		  margin-bottom: 15px;
		}
		60% {
		  margin-bottom: 0px;
		}
	  100% {
		margin-bottom: 0px;
	  }
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
		cursor: pointer;
		user-select: none;
		font-size: 1.8vw;
		line-height: 2.7vw;
		position: absolute;
		left: 50%;
		bottom: 50%;
		/* transform: translate(-50%, -50%); */
		background: rgba(0,0,0,0.8);
		color: white;
		/* border: 2px solid #000; */
		padding: 15px 10px;
		width: calc(100% - 20px);
		max-height: 95%;
		z-index: 99999;
	}
	.modal.bigImage {
		left: 0px;
		bottom: 0px;
		width: 100%;
		height: 100% !important;
		padding: 0;
		margin: 0;
		max-height: none;
		z-index: 999;
		
	}
	.modal.bigImage img {
		width: 90%;
		left: 5%;
		top: 50%;
		position: absolute;
		transform: translate(0%, -50%);
		/* transform: rotate(1deg); */
	}
	.modal .mobileImage {
		display: none;
	}
	
	.closeModal {
		position: absolute;
		left: 50%;
		transform: translateX(-50%);
		bottom: calc(100% - 35px);
		color: white;
		font-family: "National 2 Web",sans-serif;
		font-size: 16px;
		z-index: 1000;
	}
	@media screen and (max-width: 880px) {
		.modal  {
			font-size: 17px;
			line-height: 22px;
		}
	}
	
	@media screen and (max-width: 620px) {
		.modal.bigImage img {
			width: 100%;
			left: 0%;
			top: 50%;
			position: absolute;
			transform: translate(0%, -50%);
			/* transform: rotate(1deg); */
		}
		.modal  {
			font-size: 16px;
			line-height: 20px;
			position: fixed;
		}
		.modal .mobileImage {
			display: block;
		}
		.modal .desktopImage {
			display: none;
		}
		.closeModal {
			position: absolute;
			left: 50%;
			transform: translateX(-50%);
			top: 30px;
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
	
	
	
	/* WORDS ONLY */
	.modal.words img {
		display: none;
	}
	
	
	/* BIG IMAGE */
	
	.modal.bigImage .modalWords {
		position: absolute;
		width: 80%;
		left: 50%;
		transform: translateX(-50%); 
		max-width: 600px;
		text-align: center;
	}
	
	.modal .modalWords {
		position: relative;
		left: 0px;
		width: 100%;
	}
	
	.swipeHint {
		position: absolute;
		left: 70%;
		bottom: 25%;
		width: 50px;
		height: 50px;
		transform: translateX(-50%);
		background: rgba(0,0,0,0.5);
		border-radius: 50%;
		animation: swipe-animation 1.5s infinite;
		display: none;
	}
	@media screen and (max-width: 620px) {
		.swipeHint { display: block; }
	}
	@keyframes swipe-animation{
		0% {
			left: 70%;
		}
	
		70% {
			left: 20%;
		}

		100% {
			left: 70%;
		}
	}
</style>
