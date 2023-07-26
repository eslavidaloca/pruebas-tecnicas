<script lang="ts">
	import type { Readable, Writable } from 'svelte/store';
	import data from '../data/books.json';
	import { openDrawer, closeDrawer } from '$lib/components/drawerSkeleton.svelte';
	import { localStorageStore } from '@skeletonlabs/skeleton';
	import { get } from 'svelte/store';

	import CardImage from '$lib/components/cardImage.svelte';

	import type { Book } from '$lib/interfaces/book.interface.svelte';

	let books: Book[] = [];
	let available: number = 0;
	let availableSpan: HTMLSpanElement; // Variable para hacer referencia al span
	const lectureList: Readable<Book[]> = localStorageStore('lectureList', []);

	async function getBooks(): Promise<void> {
		try {
			books = data.library.map((item) => item.book as Book);
			// delete books from books that are in lectureList
			const currentLectureList: Book[] = get(lectureList);
			const lectureTitles = currentLectureList.map((item) => item.title); // Crear un array de títulos de libros
			books = books.filter((item) => !lectureTitles.includes(item.title)); // Comparar los títulos de libros
			available = books.length;
		} catch (error) {
			console.error('Error:', error);
		}
	}
</script>

<svelte:head>
	<title>Inicio</title>
</svelte:head>

<div class="flex justify-end">
	<button
		id="lectureList"
		type="button"
		class="btn btn-xl md:btn-xl bg-primary-500 rotated-btn"
		on:click={openDrawer}
	>
		Lista de lectura
	</button>
</div>
<div class="flex justify-center mt-3">
	<div class="flex-col">
		{#await getBooks() then}
			<span
				class="md:text-5xl text-4xl text-primary-500 dark:text-tertiary-500"
				bind:this={availableSpan}
			>
				{available} Libros disponibles
			</span>
			<div class="grid grid-cols-4 mt-5 space-x-4">
				{#each books as book}
					<CardImage bind:books {book} bind:availableSpan addToLecture={true} />
				{/each}
			</div>
		{/await}
	</div>
</div>

<style>
	#lectureList {
		position: fixed;
		transform: rotate(-90deg);
		/* position: relative; */
		top: 15vh;
		/* left: 86vw; */
	}
	.rotated-btn {
		/* Altura y ancho ajustados para que el botón ocupe el espacio necesario */
		height: 3rem;
		width: 10rem;
		/* Margen derecho para dejar espacio entre el botón y el borde derecho de la pantalla */
		margin-right: -4rem;
	}
	@media (min-width: 768px) {
		.rotated-btn {
			/* Altura y ancho ajustados para que el botón ocupe el espacio necesario */
			height: 4rem;
			width: 15rem;
			/* Margen derecho para dejar espacio entre el botón y el borde derecho de la pantalla */
			margin-right: -6rem;
		}
	}
</style>
