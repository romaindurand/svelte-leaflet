<script lang="ts">
	import type { Layer, Popup } from 'leaflet';
	import { getContext, onDestroy, onMount, tick } from 'svelte';
	const L = globalThis.window.L;

	export let popup: Popup | undefined = undefined;

	let popupElement: HTMLElement;

	let showContents = false;
	let popupOpen = false;

	const getLayer = getContext<() => Layer>('layer');
	let layer: Layer;

	onMount(async () => {
		await tick();
		layer = getLayer();
		popup = L.popup().setContent(popupElement);
		layer.bindPopup(popup, { maxWidth: 300, minWidth: 150 });
		layer.on('popupopen', () => {
			popupOpen = true;
			showContents = true;
		});
		layer.on('popupclose', () => {
			popupOpen = false;
			// change the state after the CSS transition
			setTimeout(() => {
				if (!popupOpen) {
					showContents = false;
				}
			}, 500);
		});
	});

	onDestroy(() => {
		layer.unbindPopup();
		popup?.remove();
	});
</script>

<div bind:this={popupElement}>
	{#if showContents}
		<slot />
	{/if}
</div>
