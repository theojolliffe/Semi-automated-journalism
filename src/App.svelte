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

		fetch("template.pug")
        	.then((res) => res.text())
        	.then((txt) => (template = txt))

		fetch("words.json")
        	.then((res) => res.json())
        	.then((txt) => (words = txt))

	});
	
	let selected, story, data, splits;
	
	$: if (selected) {
		story = selected['Story']
		splits = story.map((e) => e.label.split("_"))
		data = selected['Data']
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
		return result.split(`<div id="chartinsert"></div>`)
	}


	function make_data(data_raw) {
		let data_temp = []
        Object.keys(data_raw).forEach((e) => {
            data_temp.push({myX: e, myY: data_raw[e]})
        })
		return data_temp
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

	{#if selected && template && loaded && words && story && splits}
		{#each generate(selected) as chunk, i}
			{@html chunk}
			{#if (i<story.length)}
				<div style="height: 150px; width: 50%">
					<Line data={make_data(data[splits[i][0]][splits[i][1]])} ></Line>
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