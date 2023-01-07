<script lang="ts">
	import Footer from "$lib/components/Footer.svelte";

	import PocketBase, { Record } from "pocketbase";
	import type { PageData } from "./$types";
	import { onMount } from "svelte";
	import Slideshow from "$lib/components/slideshow/Slideshow.svelte";

	export let data: PageData;
	// @ts-ignore
	export const { host, slidesid } = data;

	// create pocketbase client
	let pb = new PocketBase(`http://${host}`);
	let slideshow: Record = new Record({
		name: "",
		slide_details: {
			slides: [""],
			styleS: ""
		}
	});

	onMount(async () => {
		if (window.location.protocol === "https:") pb = new PocketBase(`https://${host}`);
		// if (!pb.authStore.model) return (window.location.href = `/${host}/auth`);

		try {
			// get slideshow
			slideshow = await pb.collection("slideshows").getOne(slidesid);
		} catch {
			// slideshow doesn't exist!
			window.location.href = `/${host}`;
		}
	});
</script>

<svelte:head>
	<title>{slideshow.name}</title>
</svelte:head>

<Slideshow
	record={slideshow}
	{host}
	options={{
		sidebar: {
			isEditor: false,
			isOwner: pb.authStore.model && pb.authStore.model.id === slideshow.owner
		}
	}}
/>

<style>
</style>
