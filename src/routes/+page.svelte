<script>
	import Logo from '$lib/components/Logo.svelte';
	import { quadOut } from 'svelte/easing';
	import { blur, fly } from 'svelte/transition';
	import { renderSVG } from 'uqr';

	/** @type { string } */
	let theText = $state('');
	/** @type { string | null } */
	let theQR = $state(null);
	/** @type { Element | null } */
	let theRenderer = $state(null);
	/** @type { boolean } */
	let isQRGenerated = $derived(theQR !== null);

	function generateQR() {
		if (theText.length < 1) {
			return;
		}
		theQR = renderSVG(theText);
		let theRenderer = document.querySelector('div.qr-container');

		// if (theRenderer) {
		// 	theRenderer.innerHTML = theQR;
		// }
	}

	/** @type { (ev: KeyboardEvent) => void } */
	function handleKeydown(ev) {
		if (ev.key === 'Enter') {
			ev.preventDefault();
			generateQR();
		} else {
			return;
		}
	}
</script>

<svelte:head>
	<title>QuAr - Text-to-QR code generator</title>
</svelte:head>

<header>
	<div class="logo-container">
		<Logo fill="var(--color-primary-500)" />
		<span class="h6">QuAr</span>
	</div>
</header>

<main>
	<div class="container">
		<section id="hero" class="hero">
			<hgroup>
				<h1>Turn text into QR codes</h1>
				<p>
					I made this tool to help me share text from my computer to my phone more easily. If it
					helps you too, thanks for using it.
				</p>
			</hgroup>
			<div class="textbox">
				<div class="textbox-container" data-replica-text={theText}>
					<textarea
						name="the_text"
						id="the_text"
						maxlength="2000"
						placeholder="Write/paste your text here"
						bind:value={theText}
						onkeydown={handleKeydown}
					></textarea>
				</div>
				<small>{theText.length}/2000 characters</small>
			</div>
			<div class="cta">
				<button class="btn" onclick={generateQR} disabled={theText.length < 1}>Generate</button>
			</div>
		</section>
		<section id="renderer" class="renderer">
			<div class="qr-container fix-z-index">
				{#if !isQRGenerated}
					<div
						class="empty-state"
						transition:blur={{ duration: 200, easing: quadOut, amount: 24, opacity: 0 }}
					>
						<p>Your QR code will appear here</p>
					</div>
				{:else}
					<div
						class="qr-instruction"
						transition:fly={{ duration: 200, easing: quadOut, y: '20%', opacity: 0 }}
					>
						<small>Scan this QR code with your other device</small>
					</div>
					{@html theQR}
				{/if}
			</div>
		</section>
	</div>
</main>

<footer>
	<small>
		A project by
		<a href="https://matovius.dev" target="_blank" rel="noopener noreferrer" class="link"
			>@matovius</a
		>
		<span>&CenterDot;</span>
		Check out the
		<a
			href="https://github.com/matovius/que-arr"
			target="_blank"
			rel="noopener noreferrer"
			class="link">GitHub</a
		>
	</small>
</footer>

<style>
	header {
		display: flex;
		justify-content: center;
		align-items: center;
		padding: 20px;

		& > .logo-container {
			display: flex;
			flex-direction: row;
			justify-content: center;
			align-items: center;
			gap: 10px;
		}
	}

	main {
		min-height: 80lvh;
		padding-inline: 20px;
		padding-block-start: 5rem;
		padding-block-end: 20px;

		& > div.container {
			display: grid;
			gap: 40px;

			@media screen and (width >= 960px) {
				grid-template-columns: repeat(2, 1fr);
			}
		}
	}

	section.hero {
		& hgroup > p {
			margin-block-start: 10px;
		}

		/* === TEXTBOX === */
		& .textbox {
			margin-block-start: 20px;
			display: grid;

			& .textbox-container {
				display: grid;
				position: relative;

				&::after {
					content: attr(data-replica-text);
					white-space: pre-wrap;
					visibility: hidden;
					opacity: 0;
					pointer-events: none;
				}
				& > textarea {
					min-height: 5em;
					padding: 20px;
					border-radius: 20px;
					border: none;
					background-color: var(--color-neutral-100);
					resize: none;
					&::placeholder {
						color: var(--color-neutral-400);
					}
				}

				&::after,
				& > textarea {
					font-family: inherit;
					font-size: var(--text-base);
					line-height: 1.5;
					min-height: 10em;
					max-height: 20em;
					padding: 20px;
					/*grid-row-start: 1;
					grid-column-start: 1;
					grid-row-end: 2;
					grid-column-end: 2;*/
					grid-area: 1 / 1 / 2 / 2;
					border: none;
				}
			}

			& small {
				color: var(--color-neutral-400);
				margin-block-start: 5px;
				margin-inline-start: 10px;
			}
		}

		& .cta {
			display: flex;
			justify-content: flex-start;
			align-items: flex-start;
			margin-block-start: 20px;

			& > button.btn {
				color: var(--color-white);
				background-color: var(--color-primary-500);
			}
		}
	}

	div.qr-container {
		/*max-width: 500px;*/
		aspect-ratio: 1;
		padding: 40px;
		border-radius: 40px;
		border: 2px solid var(--color-neutral-100);
		position: relative;
		overflow: hidden;

		& > .empty-state {
			color: var(--color-neutral-300);
			display: flex;
			justify-content: center;
			align-items: center;
			background-color: var(--color-neutral-50);
			position: absolute;
			inset: 0;
		}

		& > .qr-instruction {
			color: var(--color-neutral-300);
			display: flex;
			justify-content: center;
			align-items: center;
			padding-block-start: 1em;
			position: absolute;
			inset-inline: 0;
			inset-block-start: 0;
			z-index: 2;
		}
	}

	footer {
		padding: 40px;
		display: flex;
		justify-content: center;
		align-items: center;

		& > small {
			color: var(--color-neutral-500);
		}
	}
</style>
