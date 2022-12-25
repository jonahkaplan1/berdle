<script lang="ts">
	import { getRowData, words } from "../../utils";

	import Row from "./Row.svelte";
	import ContextMenu from "../widgets/ContextMenu.svelte";
	import { createEventDispatcher } from "svelte";
	import { scale } from "svelte/transition";

	export let value: string[];
	export let board: GameBoard;
	export let guesses: number;
	export let icon: string;
	export let svgIconPath: string;
	export let tutorial: boolean;
	export function shake(row: number) {
		rows[row].shake();
	}
	export function bounce(row: number) {
		rows[row].bounce();
	}
	export function hideCtx(e?: MouseEvent) {
		if (!e || !e.defaultPrevented) showCtx = false;
	}
	const dispatch = createEventDispatcher();

	let rows: Row[] = [];
	let showCtx = false;
	let pAns = 0;
	let pSols = 0;
	let x = 0;
	let y = 0;
	let word = "";

	function context(cx: number, cy: number, num: number, val: string) {
		if (guesses >= num) {
			x = cx;
			y = cy;
			showCtx = true;
			word = guesses > num ? val : "";

			const match = getRowData(num, board);
			pAns = words.words.filter((w) => match(w)).length;
			pSols = pAns + words.valid.filter((w) => match(w)).length;
		}
	}
</script>

{#if showCtx}
	<ContextMenu {pAns} {pSols} {x} {y} {word} />
{/if}

<div class="board">
	{#each value as _, i}
		<Row
			num={i}
			{guesses}
			bind:this={rows[i]}
			bind:value={value[i]}
			state={board.state[i]}
			on:ctx={(e) => context(e.detail.x, e.detail.y, i, value[i])}
		/>
	{/each}
	{#if icon}
		<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 200 200" fill="none">
			<path d={icon} stroke-width="14" />
		</svg>
	{/if}
	{#if svgIconPath}
		<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 392.619 392.619" fill="none">
			<path d="M310.723,0.929H81.896C36.738,0.929,0,37.667,0,82.825v226.969c0,45.158,36.738,81.896,81.896,81.896h228.827
			c45.158,0,81.896-36.738,81.896-81.896V82.825C392.619,37.667,355.881,0.929,310.723,0.929z M362.619,309.794
			c0,28.616-23.28,51.896-51.896,51.896H81.896C53.28,361.69,30,338.41,30,309.794V82.825c0-28.615,23.28-51.896,51.896-51.896
			h228.827c28.616,0,51.896,23.28,51.896,51.896V309.794z" stroke-width="14" />
			<path d="M211.056,105.476h-81.993v35h10.334v111.667h-10.334v35h81.993c29.868,0,54.167-24.299,54.167-54.167
			c0-14.132-5.445-27.012-14.341-36.667c8.896-9.655,14.341-22.535,14.341-36.667C265.223,129.775,240.924,105.476,211.056,105.476z
			 M211.056,140.476c10.568,0,19.167,8.598,19.167,19.167c0,10.568-8.599,19.167-19.167,19.167h-36.659v-38.333H211.056z
			 M211.056,252.143h-36.659V213.81h36.659c10.568,0,19.167,8.598,19.167,19.167C230.223,243.545,221.624,252.143,211.056,252.143z" stroke-width="14" />
		</svg>`
	{/if}
	{#if tutorial}
		<div transition:scale class="tutorial" on:click={() => dispatch("closeTutPopUp")}>
			double tap (right click) a row to see a word's definition, or how many words could be
			played there
			<span class="ok">OK</span>
		</div>
	{/if}
</div>

<style>
	.board {
		display: grid;
		grid-template-rows: repeat(var(--rows), 1fr);
		gap: 5.5px;
		max-height: 420px;
		flex-grow: 1;
		aspect-ratio: var(--cols) / var(--rows);
		padding: 10px;
		position: relative;
	}
	svg {
		position: absolute;
		z-index: -1;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		width: min(130%, 100vw);
		max-height: 100%;
	}
	path {
		stroke: var(--bg-secondary);
	}
	.tutorial {
		top: calc(100 / var(--rows) * 1%);
	}
</style>
