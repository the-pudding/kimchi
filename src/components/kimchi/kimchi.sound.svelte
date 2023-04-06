<script>
	import * as Tone from 'tone'
	import { onMount } from 'svelte';
	export let chapter;
	export let mode;
	let synth;
	let now;
	let mounted = false;
	export let soundon;
	let measure = 4000;
	let scales = ["A","Bb","B","C","Db","D","Eb","E","F","Gb","G","Ab","A","Bb","B","C","Db","D","Eb","E","F","Gb","G","Ab","A","Bb","B","C","Db","D","Eb","E","F","Gb","G","Ab"];
	let baseKey = 8;
	let key = baseKey;
	let progressionStep = -1;
	let sceneDarkness = {
		"0": "dark",
		"1": "light",
		"2": "dark",
		"3": "light",
		"4": "light",
		"5": "dark",
		"6": "light",
		"7": "light",
		"8": "dark",
		"9": "light",
		"10": "light",
		"11": "dark",
		"12": "dark",
		"13": "dark"
	}
	
	let chordProgressionLookup = {
		"0": ["happy","anthem"],
		"1": ["happy","anthem"],
		"2": ["happy","anthem"],
		"3": ["upbeat","peppy"],
		"4": ["contemplative","slow"],
		"5": ["contemplative","slow"],
		"6": ["somber","slower"],
		"7": ["contemplative","slower"],
		"8": ["contemplative","slow"],
		"9": ["contemplative","slow"],
		"10": ["content","forging"],
		"11": ["content","forging"],
		"12": ["content","forging"],
		"13": ["content","anthem"]
	}
	
	let songLookup = {
		"anthem": [
			["1",measure/64/1000,2,0.5, true],
			["2",measure/64/1000,4,-1, true],
			["2",measure/64/1000,3,-1, true],
			["3",measure/16/1000,4, 0.5, true],
			["4",measure/64/1000,5, 0.5, false],
			["4",measure/64/1000,3, 0.5, false]
		],
		"peppy": [
			["1",measure/64/1000,1,0.5, true],
			["2",measure/32/1000,4,-1, false],
			["3",measure/32/1000,3, 0.5, false],
			["4",measure/64/1000,3, 0.5, false],
			["4",measure/32/1000,4, 0.5, false]
		],
		"slow": [
			["2",measure/2/1000,16,-1, true],
			["3",measure/32/1000,6, 0.8, false],
			["3",measure/16/1000,8, 0.7, false],
			["3",measure/4/1000,16, 0.7, false]
		],
		"slower": [
			["2",measure/2/1000,32,-1, true],
			["3",measure/8/1000,8,-1, false],
			["3",measure/20/1000,16,-1, false]
		],
		"forging": [
			["1",measure/32/1000,4,-1, true],
			["2",measure/3/1000,16,-1, true],
			["3",measure/16/1000,5, 0.8, false],
			["3",measure/16/1000,3, 0.5, false]
		]
	} 
	
	// each has 16
	let chordProgression = {
		"happy": [
			[0, "M"],
			[4, "M"],
			[2, "m"],
			[5, "M"],
			[0, "M"],
			[4, "M"],
			[2, "m"],
			[5, "M"]
		],
		"upbeat": [
			[0, "M"],
			[5, "M"],
			[2, "m"],
			[7, "M"],
			[0, "M"],
			[5, "M"],
			[2, "m"],
			[7, "M"]
		],
		"contemplative": [
			[2, "m"],
			[5, "M"],
			[4, "M"],
			[7, "M"],
			[2, "m"],
			[5, "M"],
			[4, "M"],
			[7, "M"]
		],
		"somber": [
			[0, "m"],
			[4, "M"],
			[2, "m"],
			[5, "m"],
			[0, "m"],
			[4, "M"],
			[2, "m"],
			[5, "m"]
		],
		"content": [
			[7, "M"],
			[11, "M"],
			[9, "m"],
			[0, "M"],
			[7, "M"],
			[11, "m"],
			[9, "m"],
			[0, "M"]
		]
	};
	let chord = "M";
	let chords = {
	  "M": [0, 4, 7],
	  "m": [0, 3, 7],
	  "aug": [0, 4, 8],
	  "dim": [0, 3, 6],
	  "sus2": [0, 2, 7],
	  "sus4": [0, 5, 7],
	  "7": [0, 4, 7, 10],
	  "M7": [0, 4, 7, 11],
	  "m7": [0, 3, 7, 10],
	  "mM7": [0, 3, 7, 11],
	  "6": [0, 4, 7, 9],
	  "m6": [0, 3, 7, 9],
	  "9": [0, 2, 4, 7, 10],
	  "M9": [0, 2, 4, 7, 11],
	  "m9": [0, 2, 3, 7, 10],
	  "11": [0, 2, 4, 5, 7, 10],
	  "M11": [0, 2, 4, 5, 7, 11],
	  "m11": [0, 2, 3, 5, 7, 10],
	  "13": [0, 2, 4, 7, 9, 10],
	  "M13": [0, 2, 4, 7, 9, 11],
	  "m13": [0, 2, 3, 7, 9, 10],
	  "add9": [0, 2, 4, 7],
	  "6add9": [0, 2, 4, 7, 9],
	  "aug7": [0, 4, 8, 10],
	  "aug9": [0, 4, 8, 10, 2],
	  "dim7": [0, 3, 6, 9],
	  "m7b5": [0, 3, 6, 10],
	  "7b5": [0, 4, 6, 10],
	  "7#5": [0, 4, 8, 10],
	  "7b9": [0, 4, 7, 10, 1],
	  "7#9": [0, 4, 7, 10, 3]
	}
	
	onMount(async () => {
		synth = new Tone.PolySynth(Tone.Synth).toDestination();
		now = Tone.now();
		mounted = true;
	});
	function soundToggle() {
		soundon = !soundon;
	}
	
	function play(n, length) {
		if (mounted) {
			if (soundon) {
				now = Tone.now();
					synth.triggerAttack(n, now);
			}
			synth.triggerRelease([n], now + length);
		}
	}

	let counter = 0;
	let songPlayed = false;
	setInterval(song, measure/24);
	function song() {
		if (mounted && soundon) {
			if (counter % 16 == 0 || counter == 0) {
				let currentProgression = chordProgression[chordProgressionLookup[chapter][0]];
				progressionStep +=1;
				if (progressionStep >= currentProgression.length - 1) {
					progressionStep = 0;
				}
				key = currentProgression[progressionStep][0];
				chord = currentProgression[progressionStep][1];
				//console.log(scales[key], chord);
			}

			let currentSong = songLookup[chordProgressionLookup[chapter][1]];
			for (let i = 0; i < currentSong.length; i++) {
				playNote(currentSong[i]);
			}
		} else if (mounted) {
			console.log("off");
			Tone.Transport.pause();
		}
		if (songPlayed) {
			counter++;
		}
	}
	
	function playNote(song) {
		songPlayed = true;
		let pitch = song[0];
		let len = song[1];
		let  interval = song[2];
		let rand = song[3];
		let chordKey = song[4];
		if (counter % interval == 0 && 64-(counter%64) > len ) {
			let keyShift = key;
			if (!chordKey) {
				//let keyRand = melody[chordProgressionLookup[chapter][1]].randFromArray();
				let keyRand = chords[chord].randFromArray();
				keyShift = key + keyRand;
			}
			let m = scales[keyShift];
			if (rand == -1 || Math.random() < rand ) {
				if (pitch == "4" || pitch == "3") {
					if (counter > 16) {
						play(m + pitch, len);
					}
				} else {
					play(m + pitch, len);
				}
			}
		}
	}
	

	Array.prototype.randFromArray = function(){
	  return this[Math.floor(Math.random()*this.length)];
	}
	let oldChapter = -1;
	$: {
		if (chapter != oldChapter) {
			progressionStep = -1;
			oldChapter = chapter;
		}
		chapter = chapter;
		soundon = soundon;
		mode = mode;
	}
</script>	
<div class="soundbar">
	{#if soundon}
		
			{#if mode}
			<button class="soundbutton light" on:keydown={soundToggle} on:click={soundToggle}>
				<img class="soundIcon" alt="sound on button" src="assets/kimchi/universal/sound-on-light.png"/>
				Sound on
			</button>
			{:else}
			<button class="soundbutton {sceneDarkness[chapter]}" on:keydown={soundToggle} on:click={soundToggle}>
				<img class="soundIcon" alt="sound on button" src="assets/kimchi/universal/sound-on-{sceneDarkness[chapter]}.png"/>
				Sound on
			</button>
			{/if}
		
	{:else}
		
			{#if mode}
			<button class="soundbutton light" on:keydown={soundToggle} on:click={soundToggle}>
				<img class="soundIcon" alt="sound off button" src="assets/kimchi/universal/sound-off-light.png"/>
				Sound off
			</button>
			{:else}
			<button class="soundbutton {sceneDarkness[chapter]}" on:keydown={soundToggle} on:click={soundToggle}>
				<img class="soundIcon" alt="sound off button" src="assets/kimchi/universal/sound-off-{sceneDarkness[chapter]}.png"/>
				Sound off
			</button>
			{/if}
		
	{/if}
</div>
<style>
	.soundbar { text-align: right; }
	.soundbutton {
		cursor: pointer;
		display: inline-block;
		position: absolute;
		right: 0px;
		top: 0px;
		/* background: rgba(0,0,0,0.1); */
		width: 115px;
		border-radius: 0%;
		color: black;
		text-align: center;
		z-index: 9999;
		user-select: none;
		background: none;
	}
	.soundbutton.light {
		color: white;
	}
	.soundbutton img {
		width: 20px;
		height: 20px;
		margin-right: 5px;
		float: left;
	}
	.soundbutton:hover {
		opacity: 0.8;
	}
</style>