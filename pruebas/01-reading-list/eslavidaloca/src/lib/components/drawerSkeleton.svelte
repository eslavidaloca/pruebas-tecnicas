<script context="module" lang="ts">
	import { Drawer, drawerStore } from '@skeletonlabs/skeleton';

	export function openDrawer() {
		drawerStore.open();
	}
</script>

<script lang="ts">
	import CardImage from './cardImage.svelte';

	import type { Writable } from 'svelte/store';

	import type { Book } from '$lib/interfaces/book.interface.svelte';

    import { localStorageStore } from '@skeletonlabs/skeleton';

	const lectureList: Writable<Book[]> = localStorageStore('lectureList', []);
</script>

<Drawer position="right">
	<div class="flex justify-center mt-3">
		<div class="flex-col text-center">
				<span class="text-5xl text-primary-500 dark:text-tertiary-500">Lista de lectura</span>
				<div class="grid grid-cols-4 mt-5 space-x-4">
					{#each $lectureList as book}
						<CardImage bind:books={$lectureList} {book}/>
					{/each}
				</div>
		</div>
	</div>
</Drawer>
