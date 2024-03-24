<script lang="ts">
	import { onMount } from 'svelte';
	import Candidate from './candidate/+page.svelte';

	let name: string = '';
	let phone: string = '';
	let email: string = '';
	let challengeStarted: boolean = false;
	let successModal: boolean = false;
	let failureModal: boolean = false;
	let timer: number = 3;
	let interval: NodeJS.Timeout;

	function startChallenge() {
		challengeStarted = true;
		interval = setInterval(() => {
			timer--;
			if (timer <= 0) {
				clearInterval(interval);
				showFailureModal();
			}
		}, 1000);
	}

	function showSuccessModal() {
		successModal = true;
		const modal = document.getElementById('my_modal_3') as HTMLDialogElement;
		if (modal) modal.showModal();
	}

	function showFailureModal() {
		failureModal = true;
		const modal = document.getElementById('my_modal_3') as HTMLDialogElement;
		if (modal) modal.showModal();
	}

	function closeModal() {
		successModal = false;
		failureModal = false;
		const modal = document.getElementById('my_modal_3') as HTMLDialogElement;
		if (modal) modal.close();
	}

	onMount(() => {
		return () => clearInterval(interval);
	});

	function handleSubmit() {
		clearInterval(interval);
		if (timer > 0) {
			showSuccessModal();
		} else {
			showFailureModal();
		}
	}
</script>

<main class="flex min-h-screen flex-col items-center justify-center">
	<h1 class="mb-8 text-3xl font-bold">Desafio</h1>
	<form class="w-full max-w-lg" on:submit|preventDefault={handleSubmit}>
		<div class="mb-4">
			<label class="mb-2 block text-sm font-bold text-gray-400">
				Nome:
				<input class="mt-1 block w-full" type="text" bind:value={name} required />
			</label>
		</div>
		<div class="mb-4">
			<label class="mb-2 block text-sm font-bold text-gray-400">
				Telefone:
				<input class="mt-1 block w-full" type="text" bind:value={phone} required />
			</label>
		</div>
		<div class="mb-6">
			<label class="mb-2 block text-sm font-bold text-gray-400">
				Email:
				<input class="mt-1 block w-full" type="email" bind:value={email} required />
			</label>
		</div>
		<div class="flex items-center justify-between">
			<button
				class="rounded bg-blue-500 px-4 py-2 font-bold text-white hover:bg-blue-700"
				on:click={startChallenge}
				type="button">Iniciar Desafio</button
			>
			{#if challengeStarted}
				<p class="text-sm text-gray-700">
					Tempo restante: {Math.floor(timer / 60)
						.toString()
						.padStart(2, '0')}:{(timer % 60).toString().padStart(2, '0')}
				</p>
				<button
					class="rounded bg-blue-500 px-4 py-2 font-bold text-white hover:bg-blue-700"
					type="submit">Enviar</button
				>
			{/if}
		</div>
	</form>

	{#if challengeStarted}
		<a href="/candidate" class="btn">Candidato</a>
		<Candidate {name} {phone} {email} />
	{/if}

	<dialog id="my_modal_3" class="modal">
		<div class="modal-box">
			<form method="dialog">
				<button class="btn btn-circle btn-ghost btn-sm absolute right-2 top-2" on:click={closeModal}
					>✕</button
				>
			</form>
			<h3 class="text-lg font-bold">Desafio Finalizado</h3>
			{#if successModal}
				<p>Desafio finalizado com sucesso!</p>
			{:else if failureModal}
				<p>Desafio finalizado com falha!</p>
			{/if}
		</div>
	</dialog>
</main>
