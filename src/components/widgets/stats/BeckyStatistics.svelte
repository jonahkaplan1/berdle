<script lang="ts">
	import { mode } from "../../../stores";
	import { modeData } from "../../../utils";

	import Stat from "./Stat.svelte";
	export let data: Stats;

    console.log(data)

	let beckyStats: [string, string | number][];
    let sumScores = 0;
    data.scores.forEach((element) => {
        sumScores += element;
    })
	$: {

		beckyStats = [
			["Played", data.played],
            ["Max Score", data.scores ? Math.max(...data.scores).toString() : 'N/A'],
            ["Lowest Score", data.scores ? Math.min(...data.scores).toString() : 'N/A'],
            ["Average Score", data.scores ? Math.round((sumScores / data.scores.length)).toString() : 'N/A'],
            ["Top Player", "Bex"],
		]
		if ("streak" in data) {
			beckyStats.push(["Current Streak", data.streak]);
			beckyStats.push(["Max Streak", data.maxStreak]);
		}
	}
</script>

<h3>Statistics ({modeData.modes[$mode].name})</h3>
<div>
	{#each beckyStats as stat}
		<Stat name={stat[0]} stat={stat[1]} />
	{/each}
</div>


<style>
	div {
		display: flex;
		justify-content: center;
		gap: 15px;
	}
</style>
