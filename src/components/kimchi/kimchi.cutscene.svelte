<script>
	import P5 from 'p5-svelte';
	import { fade } from 'svelte/transition';
	export let chapter;
	export let w;
	export let h;
	export let maxWidth;
	export let chapterTracker = Number(chapter);
	let running = true;

	let cutsceneText = {
		"scene1": {"words":"Psycheldelic kimchi"},
		"scene2": {
			"words": "The kimchi flavor reaches deep into my soul. Psychodelic visual representation of Grandma's kimchi. It's beautiful but chaotic."
		},
		"scene3": {"words":"","color":""},
		"scene4": {
			"words": "The kimchi has no soul, and tastes like the sum of its ingredients. A dull, gray representation of the kimchi flavor. It's both chaotic and dull. Disappointing."
		},
		"scene5": {"words":""},
		"scene6": {
			"words": "The kimchi is delicious, albeit not Grandma's kimchi. A very ordered and red-only representation of the kimchi. It's beautiful, but also somewhat foreign."
		},
		"scene7": {"words":"","color":""},
		"scene8": {
			"words": "The kimchi is a mix of Grandma's kimchi, but I've reconstructed it for myself. A very ordered representation of kimchi. It's beautiful, and it's my kimchi."
		}
	}

	function exit() {
		running = false;
		if ( chapterTracker < 7) {
			chapterTracker = chapterTracker + 1;
		} else {
			chapterTracker = 0;
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
			img = p.loadImage(`assets/kimchi/kimchibg.png`);
			p.frameRate(20);
		};
	
		p.draw = () => {
			if (running) {
				if (chapter == 2 || chapter == 8) {
					p.background([0,0,0,20]);
				}
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
						cellSize = w/200 + Math.round(dist*20);
						if (chapter == 2) {
							c1[3] = 50;
						}
						if (chapter == 4) {
							c1[0] *= 0.1;
							c1[1] *= 0.7;
							c1[2] *= 0.7; 
							c1[3] = 10;
						}
						if (chapter == 6) {
							c1[0] *= 1.1;
							c1[1] *= 0.6;
							c1[2] *= 0.6; 
							c1[3] = 20;
							cellSize *= 1.4;
						}
						if (chapter == 8) {
							c1[0] *= 1.5;
							c1[1] *= 1.1;
							c1[2] *= 1.1; 
						}
						p.fill(c1);
						p.ellipse(x*cellSize,y*cellSize,cellSize,cellSize);
						//p.rect(x*cellSize,y*cellSize,cellSize,cellSize);
					}
			  	}
			  	counter++;
			  	changeBall();
		  	}
		};
		
		function getDistance(x,y,bx,by) {
			bx = bx + Math.sin(counter/10)*200;
			if (chapter == 8) {
				by = by + Math.cos(counter/10)*200;
			}
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
		
	setInterval(function() {
		mx = getRandomInt(w);
		my = getRandomInt(h);
		xVel = randomIntFromInterval(-90,90);
		yVel = randomIntFromInterval(-90,90);
		// randomizer = randomIntFromInterval(-80,80);
	},1700)
		
	function getRandomInt(max) {
		return Math.floor(Math.random() * max);
	}
		
	function randomIntFromInterval(min, max) { // min and max included 
		return Math.floor(Math.random() * (max - min + 1) + min)
	}
	 
	
	  
	$: {
		chapterTracker = chapterTracker;
		w = w;
		if (w >= maxWidth) {
			h = maxWidth * 7/11;
		} else if (w > 620) {
			h = (w-40) * 7/11;
		} else {
			h = (w-40) * 14/11;
		}
		h = h;
	}
</script>
<svelte:window bind:innerWidth={w}/>
<div class="sceneInside cutscene"  on:click={exit} on:keyup={exit} out:fade="{{duration: 200}}">
	<div class="visualContainer">
		<P5 {sketch} />
	</div>
	<div class="introWords">
		{cutsceneText["scene" + chapterTracker].words}
		<div class="introExit">[tap to continue]</div>
	</div>
	
	<!-- {#if chapterTracker < 8}
		<div class="button" on:click={nextChapter}>
			More kimchi
		</div>
	{:else}
		<div class="button" on:click={end}>
			Restart
		</div>
	{/if} -->

</div>

<style>
.sceneInside {
	background: black;
}
</style>
