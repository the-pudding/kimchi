<script>
	import P5 from 'p5-svelte';
	import { fade } from 'svelte/transition';
	import Typewriter from 'svelte-typewriter';
	export let chapter;
	export let w;
	export let h;
	export let maxWidth;
	export let chapterTracker = Number(chapter);
	let running = true;
	let delayNumber = 1000;
	let blankScreen = 0;
	
	export let cutsceneText;
	let cutsceneStage = 0;
	let stageText = cutsceneText;
	let opacityAmount = 40;

	
	
	let typeClick = 0;
	function resetTypewriter() {
		typeClick = 1;
	}
	
	function next() {
		if (cutsceneStage < stageText.length) {
			if (typeClick != 0) {
				cutsceneStage++;
				typeClick = 0;
			} else {
				typeClick++;
			}
			delayNumber = 0;
			opacityAmount = 255;
			
			if (cutsceneStage > stageText.length - 1) {
				running = false;
				if ( chapterTracker < 13) {
					chapterTracker = chapterTracker + 1;
				} else {
					chapterTracker = 0;
				}
			}
			mx = getRandomInt(w);
			my = getRandomInt(h);
			xVel = randomIntFromInterval(-90,90);
			yVel = randomIntFromInterval(-90,90);
		}
	}
	
	let cellSize = 60;
	let ballX = 20
	let ballY = h/2
	let xVel = randomIntFromInterval(-40,40);
	let yVel = randomIntFromInterval(-40,40);
	let mx = getRandomInt(w);
	let my = getRandomInt(h);
	let diagonalLength = Math.sqrt(w*w + h*h);
	let counter = 0;
	let randomizer = randomIntFromInterval(-20,20);
	const sketch = (p) => {
		let img;
		p.setup = () => {
			p.createCanvas(w, h);
			p.background(0);
			p.noStroke();
			if (chapter == 1) {
				img = p.loadImage(`assets/kimchi/bg-1.jpg`);
			} else if (chapter < 12) {
				img = p.loadImage(`assets/kimchi/kimchibg.png`);
			} else {
				img = p.loadImage(`assets/kimchi/kimchibg-2.png`);
			}
			p.frameRate(20);
		};
	
		p.draw = () => {
			if (running) {
				p.background([0,0,0,2]);
				for (let x = 0; x < w/cellSize; x += 1) {
					for (let y = 0; y < h/cellSize; y += 1) {
						let xCoord = (x*cellSize/w * img.width);
						let yCoord = (y*cellSize/h * img.height);
						let dist = getDistance(xCoord,yCoord,ballX,ballY);
						if (yCoord > img.width) {
							yCoord = img.width;
						}
						let randomSeeker = Math.round(xCoord) + randomizer;
						if (xCoord + randomSeeker > img.width/cellSize) {
							randomSeeker = Math.round(img.width/cellSize);
						}
						let c1 = img.get(xCoord, yCoord);
						cellSize = w/40 + Math.round(dist*10);
						
						if (chapter == 1) {
							c1[0] *= 0.6;
							c1[1] *= 0.6;
							c1[2] *= 0.6; 
							c1[3] = 8;
						}
						if (chapter == 4) {
							c1[0] *= 0.1;
							c1[1] *= 0.6;
							c1[2] *= 0.6; 
							c1[3] = 20;
							cellSize *= 1.4;
						}
						if (chapter == 7) {
							c1[0] *= 0.6;
							c1[1] *= 7;
							c1[2] *= 2; 
							c1[3] = 6;
						}
						if (chapter == 10) {
							c1[0] *= 0.7;
							c1[1] *= 0.2;
							c1[2] *= 1.8; 
							c1[3] = 8;
						}
						if (chapter == 13) {
							c1[0] *= 0.1;
							c1[1] *= 0.1;
							c1[2] *= 1.1; 
							c1[3] = 20;
						}
						p.fill(c1);
						let rand = dist*90*Math.random();
						p.ellipse(x*cellSize,y*cellSize,cellSize*1.2+rand,cellSize*1.2+rand);
						//p.rect(x*cellSize,y*cellSize,cellSize,cellSize);
					}
			  	}
			  	counter++;
			  	changeBall();
		  	}
		};
		
		function getDistance(x,y,bx,by) {
			bx = bx + Math.sin(counter/chapter*4)*40;
			by = by + Math.cos(counter/chapter*4)*40;
			var a = x - bx;
			var b = y - by;
			return Math.sqrt( a*a + b*b ) / diagonalLength;
		}
		
		
		function changeBall() {
			if (ballX < mx) {
			  xVel = Math.sqrt(Math.abs(ballX - mx))*6;
			} else if (ballX > mx) {
			  xVel = -Math.sqrt(Math.abs(mx - ballX))*6;
			}
			if (ballY < my) {
			  yVel = Math.sqrt(Math.abs(ballY - my))*6;
			} else if (ballY > my) {
			  yVel = -Math.sqrt(Math.abs(my - ballY))*6;
			}
			ballX += xVel;
			ballY += yVel;
		  }
	};
		
	function getRandomInt(max) {
		return Math.floor(Math.random() * max);
	}
		
	function randomIntFromInterval(min, max) { // min and max included 
		return Math.floor(Math.random() * (max - min + 1) + min)
	}
	 
	
	  
	$: {
		chapterTracker = chapterTracker;
		cutsceneStage = cutsceneStage;
		w = w;
		if (w >= maxWidth) {
			h = maxWidth * 7/11;
		} else if (w > 620) {
			h = (w) * 7/11;
		} else {
			h = (w) * 14/11;
		}
		if (stageText[cutsceneStage] == "…") {
			blankScreen++;
		}
		h = h;
	}
</script>
<svelte:window bind:innerWidth={w}/>
<div class="sceneInside cutscene"  on:click={next} on:keyup={next} out:fade="{{duration: 200}}">
	<div class="visualContainer">
		<P5 {sketch} />
	</div>
	<div class="introWords">
		{#if stageText[cutsceneStage] != undefined}
			{#if typeClick == 0}
			<Typewriter interval={[90,10,15,1,2,12,20,2,5,10,14,10,20,8,14,30]} delay={delayNumber} on:done={resetTypewriter}>
			{stageText[cutsceneStage]}
			</Typewriter>
			{:else}
				{@html stageText[cutsceneStage]}
			{/if}
		{/if}
	</div>
	

</div>

<style>
	.cutscene { cursor: pointer; }
	.sceneInside {
		background: black;
	}
</style>
