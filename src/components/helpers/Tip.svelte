<script>
	import { DollarSign } from "lucide-svelte";
	export let text = "Tip";
	export let href =
		"https://checkout.stripe.com/c/pay/cs_live_a1c8aI8brGH7KRjoEd6XOGBGfCStLyqn4ajmd23lPVuGgOBkoPgF1RpI2j#fidkdWxOYHwnPyd1blppbHNgWjA0SF9CXVZETVdkV01pVG5SSlVUPENGNGt3RERgQnFOSFY2dnFzRFZiNDc9UGYxQjxvT1d9S1M0aTxrRzBNcmZxcEZmQTddc2NUXTZscXFVM3RscV9UMTNXNTVndTBqUnBcRCcpJ3VpbGtuQH11anZgYUxhJz8nZ0xcMGtMPUdPZjVqNn90YFBcJ3gl";
</script>

<a class="tip-button" {href} target="_blank" rel="noreferrer" role="button"
	>{text} <span><DollarSign color="currentColor" /></span>
</a>

<style>
	a.tip-button {
		background: var(--color-fg);
		color: var(--color-bg);
		display: inline-flex;
		align-items: center;
		justify-content: center;
		border-radius: 4px;
		box-shadow: 0px 3px 3px 0px var(--color-gray-500);
		letter-spacing: 0.05em;
		padding: 12px 16px;
		position: absolute;
		top: 16px;
		right: 16px;
	}

	span {
		display: inline-block;
		border: 1px solid currentColor;
		width: 1.5em;
		height: 1.5em;
		border-radius: 50%;
		text-align: center;
		display: inline-flex;
		justify-content: center;
		align-items: center;
		margin-left: 8px;
		padding: 2px;
	}
</style>
