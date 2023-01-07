<script lang="ts">
	import { Record } from "pocketbase";

	import { writable } from "svelte/store";
	import { onMount } from "svelte";

	export let sidebarOpen = writable(false);

	import Sidebar from "./Sidebar.svelte";
	import Slide from "./Slide.svelte";

	export let record: Record;
	export let host: string;

	export let options: any;

	let slideNumber = 1;
	let showSlide = true;

	// sync slideNumber to window.hash
	onMount(() => {
		function syncNumber() {
			slideNumber = parseFloat(window.location.hash.slice(1))
				? parseFloat(window.location.hash.slice(1))
				: (1 as number);

			// force slide render
			showSlide = false;

			setTimeout(() => {
				showSlide = true;
			}, 1);
		}

		syncNumber(); // initial sync
		window.addEventListener("hashchange", syncNumber); // recurring sync
	});

	// functions

	function nextSlide() {
		// increase slide number if we weren't at the end
		if (slideNumber < record.slide_details.slides.length) {
			window.location.hash = (slideNumber + 1).toString();
		}
	}

	function previousSlide() {
		// decrease slide number if we weren't at the end
		if (slideNumber > 1) {
			window.location.hash = (slideNumber - 1).toString();
		}
	}

	function slideKeyboardControl(event: KeyboardEvent) {
		if (options.sidebar.isEditor) return; // don't use keyboard for control in editor
		if (event.key === "ArrowLeft") previousSlide();
		else if (event.key === "ArrowRight") nextSlide();
	}
</script>

<svelte:body on:keydown={slideKeyboardControl} />

<component style="width: {options.width !== undefined ? options.width : '100%'};">
	<button
		class="slideshow-sidebar-button"
		on:click={() => {
			// toggle sidebar
			sidebarOpen.set(true);
		}}
	>
		<svg
			xmlns="http://www.w3.org/2000/svg"
			width="18"
			height="18"
			viewBox="0 0 24 24"
			fill="none"
			stroke="currentColor"
			stroke-width="2"
			stroke-linecap="round"
			stroke-linejoin="round"
			class="feather feather-sidebar"
			><rect x="3" y="3" width="18" height="18" rx="2" ry="2" /><line
				x1="9"
				y1="3"
				x2="9"
				y2="21"
			/></svg
		>
	</button>

	<Sidebar {record} {host} {sidebarOpen} {options} />

	{#if record.name !== ""}
		{#if options.sidebar.isEditor}
			<div class="slideNumberDisplay">
				<b>Slide: {slideNumber}/{record.slide_details.slides.length}</b>,
				<i>Create new slides with: &lt;Split/&gt;&lt;/Split&gt;</i>
			</div>
		{/if}

		<slideshow
			aria-label={record.name}
			on:mousedown={nextSlide}
			style="height: {options.height !== undefined ? options.height : '100vh'}"
		>
			{#if showSlide}
				<Slide {record} {slideNumber} />
			{:else}
				<!-- make sure we don't see a quick flash when changing slides -->
				<Slide
					record={new Record({
						name: "",
						slide_details: {
							slides: [""],
							styles: ""
						}
					})}
					slideNumber={1}
				/>
			{/if}
		</slideshow>

		<div class="slideshow-slide-controls">
			<button aria-label="Previous slide button" on:click={previousSlide}>
				<svg
					xmlns="http://www.w3.org/2000/svg"
					width="18"
					height="18"
					viewBox="0 0 24 24"
					fill="none"
					stroke="currentColor"
					stroke-width="2"
					stroke-linecap="round"
					stroke-linejoin="round"
					class="feather feather-arrow-left-circle"
					><circle cx="12" cy="12" r="10" /><polyline points="12 8 8 12 12 16" /><line
						x1="16"
						y1="12"
						x2="8"
						y2="12"
					/></svg
				>
			</button>

			<button aria-label="Next slide button" on:click={nextSlide}>
				<svg
					xmlns="http://www.w3.org/2000/svg"
					width="18"
					height="18"
					viewBox="0 0 24 24"
					fill="none"
					stroke="currentColor"
					stroke-width="2"
					stroke-linecap="round"
					stroke-linejoin="round"
					class="feather feather-arrow-right-circle"
					><circle cx="12" cy="12" r="10" /><polyline points="12 16 16 12 12 8" /><line
						x1="8"
						y1="12"
						x2="16"
						y2="12"
					/></svg
				>
			</button>
		</div>
	{/if}
</component>

<style>
	:root {
		--bg-surface: hsl(0, 0%, 30%);
		--bg-surface-low: hsl(0, 0%, 20%);
		--bg-surface-lower: hsl(0, 0%, 10%);
		--bg-surface-lowest: hsl(0, 0%, 0%);
	}

	slideshow {
		width: 100%;
		height: 100vh;
		display: block;
		background: white;
	}

	.slideshow-sidebar-button {
		opacity: 20%;
		position: absolute;
		display: grid;
		place-items: center;
		cursor: context-menu;
		z-index: 2;
		width: max-content;
		padding: 0.4rem;
		top: 0.4rem;
		left: 0.4rem;
	}

	.slideshow-sidebar-button:hover {
		opacity: 100%;
	}

	.slideshow-slide-controls {
		position: absolute;
		opacity: 50%;
		display: flex;
		gap: var(--u-2);
		top: 54.2rem;
		left: 0.4rem;
		z-index: 2;
		transition: all 0.2s;
	}

	.slideshow-slide-controls button {
		width: max-content;
	}

	.slideshow-slide-controls:hover {
		opacity: 100%;
	}

	.slideNumberDisplay {
		width: 100%;
		background: var(--bg-surface-low);
		color: white;
		box-shadow: -1px 0 4px var(--bg-surface-lower);
		padding: 0.4rem;
	}
</style>
