<script>
	import SearchTerms from '../components/SearchTerms.svelte';
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
	<title>Search Terms</title>
</svelte:head>

<h2>Search Terms</h2>

<figure>
    <SearchTerms></SearchTerms>
</figure>

