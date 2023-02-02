<script>
	import { onMount } from 'svelte';

	import Line from "./charts/Line/App.svelte"

	// This creates a variable that becomes true once rosae is loaded
	let loaded = false;
	const onRosaeNlgLoad = () => { loaded = true }

	let divisions, template, words;
	onMount(async () => {
		const res = await fetch(`https://gist.githubusercontent.com/theojolliffe/25c4ba707fad4a06ee2bb822fa6a600e/raw/9fa39b79fc680bf1d4bcc3494f4cf25ba2ccb56b/divisions.json`);
		divisions = await res.json();
		console.log("divisions", divisions)

		fetch("template.pug")
        	.then((res) => res.text())
        	.then((txt) => (template = txt))

		fetch("words.json")
        	.then((res) => res.json())
        	.then((txt) => (words = txt))

	});
	
	let selected, story, data;
	
	$: console.log("selected", selected)
	$: console.log("words", words)
	$: if (selected) {
		story = selected['Story']
		data = selected['Data']
		console.log("story", story)
		console.log("data", data)
	}
	
	function strip(string) {
		let firstSpaceIndex = string.indexOf(' ');
		let result = string.substr(firstSpaceIndex + 1);
		return result.toLowerCase()
	}

	// Create a look up which will allow us to index the previous time. There's probably a good way of automating finding the previous time span if you were going to be using this engine for monthly releases, in ordered to save having to update this lookup each time.


	function generate(selected) {
		let result = rosaenlg_en_US.render(template, {
			language: 'en_UK',
			selected: selected,
			strip: strip,
			words: words
		})
		console.log(result.split(`<div id="chartinsert"></div>`))
		return result.split(`<div id="chartinsert"></div>`)
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

	{#if selected && template && loaded && words && story}
		{#each generate(selected) as chunk, i}
			{@html chunk}
			{#if (i<story.length)}
				{@const splits = story[i]['label'].split("_")}
				<div style="height: 150px; width: 50%">
					<Line data_raw={data[splits[0]][splits[1]]}/>
				</div>
			{/if}
		{/each}
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