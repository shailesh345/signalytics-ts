<script>
	import dashboardImg from '../../static/dashboard.svg';
</script>

<style>
	h1, figure, p {
		text-align: center;
		margin: 0 auto;
		color:#ff2c54;
	}

	h1 {
		font-size: 2.8em;
		text-transform: uppercase;
		font-weight: 700;
		margin: 0 0 0.5em 0;
	}

	figure {
		margin: 0 0 1em 0;
	}

	img {
		width: 100%;
		max-width: 400px;
		margin: 0 0 1em 0;
	}

	p {
		margin: 1em auto;
	}

	@media (min-width: 480px) {
		h1 {
			font-size: 4em;
		}
	}
</style>

<svelte:head>
	<title>Signalytics</title>
</svelte:head>

<h1>Signalytics.AI</h1>

<figure>
	<img alt="Signalytics" src="{dashboardImg}">
	<figcaption>Customizable dashboards coming soon!!</figcaption>
</figure>

<p><strong></strong></p>
