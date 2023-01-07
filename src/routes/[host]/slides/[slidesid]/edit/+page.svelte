<script lang="ts">
	import Slideshow from "$lib/components/slideshow/Slideshow.svelte";
	import Editor from "$lib/components/editor/Editor.svelte";
	import Footer from "$lib/components/Footer.svelte";

	import PocketBase, { Record } from "pocketbase";
	import type { PageData } from "./$types";
	import { onMount } from "svelte";

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
		if (!pb.authStore.model) return (window.location.href = `/${host}/auth`);

		try {
			// get slideshow
			slideshow = await pb.collection("slideshows").getOne(slidesid);
		} catch {
			// slideshow doesn't exist!
			window.location.href = `/${host}`;
		}
	});

	// functions

	let htmlEditor: Editor;
	let cssEditor: Editor;

	function syncSlideshow() {
		if (!htmlEditor.editor || !cssEditor.editor) return;

		slideshow.slide_details.slides = htmlEditor.editorValue!.split("<SlideSplit />\n\n");
		slideshow.slide_details.styles = cssEditor.editorValue;

		// force slideshow render
		hideSlideshow = true;

		setTimeout(() => {
			hideSlideshow = false;
		}, 1);
	}

	let hideSlideshow = false;
</script>

<svelte:head>
	<title>Edit "{slideshow.name}" - Slidee</title>
</svelte:head>

<group style="overflow: hidden; height: 100vh;">
	<Editor
		lang="HTML"
		width="24%"
		editor={undefined}
		value={slideshow.slide_details.slides.join("<SlideSplit />\n\n")}
		bind:this={htmlEditor}
	/>

	<Editor
		lang="CSS"
		width="24%"
		editor={undefined}
		value={slideshow.slide_details.styles}
		bind:this={cssEditor}
	/>

	{#if !hideSlideshow}
		<Slideshow
			record={slideshow}
			{host}
			options={{
				width: "51.5%", // 51.5% width because we're going to split the screen into thirds, 100 - (24 * 2) - 0.5
				sidebar: {
					isEditor: true,
					reloadFunction: syncSlideshow,
					saveFunction: async () => {
						try {
							await pb.collection("slideshows").update(slideshow.id, {
								slide_details: slideshow.slide_details
							});

							alert("Saved slideshow to server!");
						} catch {
							alert("Failed to save slideshow!");
						}
					},
					deleteFunction: async () => {
						try {
							await pb.collection("slideshows").delete(slideshow.id);
							window.location.href = `/${host}`;
						} catch {
							alert("Failed to save slideshow!");
						}
					}
				}
			}}
		/>
	{/if}
</group>

<style>
	group {
		display: flex;
		width: 100%;
	}
</style>
