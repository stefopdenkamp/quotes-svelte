<script lang="ts">
	import ButtonSubmit from './ButtonSubmit.svelte';
	import ButtonCancel from './ButtonCancel.svelte';
	import ButtonDelete from './ButtonDelete.svelte';
	import ButtonEdit from './ButtonEdit.svelte';
	import ButtonUpdate from './ButtonUpdate.svelte';
	import type { TransitionConfig } from 'svelte/transition';

	let {
		quote = { id: 0, quote: '', quotee: '' },
		type = 'text',
		addQuote,
		deleteQuote,
		updateQuote
	} = $props();

	let quoteInput = $state('');
	let quoteeInput = $state('');
	let error = $state('');
	const animationDuration: number = 200;

	async function handleDelete() {
		await deleteQuote(quote.id);
	}

	function handleUpdate(update: boolean) {
		if (type === 'edit') {
			if (update) {
				if (!quoteInput.trim()) {
					error = 'The Quote field is required.';
					return;
				}

				if (quote.quote !== quoteInput || quote.quotee !== quoteeInput) {
					updateQuote(quote.id, quoteInput, quoteeInput);
				}

				error = '';
			}

			type = 'text';
		} else {
			quoteInput = quote.quote;
			quoteeInput = quote.quotee;
			type = 'edit';
			error = '';
		}
	}

	function handleSubmit(e: SubmitEvent) {
		e.preventDefault();

		if (!quoteInput.trim()) {
			console.log('error', error);
			error = 'The Quote field is required.';
			return;
		}

		addQuote(quoteInput, quoteeInput);
		quoteInput = '';
		quoteeInput = '';
		error = '';

		(document.querySelector('.focus-element') as HTMLInputElement | null)?.focus();
	}

	export function HeightExpandFadeIn(
		node: HTMLElement,
		{ delay = 0, duration = 500 }: { delay?: number; duration?: number } = {}
	): TransitionConfig {
		const height = node.scrollHeight;

		return {
			delay,
			duration,
			css: (t: number) => {
				const heightProgress = t < 0.45 ? t / 0.45 : 1;
				const currentHeight = height * heightProgress;

				const opacity = t > 0.55 ? (t - 0.55) / 0.4 : 0;

				return `
        height: ${currentHeight}px;
        opacity: ${opacity};
        overflow: hidden;
      `;
			}
		};
	}

	export function fadeOutHeightCollapse(
		node: HTMLElement,
		{ duration = 500 }: { duration?: number } = {}
	): TransitionConfig {
		const height = node.scrollHeight;

		return {
			duration,
			css: (t: number) => {
				const opacity = t > 0.55 ? (t - 0.55) / 0.4 : 0;

				const heightProgress = t < 0.45 ? t / 0.45 : 1;
				const currentHeight = height * heightProgress;

				return `
        height: ${currentHeight}px;
        opacity: ${opacity};
        overflow: hidden;
      `;
			}
		};
	}

	type Type = 'add' | 'edit' | 'text';
</script>

<li>
	<form onsubmit={handleSubmit}>
		{#if type === 'add' || type === 'edit'}
			<div
				class="input-container"
				in:HeightExpandFadeIn={{ delay: animationDuration * 0.55, duration: animationDuration }}
				out:fadeOutHeightCollapse={{ duration: animationDuration }}
			>
				<input
					type="text"
					bind:value={quoteInput}
					placeholder="Quote"
					class="focus-element italic"
				/>
				<input type="text" bind:value={quoteeInput} placeholder="Quotee" />
				{#if error}<div
						class="error-message"
						in:HeightExpandFadeIn={{ duration: 300 }}
						out:fadeOutHeightCollapse={{ duration: 300 }}
					>
						{error}
					</div>{/if}
			</div>
		{:else}
			<div
				class="quote-container"
				in:HeightExpandFadeIn={{ delay: animationDuration * 0.55, duration: animationDuration }}
				out:fadeOutHeightCollapse={{ duration: animationDuration }}
			>
				<div class="quote">{quote.quote}</div>
				<div class="quotee">{quote.quotee ? `- ${quote.quotee}` : ''}</div>
			</div>
		{/if}
		<div class="button-container">
			{#if type === 'add'}
				<ButtonSubmit />
			{:else if type === 'edit'}
				<ButtonUpdate
					onclick={() => {
						handleUpdate(true);
					}}
				/>
				<ButtonCancel
					onclick={() => {
						handleUpdate(false);
					}}
				/>
			{:else}
				<ButtonEdit
					onclick={() => {
						handleUpdate(false);
					}}
				/>
				<ButtonDelete onclick={handleDelete} />
			{/if}
		</div>
	</form>
</li>

<style>
	li {
		--quote-padding: 0.6rem;
		--min-height: 90px;

		width: 100%;
		list-style-type: none;
	}

	form {
		display: flex;
		justify-content: center;
		flex-direction: column;

		font-family: georgia, Helvetica, sans-serif;
	}

	.italic {
		font-style: italic;
	}

	.input-container {
		display: flex;
		justify-content: center;
		flex-direction: column;
	}

	input {
		margin-bottom: 0.5rem;
		padding: 0.5rem;

		border: rgb(25, 25, 25) 1px solid;
		border-radius: 0.8rem 0.2rem 0.8rem 0.2rem;
	}

	input:focus {
		outline: none;
		border: dotted black 3px;
	}

	.quote-container {
		display: flex;
		justify-content: center;
		flex-direction: column;

		border-top: solid 1px rgba(0, 0, 0, 0.3);
	}

	.quote {
		display: flex;
		justify-content: center;
		padding-top: var(--quote-padding);

		font-style: italic;
	}

	.quotee {
		display: flex;
		justify-content: center;
		padding: var(--quote-padding);
	}

	.button-container {
		display: flex;
		justify-content: center;
		min-height: 50px;
		width: 100%;
	}

	.error-message {
		display: flex;
		justify-content: center;
		padding: 0.8rem;
		color: gray;
	}
</style>
