<script context="module" lang="ts">
	import { Drawer, drawerStore } from '@skeletonlabs/skeleton';
	import type { DrawerSettings } from '@skeletonlabs/skeleton';

	const drawerSettings: DrawerSettings = {
		id: 'lectList',
		bgBackdrop:
			'bg-gradient-to-tr from-primary-500/50 dark:from-secondary-500/50 via-tertiary-500/50 to-secondary-500/50 dark:to-primary-500/50',
		width: 'w-8/12 md:w-10/12',
		padding: 'p-4',
		rounded: 'rounded-xl'
	};

	export function openDrawer() {
		drawerStore.open(drawerSettings);
		// drawerStore.open();
	}
</script>

<script lang="ts">
	import { onMount } from 'svelte';
	import type { Writable } from 'svelte/store';

	import { localStorageStore } from '@skeletonlabs/skeleton';

	import type { Book } from '$lib/interfaces/book.interface.svelte';
	import CardImage from './cardImage.svelte';

	let lectureList: Book[] = [];

	const lectureListStore: Writable<Book[]> = localStorageStore('lectureList', []);
	const noPaginasSlider: Writable<number> = localStorageStore('noPaginasSlider', 1500);
	const selectedGenres: Writable<string[]> = localStorageStore('selectedGenres', []);

	const filterBooks = (): void => {
		try {
			lectureList = $lectureListStore;
			lectureList = lectureList
				.filter((item) => $selectedGenres.length === 0 || $selectedGenres.includes(item.genre)) // Filtrar por géneros
				.filter((item) => item.pages <= $noPaginasSlider); // Filtrar por el valor del slider
		} catch (error) {
			console.error('Error:', error);
		}
	};
	noPaginasSlider.subscribe(() => filterBooks());
	selectedGenres.subscribe(() => filterBooks());
	onMount(() => {
		filterBooks();
	});
</script>

<Drawer position="right">
	<div class="flex justify-center mt-3">
		<div class="flex-col text-center">
			<span class="text-5xl text-primary-500 dark:text-tertiary-500">Lista de lectura</span>
			<div class="grid grid-cols-4 mt-5 space-x-4">
				{#each lectureList as book}
					<CardImage bind:books={$lectureListStore} {book} />
				{/each}
			</div>
		</div>
	</div>
</Drawer>
