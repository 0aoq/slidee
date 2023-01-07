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
	let allSlideshows: Array<Record> = [];

	onMount(async () => {
		if (window.location.protocol === "https:") pb = new PocketBase(`https://${host}`);
		if (!pb.authStore.model) return (window.location.href = `/${host}/auth`);

		// fetch all slideshows that our user owns
		const slideshows = await pb.collection("slideshows").getList(1, 64 * 5, {
			sort: "-created", // newest first
			filter: `owner = "${pb.authStore.model.id}"`
		});

		allSlideshows = slideshows.items;
	});
</script>

<svelte:head>
	<title>Slidee!</title>
</svelte:head>

{#if pb.authStore.model}
	<component>
		<main>
			<!-- user actions -->
			<section class="mb-4">
				<span class="flex align-center" aria-label="(User Icon) Actions:">
					<svg
						xmlns="http://www.w3.org/2000/svg"
						width="18"
						height="18"
						viewBox="0 0 24 24"
						fill="none"
						stroke="var(--primary)"
						stroke-width="2"
						stroke-linecap="round"
						stroke-linejoin="round"
						class="feather feather-user"
						><path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2" /><circle
							cx="12"
							cy="7"
							r="4"
						/></svg
					>

					<span class="ml-2">Actions:</span>
				</span>

				<div
					class="flex align-center mt-2"
					style="gap: var(--u-2);"
					aria-label="Sign out button box"
				>
					<button
						on:click={() => {
							pb.authStore.clear();
							window.location.href = `/${host}/auth`;
						}}>Sign Out</button
					>
				</div>
			</section>

			<!-- main actions -->
			<section class="mb-4">
				<span class="flex align-center" aria-label="(User Icon) Actions:">
					<svg
						xmlns="http://www.w3.org/2000/svg"
						width="18"
						height="18"
						viewBox="0 0 24 24"
						fill="none"
						stroke="var(--primary)"
						stroke-width="2"
						stroke-linecap="round"
						stroke-linejoin="round"
						class="feather feather-menu"
						><line x1="3" y1="12" x2="21" y2="12" /><line x1="3" y1="6" x2="21" y2="6" /><line
							x1="3"
							y1="18"
							x2="21"
							y2="18"
						/></svg
					>

					<span class="ml-2">Actions:</span>
				</span>

				<div
					class="flex align-center mt-2"
					style="gap: var(--u-2);"
					aria-label="'Create Slideshow' button box"
				>
					<button
						class="primary"
						on:click={() => {
							window.location.href = `/${host}/slides/new`;
						}}>Create Slideshow</button
					>
				</div>
			</section>

			<!-- slideshow view -->
			<section aria-label="Main slideshow dashboard">
				<h2>Slideshows</h2>

				<div class="flex" style="gap: var(--u-2);">
					{#each allSlideshows as slideshow}
						<a href="/{host}/slides/{slideshow.id}/view" class="chip">{slideshow.name}</a>
					{:else}
						<p>No slideshows! <a href="/{host}/slides/new">Create one</a> to get started</p>
					{/each}
				</div>
			</section>

			<Footer {host} />
		</main>
	</component>
{/if}

<style>
	button {
		width: max-content;
	}
</style>
