<script lang="ts">
	export let host: string;
</script>

<section class="flex justify-center align-center mt-4">
	<span>
		<span class="mr-4">&lbrack;<a href="/{host}">{host}</a>&rbrack;</span>
		<a href="https://github.com/0aoq/slidee" class="chip">Slidee</a> - Rendered {new Date().toLocaleString()}
	</span>
</section>
