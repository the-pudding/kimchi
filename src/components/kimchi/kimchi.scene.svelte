<script>
	import { fade } from 'svelte/transition';
	import { swipe } from 'svelte-gestures';
	
	// determines which scene to show
	export let chapter;
	export let chapterTracker = Number(chapter);
	export let hoverHints;
	export let year;
	
	
	
	let sceneIntroWords = {
		"1": "<p><strong>It's the fall of 1995.</strong> You live in Kansas with your Korean family. You walk into the kitchen and find that it's that time of year again.</p><p>Kimchi day.</p>",
		"3": "<p><strong>It's fall of 2005</strong> – your first year of college. And today, you can't stop thinking about her kimchi.</p><p>But you're thousands of miles from home. And Grandma now lives in Seoul.</p><p>You try to remember how she made her kimchi, and make it in your dorm kitchen.</p>",
		"5": "<p><strong>It's the fall of 2015</strong> – there are Korean restaurants everywhere in the US, so you can eat kimchi whenever you want.</p><p>Is it Grandma's kimchi? You're not sure. You don't really remember.</p><p>Whatever, let's go eat kimchi.</p>",
		"7": "<p><strong>It's the fall of 2022.</strong> You never learned how to make Grandma's kimchi, but you've figured out how to make your own.</p><p>Does it taste like hers? You don't remember.</p><p>But this is <em>your</em> kimchi. And today is kimchi day.</p>"
	}
	let sceneOffset = "rightSide";
	let modalShown = false;
	let allClicked = false;
	let hed, words, type, image, alt;
	let fullWidth = 2200/100;
	
	function swipeHandler(event) {
		if (event.detail.direction == "left") {
			sceneOffset = "leftSlide";
		} else {
			sceneOffset = "rightSide";
		}
	}
	  
	let selectedHint = null;
	let nextHint = null;
	function showModal(e) {
		hed = e.target.getAttribute("hed");
		words = e.target.getAttribute("words");
		type = e.target.getAttribute("type");
		image = "scene" + chapter + "/" + e.target.getAttribute("image");
		alt = e.target.getAttribute("alt");
		selectedHint = Number(e.target.getAttribute("num"));
		hoverHints[selectedHint].clicked = true;
		if (type == "bigImage") {
			modalShown = true;
		}
		// if the right hint is clicked, then show hearts from grandma
		if (selectedHint == nextHint) {
			responseVisibility = true;
		}
		// then in 2 second, hide the quotes
		setTimeout(function() {
			if (responseVisibility) {
				permaQuoteVisibility = false;
			}
			responseVisibility = false;
		}, 2000);
		
		/// then in 4 seconds, show the picture again
		setTimeout(function() {
			permaQuoteVisibility = true;
		}, 4000);
		checkAllClicked();
	}
	
	function closeModal() {
		modalShown = false;
		selectedHint = null;
		checkAllClicked();
	}
	
	let hintText = "♥ ♥ ♥";
	let hintImage = null;
	let responseVisibility = false;
	let permaQuoteVisibility = true;
	// Checking if all modals have been clicked
	function checkAllClicked() {
		allClicked = true;
		hoverHints.forEach(function(d) {
			if (d.clicked == "false") {
				hintText = d.hintText;
				hintImage = d.image;
				allClicked = false;
				nextHint = d.num;
			}
		});
		
		if (allClicked) {
			hintText = "Want to taste kimchi?";
			hintImage = null;
		}
	}
	
	function nextChapter() {
		chapterTracker = chapterTracker + 1;
	}
	
	let clickedPos = {x:0,y:0};
	function getPosition(e) {
		clickedPos.x = e.offsetX/e.srcElement.width*100;
		clickedPos.y = e.offsetY/e.srcElement.height*100;
	}
	
	let introShown = true;
	function exitIntro() {
		introShown = false;
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
		checkAllClicked();
	}
</script>	
	<div class="sceneInside {sceneOffset}" on:click={getPosition} on:keydown={getPosition}  use:swipe={{ timeframe: 300, minSwipeDistance: 50 }} on:swipe={swipeHandler} >
		<img class="sceneImage" alt="scene of grandma making kimchi" src="assets/kimchi/scene{chapter}/background.png" on:click={closeModal} on:keydown={closeModal} draggable="false"/>
		
		{#each hoverHints as hint}
			<div class="hintContainer {hint.addclass}" class:selected="{selectedHint === hint.num}" style="width:{hint.width/fullWidth}%; left:{hint.x}%; top:{hint.y}%; pointer-events: {hint.notouch};" on:click={showModal} on:keydown={showModal} hed={hint.hed} words={hint.words} type={hint.type} num={hint.num}>
			{#if hint.width > 500}
				<img class="hoverHint bigItem" alt="{hint.alt}" src="assets/kimchi/scene{chapter}/{hint.image}" image={hint.image}  draggable="false"/>
			{:else}
				<img class="hoverHint smallItem" alt="{hint.alt}" src="assets/kimchi/scene{chapter}/{hint.image}" draggable="false"/>
			{/if}
			{#if hint.addclass == "speaker"}
				{#if permaQuoteVisibility}
				<div class="quotebox perma" transition:fade>
					{#if responseVisibility}
						<span in:fade>♥ ♥ ♥</span>
					{:else}
					<img src="assets/kimchi/scene{chapter}/{hintImage}" in:fade/>
					{/if}
				</div>
				{/if}
			{:else if hint.type != "bigImage"}
				<div class="quotebox" transition:fade>
					{hint.words}
				</div>
			{/if}
			
		</div>
		{/each}
	</div>
	{#if allClicked}
		<button class="button" on:click={nextChapter} on:keydown={nextChapter}>Eat kimchi</button>
	{/if}
	{#if modalShown}
		<div class="modal {type}" transition:fade="{{duration: 200}}" on:click={closeModal} on:keydown={closeModal}>
			<div class="closeModal" on:click={closeModal} on:keydown={closeModal}></div>
			{#if type == "bigImage"}
				<img alt="{alt}" src="assets/kimchi/{image}" draggable="false"/>
			{/if}
		</div>
	{/if}
	
	{#if introShown}
	<div class="sceneIntro" on:click={exitIntro} on:keyup={exitIntro} out:fade="{{duration: 200}}">
		<div class="introWords">
			{@html sceneIntroWords[chapter]}
			<div class="introExit">[tap to continue]</div>
		</div>
	</div>
	{/if}
	
<style>
	.sceneIntro {
		position: absolute;
		z-index: 9999;
		background: rgb(255,182,0);
		background: linear-gradient(0deg, rgba(255,182,0,1) 0%, rgba(164,27,10,1) 20%, rgba(92,9,41,1) 62%, rgba(70,3,50,1) 100%);
		left: 0;
		top: 0;
		width: 100%;
		height: 100%;
		cursor: pointer;
	}
	.introWords {
		position: absolute;
		top: 50%;
		transform: translate(-50%, -50%);
		color: white;
		padding: 10%;
		font-size: 2vw;
		line-height: 3vw;
		width: 60%;
		left: 50%;
	}
	.introExit {
		opacity: 0.4;
		font-size: 0.8em;
	}
	@media screen and (max-width: 570px) {
		.introWords {
			padding: 10%;
			font-size: 4vw;
			line-height: 5.5vw;
			width: 80%;
			left: 50%;
		}
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
	.hintContainer:hover .bigItem {
		transform: scale(1.008);
	}
	.hintContainer:hover .smallItem {
		margin-top: -2px;
	}
	.hintContainer:hover .sidehover.smallItem {
		margin-top: 0px;
		margin-left: -4px;
	}
	
	.quotebox {
		background: rgba(0,0,0,0.8);
		font-size: 15px;
		position: absolute;
		bottom: calc(100% + 15px);
		left: 45%;
		width: 230px;
		color: white;
		padding: 5px 7px;
		z-index: 999;
		display: none;
	}
	.hintContainer.selected .quotebox {
		display: block;
	}
	.hintContainer .quotebox.perma {
		display: block !important;
		background: white;
		color: black;
		width: auto;
		padding: 15px;
		box-shadow: 0px 0px 45px #000;
	}
	.quotebox.perma img {
		filter: grayscale(100) contrast(100%) brightness(200%);
	}
	.quotebox.perma .response {
		display: none;
	}
	.quotebox .quoteText {
		/* width: 130px; */
		display: inline-block;
		vertical-align: center;
	}
	.quotebox img {
		width: 70px;
		margin-right: 4px;
	}
	.quotebox::before {
		content: "";
		position: absolute;
		top: 100%;
		left: 5%;
		right: 10px;
		bottom: 10px;
		width: 0;
		height: 0;
		border-style: solid;
		border-width: 10px 10px 0 10px;
		border-color: rgba(0,0,0,0.8) transparent transparent transparent;
	}
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
		font-size: 1.8vw;
		line-height: 2.7vw;
		position: absolute;
		left: 10px;
		bottom: 10px;
		background: rgba(0,0,0,0.9);
		color: white;
		/* border: 2px solid #000; */
		padding: 15px 10px;
		width: calc(100% - 20px);
		max-height: 95%;
		z-index: 9999;
	}
	
	@media screen and (max-width: 880px) {
		.modal  {
			font-size: 17px;
			line-height: 22px;
		}
	}
	
	@media screen and (max-width: 570px) {
		.modal  {
			font-size: 16px;
			line-height: 20px;
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
		right: 10px;
		bottom: 10px;
		width: 0;
		height: 0;
		border-style: solid;
		border-width: 10px 10px 0 10px;
		border-color: #fff transparent transparent transparent;
	}
	
	
	/* WORDS ONLY */
	.modal.words img {
		display: none;
	}
	
	
	/* BIG IMAGE */
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
		max-width: none;
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
	}
	
	.modal .modalWords {
		position: relative;
		left: 0px;
		width: 100%;
	}
</style>
