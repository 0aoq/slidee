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
		if (!content) content = "";

		// sanitize strings
		content = content.replaceAll("<", "&lt;").replaceAll(">", "&gt;");

		// allow certain tags
		const allowedTags = [
			// svg
			"svg",
			"path",
			"circle",
			"path",
			"rect",
			"line",
			"polyline",

			// misc
			"button",
			"group",
			"div",

			// text
			"h1",
			"h2",
			"h3",
			"h4",
			"h5",
			"span",
			"strong",
			"em",
			"b", // must come after button or it breaks regex
			"i",
			"p",
			"a",

			// media
			"video",
			"img",
			"embed",
			"iframe"
		];

		for (let tag of allowedTags) {
			content = content.replaceAll(
				new RegExp(`(&lt;)${tag}(?<ATTRS>.*?)(&gt;)`, "gm"),
				`<${tag} $<ATTRS>>`
			);

			content = content.replaceAll(`&lt;/${tag}&gt;`, `</${tag}>`);
		}

		// allow comments
		content = content.replaceAll(/(&lt;)\!\-\-(.*?)\-\-(&gt;)/g, "");

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
