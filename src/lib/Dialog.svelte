<script lang="ts">
	import { fade } from 'svelte/transition';
	import ButtonCancel from './ButtonCancel.svelte';

	let { visible, children, toggleDialog } = $props();

	function setFocus() {
		(document.querySelector('.focus-element') as HTMLInputElement | null)?.focus();
	}
</script>

{#if visible}
	<div class="dialog" transition:fade={{ duration: 150 }} onintroend={setFocus}>
		{@render children()}
		<div class="button-container">
			<ButtonCancel onclick={toggleDialog} width="3rem" />
		</div>
	</div>
{/if}

<style>
	.dialog {
		position: absolute;
		inset: 10%;
		width: auto;
		height: auto;
		overflow-y: scroll;

		background-color: hsl(40, 73%, 84%);
		border: solid black 3px;
		border-radius: 1rem 0.5rem 1rem 0.5rem;
		padding: 1rem 1.5rem;

		display: flex;
		flex-direction: column;
	}

	/* Scrollbar */
	.dialog {
		scrollbar-color: rgba(0, 0, 0, 0.3) transparent;
	}
	.dialog::-webkit-scrollbar {
		width: 0.8rem;
	}

	.dialog::-webkit-scrollbar-track {
		background-color: transparent;
		border-radius: 1.2rem;
	}

	.dialog::-webkit-scrollbar-thumb {
		background-color: rgba(0, 0, 0, 0.3);
		border-radius: 1.2rem;
	}
	/* Scrollbar end */

	.button-container {
		display: flex;
		justify-content: center;
		width: 100%;
		margin-top: auto;
		padding-top: 2rem;

		border-top: solid 1px rgba(0, 0, 0, 0.3);
	}
</style>
