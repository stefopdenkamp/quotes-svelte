<script lang="ts">
	let { onclick, width = '2.5rem' } = $props();
</script>

<button type="button" {onclick} style:width aria-label="settings">
	<svg
		width="100%"
		height="100%"
		viewBox="0 0 24 24"
		fill="none"
		xmlns="http://www.w3.org/2000/svg"
	>
		<g id="Edit / Add_Plus">
			<path
				id="Vector"
				d="M6 12H12M12 12H18M12 12V18M12 12V6"
				stroke="currentColor"
				stroke-width="2"
				stroke-linecap="round"
				stroke-linejoin="round"
			/>
		</g>
	</svg>
</button>

<style>
	button {
		aspect-ratio: 1 / 1;
		margin: 0rem 0.5rem;

		background-color: transparent;
		border: transparent solid 1px;

		cursor: pointer;

		display: flex;
		align-items: center;
		justify-content: center;
	}

	svg {
		transition:
			transform 0.4s ease-in-out,
			color 0.2s;
		transform: rotate(45deg);
	}

	button:hover > svg {
		transform: rotate(225deg);
	}

	button:active > svg {
		color: rgb(80, 80, 80);
	}
</style>
