<script lang="ts">
	let { onclick } = $props();
</script>

<button {onclick} aria-label="settings">
	<svg
		width="100%"
		height="100%"
		viewBox="0 0 24 24"
		fill="none"
		xmlns="http://www.w3.org/2000/svg"
	>
		<path
			d="M10 11V17"
			stroke="currentColor"
			stroke-width="2"
			stroke-linecap="round"
			stroke-linejoin="round"
		/>
		<path
			d="M14 11V17"
			stroke="currentColor"
			stroke-width="2"
			stroke-linecap="round"
			stroke-linejoin="round"
		/>
		<path
			d="M4 7H20"
			stroke="currentColor"
			stroke-width="2"
			stroke-linecap="round"
			stroke-linejoin="round"
		/>
		<path
			d="M6 7H12H18V18C18 19.6569 16.6569 21 15 21H9C7.34315 21 6 19.6569 6 18V7Z"
			stroke="currentColor"
			stroke-width="2"
			stroke-linecap="round"
			stroke-linejoin="round"
		/>
		<path
			d="M9 5C9 3.89543 9.89543 3 11 3H13C14.1046 3 15 3.89543 15 5V7H9V5Z"
			stroke="currentColor"
			stroke-width="2"
			stroke-linecap="round"
			stroke-linejoin="round"
		/>
	</svg>
</button>

<style>
	button {
		width: 1.8rem;
		aspect-ratio: 1/1;
		margin: 0rem 0.5rem;

		background-color: transparent;
		border: transparent solid 1px;

		cursor: pointer;
	}

	button:hover {
		animation: wobble 0.6s ease-out;
	}

	svg {
		transition: color 0.2s;
	}

	button:active > svg {
		color: rgb(80, 80, 80);
	}

	@keyframes wobble {
		0% {
			transform: rotate(0deg);
		}
		15% {
			transform: rotate(20deg);
		}
		30% {
			transform: rotate(-20deg);
		}
		45% {
			transform: rotate(15deg);
		}
		60% {
			transform: rotate(-15deg);
		}
		75% {
			transform: rotate(10deg);
		}
		90% {
			transform: rotate(-10deg);
		}
		100% {
			transform: rotate(0deg);
		}
	}
</style>
