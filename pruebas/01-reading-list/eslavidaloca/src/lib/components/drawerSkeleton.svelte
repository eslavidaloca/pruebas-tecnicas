<script context="module" lang="ts">
	import { Drawer, drawerStore } from '@skeletonlabs/skeleton';

	export function openDrawer() {
		drawerStore.open();
	}
</script>

<script lang="ts">
	import { afterUpdate, onMount, onDestroy } from 'svelte';
	import CardImage from './cardImage.svelte';
	import storage from '$lib/store';

	import { get, type Readable, type Writable } from 'svelte/store';

	import type { Book } from '$lib/interfaces/book.interface.svelte';

    import { localStorageStore } from '@skeletonlabs/skeleton';
    
	const lectureList = storage<Book[]>('lectureList', []);
	let books: Book[] = [];
    const writableLectureList: Writable<Book[]> = localStorageStore('lectureList', []);

    // esta funcion es para actualizar el store cuando pasamos de available a lectura
    const unsubscribe = writableLectureList.subscribe(value => {
        books = value; // Actualizamos la variable cuando haya cambios en el store
    });
    
	async function getBooks(): Promise<void> {
		try {
			books = get(lectureList);
            console.log(`${books}`);
		} catch (error) {
			console.error('Error:', error);
		}
	}

    // Esto es para cuando el componente cambia en otra pestaña
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

<Drawer position="right" on:touchstart={getBooks}>
	<div class="flex justify-center mt-3">
		<div class="flex-col text-center">
			<!-- {#await getBooks() then} -->
				<span class="text-5xl text-primary-500 dark:text-tertiary-500">Lista de lectura</span>
				<div class="grid grid-cols-4 mt-5 space-x-4">
					{#each books as book}
						<CardImage bind:books bind:book />
					{/each}
				</div>
			<!-- {/await} -->
		</div>
	</div>
</Drawer>
