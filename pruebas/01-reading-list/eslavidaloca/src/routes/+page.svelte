<script lang="ts">
	import data from '../data/books.json';
	import { openDrawer } from '$lib/components/drawerSkeleton.svelte';
	import type { Writable } from 'svelte/store';

	import CardImage from '$lib/components/cardImage.svelte';

	import type { Book } from '$lib/interfaces/book.interface.svelte';
	import { localStorageStore } from '@skeletonlabs/skeleton';

	const lectureList: Writable<Book[]> = localStorageStore('lectureList', []);

	let books: Book[] = [];

	async function getBooks(): Promise<void> {
		try {
			books = data.library.map((item) => item.book as Book);
			const lectureTitles = $lectureList.map((item) => item.title); // Crear un array de títulos de libros
			books = books.filter((item) => !lectureTitles.includes(item.title)); // Comparar los títulos de libros
		} catch (error) {
			console.error('Error:', error);
		}
	}
	lectureList.subscribe(() => {
		getBooks();
	})
</script>

<svelte:head>
	<title>Inicio</title>
</svelte:head>

<div class="flex justify-end">
	<button
		id="lectureList"
		type="button"
		class="btn btn-xl md:btn-xl text-white bg-primary-500 dark:bg-secondary-500 rotated-btn"
		on:click={openDrawer}
	>
		Lista de lectura
	</button>
</div>

<div class="flex justify-center mt-3">
	<div class="flex-col">
		{#await getBooks() then}
		<span
		class="md:text-5xl text-4xl text-primary-500 dark:text-secondary-500"
		>
					{books.length} Libros disponibles
					<br />
					<small class="md:text-4xl text-3xl pl-1 dark:text-primary-500 text-secondary-500"
					>{$lectureList.length} Libros en lectura</small
					>
				</span>
				<div class="grid grid-cols-4 mt-5 space-x-4">
					{#each books as book}
					<CardImage bind:books {book} addToLecture={true} />
					{/each}
				</div>
				{/await}
	</div>
</div>

<style>
	#lectureList {
		position: fixed;
		transform: rotate(-90deg);
		top: 15vh;
	}
	.rotated-btn {
		height: 3rem;
		width: 10rem;
		margin-right: -4rem;
	}
	@media (min-width: 768px) {
		.rotated-btn {
			height: 4rem;
			width: 15rem;
			margin-right: -6rem;
		}
	}
</style>
