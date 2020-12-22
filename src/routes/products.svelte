<script>
	import ProductInfo from '../components/ProductInfo.svelte';
</script>

<style>
	h2, figure{
		text-align: center;
		margin: 0 auto;
		color:#ff2c54;
	}

	h2 {
		font-size: 1em;
		text-transform: uppercase;
		font-weight: 700;
		margin: 0 0 0.5em 0;
	}

	figure {
		margin: 0 0 1em 0;
	}


	@media (min-width: 480px) {
		h2 {
			font-size: 2em;
		}
	}
</style>

<svelte:head>
	<title>Product Info</title>
</svelte:head>

<h2>Products</h2>

<figure>
    <ProductInfo></ProductInfo>
</figure>

