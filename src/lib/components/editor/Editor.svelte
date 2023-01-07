<svelte:options accessors />

<script lang="ts">
	import { onMount } from "svelte";

	export let lang: "HTML" | "CSS";
	export let width: string;
	export let value: string;

	// import codemirror
	import { basicSetup } from "codemirror";
	import { EditorView, keymap } from "@codemirror/view";
	import { indentWithTab } from "@codemirror/commands";

	import { oneDarkTheme } from "@codemirror/theme-one-dark";

	import { css } from "@codemirror/lang-css";
	import { html } from "@codemirror/lang-html";

	// create editor
	let editorElement: HTMLElement;

	export let editorValue: string = "";
	export let editor: EditorView | undefined;

	onMount(() => {
		editorElement.attachShadow({ mode: "open" });

		editorElement.shadowRoot!.innerHTML += `<style>
            .cm-editor {
                height: 100vh;
            }
        </style>`;

		setTimeout(() => {
			editor = new EditorView({
				extensions: [
					basicSetup,
					keymap.of([indentWithTab]),
					lang === "HTML" ? html() : css(),
					EditorView.updateListener.of((event) => {
						editorValue = event.state.doc.toString();
					}),
					oneDarkTheme
				],
				doc: value,
				parent: editorElement.shadowRoot as any
			});
		}, 100);
	});
</script>

<div bind:this={editorElement} style="width: {width};" />

<style>
	div {
		max-height: 100vh;
		overflow: auto;
		border: 1px solid var(--bg-surface-low);
		box-shadow: 0 0 8px rgba(0, 0, 0, 0.25);
	}
</style>
