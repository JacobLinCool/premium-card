<script lang="ts">
	import Card from "./Card.svelte";
	import { page } from "$app/stores";
	import { browser } from "$app/environment";

	let membership =
		((browser && $page.url.searchParams.get("membership")) ?? "CSIE Premium Club") || "";
	let org = ((browser && $page.url.searchParams.get("org")) ?? "NTNU CSIE SA 39th") || "";
	let owner = ((browser && $page.url.searchParams.get("owner")) ?? "Jacob Lin") || "";
	let id = ((browser && $page.url.searchParams.get("id")) ?? "41047029S") || "";
	let expiry = ((browser && $page.url.searchParams.get("expiry")) ?? "2023.07 ~ 2024.06") || "";
	let font = ((browser && $page.url.searchParams.get("font")) ?? "Foldit") || "";
	let color = parseInt((browser && $page.url.searchParams.get("color")) || "0") || 0;
</script>

<svelte:head>
	<title>Premium Card</title>
	<meta name="description" content="Premium Card" />
	<meta name="og:image" content="https://jacoblincool.github.io/premium-card/screenshot.png" />
	<meta name="twitter:card" content="summary_large_image" />
</svelte:head>

<div class="flex h-full w-full items-center justify-center bg-zinc-950">
	<a href="https://github.com/JacobLinCool/premium-card" target="_blank" class="contents">
		<Card {membership} {org} {owner} {id} {expiry} {font} {color} />
	</a>
</div>
