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

	// functions

	/**
	 * @function createSlideshow
	 * @param {any} event
	 * @returns {Promise<void>}
	 */
	async function createSlideshow(event: any) {
		if (!pb.authStore.model) return;

		try {
			// create record
			const record = await pb.collection("slideshows").create({
				name: event.target.slidee_name.value.trim(),
				slide_details: {
					slides: ["<h1>New Slideshow</h1>"],
					styles: "slide#slide-1 h1 { place-self: center; }"
				},
				owner: pb.authStore.model.id
			});

			// open slide editor
			window.location.href = `/${host}/slides/${record.id}/view`;
		} catch {
			alert("Failed to create slideshow!");
		}
	}
</script>

<svelte:head>
	<title>Create Slideshow - Slidee</title>
</svelte:head>

{#if pb.authStore.model}
	<component>
		<nav>
			<div class="title">
				<a href="/{host}" title="Click to return to dashboard">Create Slideshow</a>
			</div>
		</nav>

		<main>
			<section>
				<form
					class="flex"
					style="flex-direction: column; gap: var(--u-2);"
					on:submit|preventDefault={createSlideshow}
				>
					<p class="form-label">Slideshow Name</p>

					<input
						type="text"
						name="slidee_name"
						placeholder="Slideshow Name"
						required
						maxlength="50"
						minlength="1"
					/>

					<button class="mt-4 primary">Create Slideshow</button>
				</form>
			</section>

			<Footer {host} />
		</main>
	</component>
{/if}

<style>
	button {
		width: max-content;
	}

	.form-label {
		user-select: none;
	}
</style>
