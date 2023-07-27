<script lang="ts">
	import { onMount, afterUpdate, onDestroy } from 'svelte';
	import data from '../data/books.json';
	import { openDrawer } from '$lib/components/drawerSkeleton.svelte';
	import { get, type Writable } from 'svelte/store';

	import storage from '$lib/store';
	import CardImage from '$lib/components/cardImage.svelte';

	import type { Book } from '$lib/interfaces/book.interface.svelte';
	import { localStorageStore } from '@skeletonlabs/skeleton';

	let books: Book[] = [];
	let available: number = 0;
	let inLectureList: number = 0;
	let availableSpan: HTMLSpanElement; // Variable para hacer referencia al span
	// const lectureList: Readable<Book[]> = localStorageStore('lectureList', []);
	const lectureList = storage<Book[]>('lectureList', []);

	const writableLectureList: Writable<Book[]> = localStorageStore('lectureList', []);

    // esta funcion es para actualizar los libros cuando pasamos de lectura a available
    const unsubscribe = writableLectureList.subscribe(async() => {
        await getBooks();
    });

	async function getBooks(): Promise<void> {
		try {
			books = data.library.map((item) => item.book as Book);
			// delete books from books that are in lectureList
			const currentLectureList: Book[] = get(lectureList);
			const lectureTitles = currentLectureList.map((item) => item.title); // Crear un array de títulos de libros
			books = books.filter((item) => !lectureTitles.includes(item.title)); // Comparar los títulos de libros
			available = books.length;
			inLectureList = currentLectureList.length;
		} catch (error) {
			console.error('Error:', error);
		}
	}
	onMount(() => {
		getBooks();
		window.onstorage = () => {
			getBooks();
		};
	});
	// afterUpdate(() => {
	// 	getBooks();
	// });
	onDestroy(() => {
        unsubscribe(); // Limpieza cuando el componente se destruye
    });
</script>

<svelte:head>
	<title>Inicio</title>
</svelte:head>

<div class="flex justify-end">
	<button
		id="lectureList"
		type="button"
		class="btn btn-xl md:btn-xl bg-primary-500 dark:bg-secondary-500 rotated-btn"
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
				bind:this={availableSpan}
			>
				{available} Libros disponibles
				<br>
				<small class="md:text-4xl text-3xl pl-1 dark:text-primary-500 text-secondary-500">{inLectureList} Libros en lectura</small>
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
