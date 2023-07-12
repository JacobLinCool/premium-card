<script lang="ts">
	import { browser } from "$app/environment";
	import { onDestroy, onMount } from "svelte";
	import { cubicOut } from "svelte/easing";
	import { tweened } from "svelte/motion";
	import { derived, writable } from "svelte/store";

	export let membership = "CSIE Premium Club";
	export let org = "NTNU CSIE SA 39th";
	export let owner = "Jacob Lin";
	export let id = "41047029S";
	export let expiry = "2023.07 ~ 2024.06";
	export let font = "Foldit";
	export let color = 0;

	let card: HTMLDivElement;
	let light: HTMLDivElement;

	const x = tweened(0.5, { duration: 200, easing: cubicOut });
	const y = tweened(0.5, { duration: 200, easing: cubicOut });
	const transform = derived([x, y], ([x, y]) => {
		const y_rotate = (x - 0.5) * 2;
		const x_rotate = -(y - 0.5) * 2;
		const max = 10;
		return `perspective(1000px) rotateX(${x_rotate * max}deg) rotateY(${y_rotate * max}deg)`;
	});
	const light_pos = derived([x, y], ([x, y]) => {
		return `left: calc( ${x * 100}% - 10rem ); top: calc( ${y * 100}% - 10rem );`;
	});

	const hue = writable(color);

	let ready = false;

	let hue_interval: number;
	onMount(async () => {
		await document.fonts.load("16px 'Dancing Script'");
		ready = true;

		card.addEventListener("mousemove", mouse_update);
		card.addEventListener("touchmove", mouse_update);
		if (color < 0) {
			hue_interval = requestAnimationFrame(update_color);
		}
	});
	onDestroy(() => {
		if (browser) {
			card.removeEventListener("mousemove", mouse_update);
			card.removeEventListener("touchmove", mouse_update);
			if (hue_interval) {
				cancelAnimationFrame(hue_interval);
			}
		}
	});

	function mouse_update(e: MouseEvent | TouchEvent) {
		if (e instanceof MouseEvent) {
			x.set(e.offsetX / card.clientWidth);
			y.set(e.offsetY / card.clientHeight);
		}

		if (e instanceof TouchEvent && e.target && e.touches.length > 0) {
			const rect = (e.target as HTMLDivElement).getBoundingClientRect();
			x.set((e.touches[0].clientX - rect.left) / card.clientWidth);
			y.set((e.touches[0].clientY - rect.top) / card.clientHeight);
		}
	}

	function update_color() {
		hue.set(($hue + 1) % 360);
		hue_interval = requestAnimationFrame(update_color);
	}
</script>

{#if font}
	<link
		href="https://fonts.googleapis.com/css2?family={font.replace(/\s/, '+')}&display=swap"
		rel="stylesheet"
	/>
{/if}

<div
	bind:this={card}
	class="flex h-48 w-80 cursor-none select-none flex-col items-center justify-center overflow-hidden rounded-xl bg-gradient-to-br from-zinc-800 to-zinc-900 shadow-lg"
	class:hidden={!ready}
	style="transform: {$transform}; font-family: '{font}', cursive;"
>
	<div class="pointer-events-none absolute left-0 top-0 p-4">
		<div
			class="text-lg text-zinc-400/80 drop-shadow-md sepia"
			style="--tw-hue-rotate: hue-rotate({$hue}deg); filter: var(--tw-sepia) var(--tw-hue-rotate) var(--tw-drop-shadow)"
		>
			{membership}
		</div>
	</div>

	<div class="pointer-events-none flex w-full items-center justify-center gap-4 p-4">
		<div
			class="flex-1 text-right text-2xl text-zinc-400 drop-shadow-md sepia"
			style="--tw-hue-rotate: hue-rotate({$hue}deg); filter: var(--tw-sepia) var(--tw-hue-rotate) var(--tw-drop-shadow)"
		>
			<div>{owner}</div>
			<div class="text-xs">{id}</div>
		</div>
		<div
			class="flex-1 text-xs text-zinc-400 drop-shadow-md sepia"
			style="--tw-hue-rotate: hue-rotate({$hue}deg); filter: var(--tw-sepia) var(--tw-hue-rotate) var(--tw-drop-shadow)"
		>
			{expiry}
		</div>
	</div>

	<div class="pointer-events-none absolute bottom-0 right-0 p-4">
		<div
			class="text-xs text-zinc-400/80 drop-shadow-md sepia"
			style="--tw-hue-rotate: hue-rotate({$hue}deg); filter: var(--tw-sepia) var(--tw-hue-rotate) var(--tw-drop-shadow)"
		>
			{org}
		</div>
	</div>

	<div
		bind:this={light}
		class="pointer-events-none absolute h-80 w-80 rounded-full"
		style="background-image: radial-gradient(#ffffff08 0%, #ffffff08 10%, transparent 50%); {$light_pos}"
	/>
</div>
