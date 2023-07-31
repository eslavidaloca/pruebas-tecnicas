<script lang="ts">
	import { onMount } from 'svelte';
	import data from '../data/books.json';
	// import { openDrawer } from '$lib/components/drawerSkeleton.svelte';
	import { get, type Writable, writable } from 'svelte/store';

	// import storage from '$lib/store';
	import CardImage from '$lib/components/cardImage.svelte';

	import type { Book } from '$lib/interfaces/book.interface.svelte';
	import { localStorageStore } from '@skeletonlabs/skeleton';

	import { TabGroup, Tab } from '@skeletonlabs/skeleton';
	
	const tabSet: Writable<number> = writable(0);

	const lectureList: Writable<Book[]> = localStorageStore('lectureList', []);

	let books: Book[] = [];
	// let lectureListStore = storage<Book[]>('lectureList', []); // This works for multiple tabs


	// const writableLectureList: Writable<Book[]> = localStorageStore('lectureList', []);

	// esta funcion es para actualizar los libros cuando pasamos de lectura a available
	// const unsubscribe = writableLectureList.subscribe(async() => {
	//     await getBooks();
	// });

	async function getBooks(): Promise<void> {
		try {
			books = data.library.map((item) => item.book as Book);
			// delete books from books that are in lectureList
			const lectureTitles = $lectureList.map((item) => item.title); // Crear un array de títulos de libros
			books = books.filter((item) => !lectureTitles.includes(item.title)); // Comparar los títulos de libros
		} catch (error) {
			console.error('Error:', error);
		}
	}
	lectureList.subscribe(() => {
		getBooks();
	})
	onMount(() => {
		getBooks();
		window.onstorage = () => {
			getBooks();
		};
	});
	// afterUpdate(() => {
	// 	getBooks();
	// });
	// onDestroy(() => {
	//     unsubscribe(); // Limpieza cuando el componente se destruye
	// });
</script>

<svelte:head>
	<title>Inicio</title>
</svelte:head>

<!-- <div class="flex justify-end">
	<button
		id="lectureList"
		type="button"
		class="btn btn-xl md:btn-xl bg-primary-500 dark:bg-secondary-500 rotated-btn"
		on:click={openDrawer}
	>
		Lista de lectura
	</button>
</div> -->

<TabGroup justify="justify-evenly">
	<Tab
		bind:group={$tabSet}
		name="availableBooks"
		title="Libros disponibles"
		value={0}
		hover="hover:text-primary-500"
		active="bg-primary-500"
	>
		<svelte:fragment slot="lead" />
		<div class="flex">
			<span>Libros disponibles </span>
			<svg
				xmlns="http://www.w3.org/2000/svg"
				fill="none"
				viewBox="0 0 24 24"
				stroke-width="1.5"
				stroke="currentColor"
				class="w-6 h-6 ml-1"
			>
				<path
					stroke-linecap="round"
					stroke-linejoin="round"
					d="M12 6.042A8.967 8.967 0 006 3.75c-1.052 0-2.062.18-3 .512v14.25A8.987 8.987 0 016 18c2.305 0 4.408.867 6 2.292m0-14.25a8.966 8.966 0 016-2.292c1.052 0 2.062.18 3 .512v14.25A8.987 8.987 0 0018 18a8.967 8.967 0 00-6 2.292m0-14.25v14.25"
				/>
			</svg>
		</div>
	</Tab>
	<Tab
		bind:group={$tabSet}
		name="lectureListBooks"
		title="Lista de lectura"
		value={1}
		hover="hover:text-primary-500"
		active="bg-primary-500"
	>
		<svelte:fragment slot="lead" />
		<div class="flex">
			<span>Lista de lectura</span>
			<svg
				xmlns="http://www.w3.org/2000/svg"
				fill="none"
				viewBox="0 0 24 24"
				stroke-width="1.5"
				stroke="currentColor"
				class="w-6 h-6 ml-1"
			>
				<path
					stroke-linecap="round"
					stroke-linejoin="round"
					d="M17.593 3.322c1.1.128 1.907 1.077 1.907 2.185V21L12 17.25 4.5 21V5.507c0-1.108.806-2.057 1.907-2.185a48.507 48.507 0 0111.186 0z"
				/>
			</svg>
		</div>
	</Tab>
	<!-- Tab Panels --->
	<svelte:fragment slot="panel">
		<div class="flex justify-center mt-3">
			<div class="flex-col">
				{#await getBooks() then}
					{#if $tabSet === 0}
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
					{:else if $tabSet === 1}
						<div class="grid grid-cols-4 mt-5 space-x-4">
							{#each $lectureList as book}
								<CardImage bind:books={$lectureList} {book}/>
							{/each}
						</div>
					{/if}
				{/await}
			</div>
		</div>
	</svelte:fragment>
</TabGroup>

<!-- <div class="flex justify-center mt-3">
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
</div> -->

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
