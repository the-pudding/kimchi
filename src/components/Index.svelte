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
			counter++;
		}
	}
	// const data = getContext("data");
</script>

<!-- <WIP /> -->
<Kimchi copy={copy}/>
<!-- <Footer /> -->
