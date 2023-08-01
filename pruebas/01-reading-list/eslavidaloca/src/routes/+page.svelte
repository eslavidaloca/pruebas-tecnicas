<script lang="ts">
	import data from '../data/books.json';
	import type { Writable } from 'svelte/store';

	import { localStorageStore } from '@skeletonlabs/skeleton';
	import { RangeSlider } from '@skeletonlabs/skeleton';
	import { ListBox, ListBoxItem } from '@skeletonlabs/skeleton';
	import { TreeView, TreeViewItem } from '@skeletonlabs/skeleton';
	import { Accordion, AccordionItem } from '@skeletonlabs/skeleton';

	import CardImage from '$lib/components/cardImage.svelte';
	import { openDrawer } from '$lib/components/drawerSkeleton.svelte';

	import type { Book } from '$lib/interfaces/book.interface.svelte';

	const lectureList: Writable<Book[]> = localStorageStore('lectureList', []);

	let books: Book[] = [];

	let sliderValue: number = 0;
	let selectedGenres: string[] = [];
	let genres: string[] = [];

	const getBooks = async (): Promise<void> => {
		try {
			books = data.library.map((item) => item.book as Book);
			const lectureTitles = $lectureList.map((item) => item.title); // Crear un array de títulos de libros
			books = books.filter((item) => !lectureTitles.includes(item.title)); // Comparar los títulos de libros
			getGenres();
		} catch (error) {
			console.error('Error:', error);
		}
	};
	const getGenres = (): void => {
		try {
			genres = books.reduce((uniqueGenre: string[], item) => {
				if (!uniqueGenre.includes(item.genre)) {
					uniqueGenre.push(item.genre);
				}
				return uniqueGenre;
			}, []);
		} catch (err) {
			console.log(`Error: ${err}`);
		}
	};
	lectureList.subscribe(() => {
		getBooks();
	});
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
			<div class="grid grid-cols-2 mt-5 space-x-4">
				<span class="md:text-5xl text-4xl text-primary-500 dark:text-secondary-500">
					{books.length} Libros disponibles
					<br />
					<small class="md:text-4xl text-3xl pl-1 dark:text-primary-500 text-secondary-500"
						>{$lectureList.length} Libros en lectura</small
					>
				</span>

				<Accordion hover="hover:bg-primary-500 dark:hover:bg-secondary-500 hover:text-white">
					<AccordionItem regionControl="bg-primary-500 dark:bg-secondary-500 text-white">
						<svelte:fragment slot="summary">Filtros</svelte:fragment>
						<svelte:fragment slot="content">
							<AccordionItem autocollapse>
								<svelte:fragment slot="summary">Filtrar por páginas</svelte:fragment>
								<svelte:fragment slot="content">
									<RangeSlider
										name="range-slider"
										bind:value={sliderValue}
										max={1500}
										step={10}
										ticked
										accent="bg-primary-500 dark:bg-secondary-500 text-primary-500 dark:text-secondary-500"
									>
										<svelte:fragment slot="trail">{sliderValue}</svelte:fragment>
									</RangeSlider>
								</svelte:fragment>
							</AccordionItem>
							<AccordionItem autocollapse>
								<svelte:fragment slot="summary">Filtrar por generos</svelte:fragment>
								<svelte:fragment slot="content">
									<ListBox multiple hover="hover:bg-primary-500 dark:hover:bg-secondary-500 hover:text-white">
										{#each genres as genre}
										<ListBoxItem bind:group={selectedGenres} name="medium" value={genre}>{genre}</ListBoxItem>
										{/each}
									</ListBox>
								</svelte:fragment>
							</AccordionItem>
						</svelte:fragment>
					</AccordionItem>
				</Accordion>

			</div>
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
