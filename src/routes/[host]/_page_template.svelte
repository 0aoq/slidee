<script lang="ts">
	import Footer from "$lib/components/Footer.svelte";

	import PocketBase, { Record } from "pocketbase";
	import type { PageData } from "./$types";
	import { onMount } from "svelte";

	export let data: PageData;
	// @ts-ignore
	export const { host } = data;

	// create pocketbase client
	let pb = new PocketBase(`http://${host}`);

	onMount(async () => {
		if (window.location.protocol === "https:") pb = new PocketBase(`https://${host}`);
		if (!pb.authStore.model) return (window.location.href = `/${host}/auth`);
	});
</script>

<component>
	<main>
		<Footer {host} />
	</main>
</component>

<style>
	button {
		width: max-content;
	}
</style>
