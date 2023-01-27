<script>
	import { onMount } from 'svelte';

	// This creates a variable that becomes true once rosae is loaded
	let loaded = false;
	const onRosaeNlgLoad = () => { loaded = true }

	let divisions, template, words;
	onMount(async () => {
		const res = await fetch(`https://gist.githubusercontent.com/theojolliffe/25c4ba707fad4a06ee2bb822fa6a600e/raw/2f2a1870b000cd93a9932c673b148999fe294181/divisions.json`);
		divisions = await res.json();
		console.log("divisions", divisions)

		fetch("template.pug")
        	.then((res) => res.text())
        	.then((txt) => (template = txt))

		fetch("words.json")
        	.then((res) => res.json())
        	.then((txt) => (words = txt))

	});
	
	let selected;
	
	$: console.log("selected", selected)
	$: console.log("words", words)
	
	function strip(string) {
		let firstSpaceIndex = string.indexOf(' ');
		let result = string.substr(firstSpaceIndex + 1);
		return result.toLowerCase()
	}

	// Create a look up which will allow us to index the previous time. There's probably a good way of automating finding the previous time span if you were going to be using this engine for monthly releases, in ordered to save having to update this lookup each time.
	let prev_time = {
		"1-month": "Dec 2021",
		"12-month": "Nov 2022"
	}
	let change_span = {
		"1-month": "12-month",
		"12-month": "1-month"
	}


	function generate() {
		let result = rosaenlg_en_US.render(template, {
			language: 'en_UK',
			selected: selected,
			strip: strip,
			words: words,
			prev_time: prev_time,
			change_span: change_span
		})
		return result
	}

</script>

<svelte:head>
	<script src="./rosaenlg.js" on:load="{onRosaeNlgLoad}"></script>
</svelte:head>

<div style="width: 640px; margin: 50px auto;">
	{#if divisions}
		<select bind:value={selected}>
			{#each divisions as division}
			<option value={division}>
				{division.Grouping.slice(3)}
			</option>
			{/each}
		</select>
	{/if}

	<br>

	{#if selected && template && loaded && words}
		{@html generate(selected)}
	{/if}
</div>

<style>
	@import url('https://fonts.googleapis.com/css2?family=Open+Sans:wght@400;700&display=swap');
	:global(body) {
		font-family: 'Open Sans', sans-serif;
		padding: 0px;
		line-height: 2;
		color: #323132;
	}

</style>