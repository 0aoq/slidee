<script lang="ts">
	import type { Record } from "pocketbase";
	import type { Writable } from "svelte/store";

	export let sidebarOpen: Writable<boolean>;
	export let record: Record;
	export let host: string;

	export let options: any;

	let openState = false;
	sidebarOpen.subscribe((v) => (openState = v));
</script>

<component>
	<div
		class="slideshow-sidebar"
		style="left: {openState === true ? '0%' : '-100%'}; top: 0%;"
		aria-label="Slideshow options sidebar"
	>
		<div class="top">
			<div>
				<b>{record.name}</b>
			</div>

			<button
				style="width: max-content; padding: 0.4rem;"
				on:click={() => {
					// toggle sidebar
					sidebarOpen.set(false);
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
					class="feather feather-x-circle"
					><circle cx="12" cy="12" r="10" /><line x1="15" y1="9" x2="9" y2="15" /><line
						x1="9"
						y1="9"
						x2="15"
						y2="15"
					/></svg
				>
			</button>
		</div>

		{#if !options.sidebar.isEditor}
			<a class="option" aria-label="Close slideshow button" href="/{host}">
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
					class="feather feather-x-square"
					><rect x="3" y="3" width="18" height="18" rx="2" ry="2" /><line
						x1="9"
						y1="9"
						x2="15"
						y2="15"
					/><line x1="15" y1="9" x2="9" y2="15" /></svg
				>

				Close Slideshow
			</a>

			{#if options.sidebar.isOwner}
				<a class="option" aria-label="Edit slideshow button" href="/{host}/slides/{record.id}/edit">
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
						class="feather feather-edit"
						><path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7" /><path
							d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"
						/></svg
					>

					Edit Slideshow
				</a>
			{/if}
		{:else}
			<a class="option" aria-label="View slideshow button" href="/{host}/slides/{record.id}/view"
				><svg
					xmlns="http://www.w3.org/2000/svg"
					width="18"
					height="18"
					viewBox="0 0 24 24"
					fill="none"
					stroke="currentColor"
					stroke-width="2"
					stroke-linecap="round"
					stroke-linejoin="round"
					class="feather feather-eye"
					><path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z" /><circle
						cx="12"
						cy="12"
						r="3"
					/></svg
				>

				View Slideshow
			</a>

			<div
				class="option"
				aria-label="Sync slideshow button"
				on:mousedown={() => {
					if (options.sidebar.reloadFunction) options.sidebar.reloadFunction();
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
					class="feather feather-refresh-cw"
					><polyline points="23 4 23 10 17 10" /><polyline points="1 20 1 14 7 14" /><path
						d="M3.51 9a9 9 0 0 1 14.85-3.36L23 10M1 14l4.64 4.36A9 9 0 0 0 20.49 15"
					/></svg
				>

				Sync Slideshow
			</div>

			<div
				class="option"
				aria-label="Sync slideshow button"
				on:mousedown={() => {
					if (options.sidebar.saveFunction) options.sidebar.saveFunction();
					else alert("Failed to save slideshow!");
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
					class="feather feather-save"
					><path d="M19 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h11l5 5v11a2 2 0 0 1-2 2z" /><polyline
						points="17 21 17 13 7 13 7 21"
					/><polyline points="7 3 7 8 15 8" /></svg
				>

				Save
			</div>

			<div
				class="option"
				aria-label="Sync slideshow button"
				on:mousedown={() => {
					if (options.sidebar.deleteFunction) options.sidebar.deleteFunction();
					else alert("Failed to delete slideshow!");
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
					class="feather feather-trash-2"
					><polyline points="3 6 5 6 21 6" /><path
						d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"
					/><line x1="10" y1="11" x2="10" y2="17" /><line x1="14" y1="11" x2="14" y2="17" /></svg
				>

				Delete
			</div>
		{/if}
	</div>
</component>

<style>
	.slideshow-sidebar {
		z-index: 100;
		height: 100vh;
		position: absolute;
		background: var(--bg-surface);
		width: var(--u-80);
		transition: all 0.2s;
		display: flex;
		flex-direction: column;
		gap: var(--u-2);
		color: white;
	}

	.slideshow-sidebar a {
		color: white;
		text-decoration: none;
	}

	.slideshow-sidebar .top {
		background: var(--bg-surface-low);
		padding: 0.6rem 0.8rem;
		display: flex;
		justify-content: space-between;
		align-items: center;
		/* border-bottom: 1px solid var(--bg-surface-lower); */
		box-shadow: -1px 0 4px var(--bg-surface-lower);
	}

	.slideshow-sidebar .option {
		padding: 0.4rem 0.8rem;
		transition: all 0.1s;
		cursor: pointer;
		border-left: 0 solid transparent;
		display: flex;
		gap: var(--u-4);
		align-items: center;
	}

	.slideshow-sidebar .option:hover {
		background: var(--bg-surface-low);
		border-left: 5px solid var(--bg-surface-lower);
	}
</style>
