<script>
	import { onMount } from 'svelte';
	let divisions
	onMount(async () => {
		const res = await fetch(`https://gist.githubusercontent.com/theojolliffe/25c4ba707fad4a06ee2bb822fa6a600e/raw/2f2a1870b000cd93a9932c673b148999fe294181/divisions.json`);
		divisions = await res.json();
		console.log("divisions", divisions)
	});
	
	let selected;
	
	$: console.log("selected", selected)
	
		function strip(string) {
		let firstSpaceIndex = string.indexOf(' ');
		let result = string.substr(firstSpaceIndex + 1);
		return result.toLowerCase()
	}

</script>

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

{#if selected}
	<h3>
		Here is our 'story' list which will form the structure of the content
	</h3>
	{#each selected.Story as story, i }
		<p>
			{i}:
			{story.label}
		</p>
	{/each}
	<h3>
		We can iterate through this list and look at some data
	</h3>
	{#each selected.Story as story, i }
		{@const splits = story.label.split("_")}
		<p>
		<!-- 	let's create a variable	that splits our label into an array we can use to index our data object	 -->
			{i}:
			The {splits[1]} inflation of {strip(splits[0])}
			was {selected.Data[splits[0]][splits[1]][splits[2]] + "%"} in December.
		</p>
	{/each}
{/if}

