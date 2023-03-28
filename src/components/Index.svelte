<script>
	import { getContext } from "svelte";
	import Kimchi from "$components/kimchi/kimchi.main.svelte";
	import WIP from "$components/helpers/WIP.svelte";
	// import Footer from "$components/Footer.svelte";

	const copy = getContext("copy");
	for (let i in copy) {
		let counter = 0;
		for (let j in copy[i]) {
			copy[i][j]["num"] = counter;
			copy[i][j]["xPos"] = "left";
			copy[i][j]["yPos"] = "top";
			if ( (copy[i][j]["x"] > 25 && copy[i][j]["x"] < 50 ) || (copy[i][j]["x"] > 75)) {
				copy[i][j]["xPos"] = "right"; 
			}
			if (copy[i][j]["y"] > 50) {
				copy[i][j]["yPos"] = "bottom"; 
			}
			counter++;
		}
	}
	// const data = getContext("data");
</script>

<!-- <WIP /> -->
<Kimchi copy={copy}/>
<!-- <Footer /> -->
