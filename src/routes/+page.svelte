<script lang="ts">
	import { onDestroy, onMount } from 'svelte';
	import Quote from '../lib/Quote.svelte';
	import Dialog from '../lib/Dialog.svelte';
	import ButtonSettings from '$lib/ButtonSettings.svelte';
	import ButtonRefresh from '$lib/ButtonRefresh.svelte';
	import { fade } from 'svelte/transition';
	import { flip } from 'svelte/animate';

	const endpoint = import.meta.env.VITE_API_ENDPOINT;

	let isDialogVisible = $state(false);
	let quotes: Quote[] = $state([]);
	let randomQuote: Quote = $state({
		id: 0,
		quote: '',
		quotee: ''
	});
	let fadeIn = $state(false);
	let apiFound: ApiStatus = $state('waiting');

	function toggleDialog() {
		isDialogVisible = !isDialogVisible;
	}

	onMount(() => {
		initKeyboardShortcuts();
		fadeIn = true;
		(async () => {
			await getAllQuotes();
			selectRandomQuote();
		})();
	});

	function initKeyboardShortcuts() {
		const handler = (e: KeyboardEvent) => {
			if (e.key === 'Escape') {
				isDialogVisible = false;
				return;
			}

			if (isDialogVisible) return;

			if (e.key.toLowerCase() === 'r') {
				selectRandomQuote();
			}

			if (e.key.toLowerCase() === 'e') {
				toggleDialog();
			}
		};

		window.addEventListener('keyup', handler);

		onDestroy(() => {
			window.removeEventListener('keyup', handler);
		});
	}

	// GET all quotes
	async function getAllQuotes() {
		try {
			const response = await fetch(`${endpoint}/quotes`, {
				method: 'GET',
				headers: {
					Authorization: 'Bearer stefsquoteapi'
				}
			});
			const data = await response.json();

			quotes =
				data.length > 0
					? data
					: [
							{ quote: 'He who builds an api has fun', quotee: '' },
							{
								quote: 'A quote for testing purposes convays little meaning',
								quotee: 'Not Albert Einstein'
							}
						];

			apiFound = response.ok ? 'found' : 'not-found';
			console.log('apiFound', apiFound);
		} catch (error) {
			console.error('Error fetching quotes:', error);

			apiFound = 'not-found';
			console.log('apiFound', apiFound);
		}
	}

	// Select random quote
	function selectRandomQuote() {
		randomQuote = quotes[Math.floor(Math.random() * quotes.length)];
	}

	// POST quote
	async function addQuote(quoteValue: string, quoteeValue: string) {
		const newQuote = { quote: quoteValue, quotee: quoteeValue };
		try {
			const response = await fetch(`${endpoint}/quote`, {
				method: 'POST',
				headers: {
					'Content-Type': 'application/json',
					Authorization: 'Bearer stefsquoteapi'
				},
				body: JSON.stringify(newQuote)
			});

			if (!response.ok) {
				throw new Error('Failed to add quote');
			}
			getAllQuotes();
		} catch (error) {
			console.error('Error adding quote:', error);
		}
	}

	// UPDATE quote
	async function updateQuote(id: number, quoteValue: string, quoteeValue: string) {
		const updatedQuote = { quote: quoteValue, quotee: quoteeValue };

		// validate for error message

		try {
			await fetch(`${endpoint}/quote/${id}`, {
				method: 'PATCH',
				headers: {
					'Content-Type': 'application/json',
					Authorization: 'Bearer stefsquoteapi'
				},
				body: JSON.stringify(updatedQuote)
			});

			quotes = quotes.map((quote) =>
				quote.id === id ? { ...quote, quote: quoteValue, quotee: quoteeValue } : quote
			);
		} catch (error) {
			console.error('Error updating quote:', error);
		}
	}

	// DELETE quote
	async function deleteQuote(id: number) {
		await fetch(`${endpoint}/quote/${id}`, {
			method: 'DELETE',
			headers: {
				'Content-Type': 'application/json',
				Authorization: 'Bearer stefsquoteapi'
			}
		});
		quotes = quotes.filter((q) => q.id !== id);
	}

	type Quote = {
		id: number;
		quote: string;
		quotee?: string;
	};

	type ApiStatus = 'found' | 'not-found' | 'waiting';
</script>

<main>
	<div class="random-quote-container">
		{#if apiFound === 'found'}
			{#key randomQuote.id}
				<div
					class="main-quote"
					out:fade={{ duration: 200 }}
					in:fade={{ duration: 200, delay: 300 }}
				>
					<div class="main-quote-wrapper">
						<span class="quotation-mark-before" class:fade-in={fadeIn}>“</span>
						<div class="random-quote">{randomQuote.quote}</div>
						<span class="quotation-mark-after" class:fade-in={fadeIn}>”</span>
					</div>
					<div class="random-quotee">{randomQuote.quotee ? `- ${randomQuote.quotee}` : ''}</div>
				</div>
			{/key}
		{:else if apiFound === 'not-found'}
			<div class="not-found">API not found</div>
		{:else}{/if}

		<div class="button-container">
			<ButtonRefresh onclick={selectRandomQuote} />
			<ButtonSettings onclick={toggleDialog} />
		</div>
	</div>

	<Dialog visible={isDialogVisible} {toggleDialog}>
		<div class="quotes-list">
			<div>
				<ul>
					<Quote type="add" {addQuote} {deleteQuote} {updateQuote} />
					{#each quotes as quote (quote.id)}
						<div
							in:fade={{ delay: 150, duration: 150 }}
							out:fade={{ duration: 150 }}
							animate:flip={{ duration: 150 }}
						>
							<Quote {quote} {addQuote} {deleteQuote} {updateQuote} />
						</div>
					{/each}
				</ul>
			</div>
		</div>
	</Dialog>
</main>

<style>
	.random-quote-container {
		display: flex;
		justify-content: center;
		align-items: center;
		flex-direction: column;

		min-height: 100vh;
		background-image: url('/background.png');
		background-color: hsl(40, 70%, 80%);

		position: relative;
	}

	.not-found {
		font-size: 3rem;
	}

	.main-quote {
		position: absolute;
	}

	.main-quote-wrapper {
		display: flex;
		justify-content: center;
		flex-direction: row;

		position: relative;
	}

	.random-quote {
		height: 100%;
		max-width: 60ch;

		font-size: 2.1rem;
		font-family: Georgia, Helvetica, sans-serif;
		font-style: italic;
		letter-spacing: 1px;
		text-align: center;
	}

	.quotation-mark-before {
		position: absolute;
		top: 0;
		left: -1.5rem;

		color: black;
		font-size: 2rem;

		opacity: 0;
	}

	.quotation-mark-after {
		position: absolute;
		bottom: -1.5rem;
		right: -1.5rem;

		color: black;
		font-size: 2rem;

		opacity: 0;
	}

	.fade-in {
		opacity: 0;
		animation: fadeIn 0.5s ease-out 0.3s forwards;
	}

	@keyframes fadeIn {
		to {
			opacity: 1;
		}
	}

	.random-quotee {
		margin: 1rem;
		font-size: 1.3rem;

		text-align: center;
	}

	.button-container {
		position: absolute;
		right: 0;
		bottom: 0;
		margin: 2rem;

		display: flex;
		justify-content: space-between;
	}

	:global(button:focus-visible) {
		outline: none;
		border: 2px black dotted;
		border-radius: 0.5rem;
	}
</style>
