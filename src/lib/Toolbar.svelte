<script lang="ts">
	import { showModalComponent } from '$misc/shared';
	import { createEventDispatcher, getContext, onDestroy } from 'svelte';
	import { PencilSquare, XMark } from '@inqling/svelte-icons/heroicon-24-solid';
	import { getModalStore } from '@skeletonlabs/skeleton';
	import type { Readable } from 'svelte/store';

	const dispatch = createEventDispatcher();
	const modalStore = getModalStore();

	export let title: string;
	export let slug = '';
	let lastScrollPercent = 0;
	let lastClientHeight = 0;
	let show = true;

	function handleScroll(target: HTMLElement) {
		const currentScrollPercent = target.scrollTop / (target.scrollHeight - target.clientHeight);
		if (target.scrollTop === 0) {
			show = true;
		} else if (lastClientHeight == target.clientHeight) {
			show = currentScrollPercent <= lastScrollPercent;
		} else {
			lastClientHeight = target.clientHeight;
		}
		lastScrollPercent = currentScrollPercent;
	}

	function handleEditTitle() {
		if (slug) {
			showModalComponent(modalStore, 'SuggestTitleModal', { slug });
		}
	}

	const unsubscribe = (getContext('scrollContext') as Readable<UIEvent | undefined>).subscribe(event => {
		if (event?.target != null) {
			handleScroll(event.target as HTMLElement);
		}
	});

	onDestroy(unsubscribe);
</script>

<header class="sticky top-0 z-40 badge-glass py-2 md:px-8 lg:rounded-xl ease-linear duration-300"
	class:-translate-y-full={!show}>
	<div class="flex flex-col-reverse md:flex-row justify-center md:justify-between items-center">
		<!-- Title -->
		<div class="flex justify-center md:justify-start w-full px-4 md:px-0 mt-4 md:mt-0 max-w-[80%]">
			<h2 class="h2 truncate text-base md:text-2xl font-bold">
				{title}
			</h2>

			<!-- Edit button -->
			{#if slug}
				<button class="btn btn-sm" on:click={handleEditTitle}>
					<PencilSquare class="w-4 h-4 md:w-6 md:h-6" />
				</button>
			{/if}
		</div>

		<div class="flex justify-self-center space-x-2">
			<!-- Provided by using components -->
			<slot name="actions" />

			<!-- Close -->
			<button class="btn" on:click={() => dispatch('closeChat')}>
				<XMark class="w-6 h-6" />
			</button>
		</div>
	</div>
</header>
