<script lang='ts'>
	// The ordering of these imports is critical to your app working properly
	import '@skeletonlabs/skeleton/themes/theme-crimson.css';
	// If you have source.organizeImports set to true in VSCode, then it will auto change this ordering
	import '@skeletonlabs/skeleton/styles/skeleton.css';
	// Most of your app wide CSS should be put in this file
	import '../app.postcss';
	import { AppShell, AppBar, drawerStore } from '@skeletonlabs/skeleton';
	import { LightSwitch } from '@skeletonlabs/skeleton';
	import { Modal } from '@skeletonlabs/skeleton';

	import DrawerSkeleton from '$lib/components/drawerSkeleton.svelte';

	$: positionClasses = $drawerStore.open ? 'translate-x-[50%]' : '';
</script>

<DrawerSkeleton />

<Modal />

<!-- App Shell -->
<AppShell class="transition-transform {positionClasses}">
	<svelte:fragment slot="header">
		<!-- App Bar -->
		<AppBar>
			<svelte:fragment slot="lead">
				<strong class="text-xl uppercase">Libreria</strong>
			</svelte:fragment>
			<svelte:fragment slot="trail">
				<LightSwitch bgLight='bg-secondary-500 dark:bg-primary-500' ring='ring-[3px] ring-surface-500/30' />
				
			</svelte:fragment>
		</AppBar>
	</svelte:fragment>
	<!-- Page Route Content -->
	<slot />
</AppShell>
