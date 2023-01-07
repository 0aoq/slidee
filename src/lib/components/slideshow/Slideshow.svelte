<script lang="ts">
	import type { Record } from "pocketbase";

	import { writable } from "svelte/store";
	export let sidebarOpen = writable(false);

	import Sidebar from "./Sidebar.svelte";
	import Slide from "./Slide.svelte";

	export let record: Record;
	export let host: string;

	export let options: any;

	let slideNumber = 1;
</script>

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
		<slideshow
			aria-label={record.name}
			on:mousedown={() => {
				if (slideNumber < record.slide_details.slides.length) slideNumber++;
			}}
		>
			<Slide {record} {slideNumber} />
		</slideshow>
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
</style>
