<script>
	// 	Note: Due to REPL limitations, full responsiveness may not work here. Download the example from here or from the website (https://layercake.graphics/example/Line) and run locally to get all features.
	
	import { LayerCake, Svg } from 'layercake';

	import Line from './Line.svelte';
	import Area from './Area.svelte';
	import AxisX from './AxisX.svelte';
	import AxisY from './AxisY.svelte';

    export let data;

    $: console.log("data", data )

    // let data = [
    //     {
    //         myX: 1979,
    //         myY: 7.19
    //     },
    //     {
    //         myX: 1980,
    //         myY: 7.83
    //     },
    //     {
    //         myX: 1981,
    //         myY: 7.24
    //     },
    //     {
    //         myX: 1982,
    //         myY: 7.44
    //     }
    // ]

	const xKey = 'myX';
	const yKey = 'myY';

    data.forEach(d => {
        d[yKey] = +d[yKey];
    });

</script>

<style>
	/*
		The wrapper div needs to have an explicit width and height in CSS.
		It can also be a flexbox child or CSS grid element.
		The point being it needs dimensions since the <LayerCake> element will
		expand to fill it.
	*/	
	.chart-container {
		width: 100%;
		height: 100%;
	}
</style>

<div class="chart-container">
	<LayerCake
		padding={{ right: 10, bottom: 20, left: 25 }}
		x={xKey}
		y={yKey}
		yDomain={[-5, null]}
		data={data}
	>
		<Svg>
			<AxisX
                ticks={1}
            />
			<AxisY
				ticks={4}
			/>
			<Line/>
			<Area/>
		</Svg>
	</LayerCake>
</div>
