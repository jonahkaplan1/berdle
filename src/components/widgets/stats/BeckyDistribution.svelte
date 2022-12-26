<script lang="ts">
	import { failed } from "../../../utils";

	export let game: GameState;
	export let distribution: ScoreGroups;

	$: max = Object.entries(distribution).reduce((p, c) => {
		return Math.max(c[1], p);
	}, 1);
</script>

<h3>guess distribution</h3>
<div class="container">
	{#each Object.entries(distribution) as guess, i (guess[0])}
		{@const g = guess[0]}
			<div class="graph">
				<span class="guess">{guess[0]}</span>
				<div
					class="bar"
					class:this={!game.active && !failed(game)}
					style="width: {(guess[1] / max) * 100}%;"
				>
					{guess[1] > 0 ? guess[1] : ''}
				</div>
			</div>
	{/each}
</div>

<style>
	.guess {
		display: grid;
		place-items: left;
        min-width: 14%;
	}
	.container {
		width: 95%;
		margin-left: auto;
		margin-right: auto;
		display: flex;
		flex-direction: column;
		gap: 4px;
	}
	.graph {
		height: 20px;
		display: flex;
		gap: 20px;
	}
	.bar {
		min-width: 2%;
		transition: width 0.3s ease-out;
		background: var(--color-absent);
		color: white;
		font-weight: bold;
		display: flex;
		justify-content: end;
		align-items: center;
		padding-right: min(0.8rem, 1vw);
	}
	.bar.this {
		background: var(--color-correct);
	}
</style>
