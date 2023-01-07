<script lang="ts">
	import { onMount } from "svelte";
	import type { Record } from "pocketbase";

	export let record: Record;
	export let slideNumber: number;
	let content = "";

	/**
	 * @function cleanContent
	 * @param {string} content
	 */
	function cleanContent(content: string) {
		// sanitize strings
		content = content.replaceAll("<", "&lt;").replaceAll(">", "&gt;");

		// allow certain tags
		const allowedTags = ["h1", "h2", "h3", "h4", "h5", "text", "b", "i", "strong", "em", "button", "group"];

		for (let tag of allowedTags) {
			content = content.replaceAll(`&lt;${tag}&gt;`, `<${tag}>`);
			content = content.replaceAll(`&lt;/${tag}&gt;`, `</${tag}>`);
		}

		// return
		return content;
	}

	let styleElement: HTMLStyleElement; // we need to use bind:this to link these because style elements don't support svelte variables
	onMount(() => {
		// clean content on mount
		content = cleanContent(record.slide_details.slides[slideNumber - 1]);

		// load css
		styleElement.innerHTML = '@import url("/slide-styles.css");\n' + record.slide_details.styles;
	});
</script>

<slide data-slide-number={slideNumber} id="slide-{slideNumber}">
	<!-- slides are written and saved in HTML -->
	{@html content}

	<!-- load styles -->
	<style bind:this={styleElement}></style>
</slide>

<style>
	slide {
		display: grid;
		height: 100vh;
	}
</style>
