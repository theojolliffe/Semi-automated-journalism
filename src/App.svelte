<script>
	import { onMount } from 'svelte';

	// This creates a variable that becomes true once rosae is loaded
	let loaded = false;
	const onRosaeNlgLoad = () => { loaded = true }

	let divisions, template;
	onMount(async () => {
		const res = await fetch(`https://gist.githubusercontent.com/theojolliffe/25c4ba707fad4a06ee2bb822fa6a600e/raw/2f2a1870b000cd93a9932c673b148999fe294181/divisions.json`);
		divisions = await res.json();
		console.log("divisions", divisions)

		fetch("template.pug")
        	.then((res) => res.text())
        	.then((txt) => (template = txt))
	});
	
	let selected;
	
	$: console.log("selected", selected)
	
	function strip(string) {
		let firstSpaceIndex = string.indexOf(' ');
		let result = string.substr(firstSpaceIndex + 1);
		return result.toLowerCase()
	}

	function generate() {
		let result = rosaenlg_en_US.render(template, {
			language: 'en_UK',
			selected: selected,
			strip: strip
		})
		return result
	}

</script>

<svelte:head>
	<script src="./rosaenlg.js" on:load="{onRosaeNlgLoad}"></script>
</svelte:head>

{#if divisions}
	<h2>
		Select a division of items
	</h2>
	<select bind:value={selected}>
		{#each divisions as division}
		<option value={division}>
			{division.Grouping.slice(3)}
		</option>
		{/each}
	</select>
{/if}

<br>

{#if selected && template && loaded}
	{@html generate(selected)}
{/if}

