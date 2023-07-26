<script context="module" lang="ts">
    import { Drawer, drawerStore } from '@skeletonlabs/skeleton';
    import type { Readable, Writable } from 'svelte/store';
    import { localStorageStore } from '@skeletonlabs/skeleton';

    import type { Book } from '$lib/interfaces/book.interface.svelte';

	const writableLectureList: Readable<Book[]> = localStorageStore('lectureList', []);
    let lectureList: Book[] = [];

    export function openDrawer() {
        drawerStore.open();
    };

    export function closeDrawer() {
        drawerStore.close();
    };

    const unsubscribe = writableLectureList.subscribe(value => {
        lectureList = value; // Actualizamos la variable cuando haya cambios en el store
    });
</script>

<Drawer position="right">
    <div class="flex justify-center mt-3">
		<div class="flex-col text-center">
			<span class="text-5xl text-primary-500 dark:text-tertiary-500" >Lista de lectura</span>
			<div class="grid grid-cols-4 mt-5 space-x-4">
				{#each lectureList as book}
					<CardImage bind:books={lectureList} bind:book={book}/>
				{/each}
			</div>

		</div>
    </div>
</Drawer>

<script lang="ts">
    import { onDestroy } from 'svelte';
	import CardImage from './cardImage.svelte';

    onDestroy(() => {
        unsubscribe(); // Limpieza cuando el componente se destruye
    });
</script>