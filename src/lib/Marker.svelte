<script lang="ts">
	import type { Marker, LatLngTuple, Map, LayerGroup, MarkerOptions } from 'leaflet';
	import { getContext, onDestroy, onMount, setContext, tick } from 'svelte';
	const L = globalThis.window.L;

	export let width = 30;
	export let height = 30;
	export let latLng: LatLngTuple;

	let markerElement: HTMLElement;
	let marker: Marker;

	const getMap = getContext<() => Map>('map');
	const getLayerGroup = getContext<() => LayerGroup>('layerGroup');
	setContext('layer', () => marker);

	onMount(async () => {
		await tick(); // waits for next paint so layers and map are done rendering
		const layerGroup = getLayerGroup?.();
		const map = getMap();
		const mapOrLayerGroup = layerGroup || map;
		const markerOptions: MarkerOptions = {};
		marker = L.marker(latLng, markerOptions);
		mapOrLayerGroup.addLayer(marker);
		await tick(); // waits for next paint so marker is done rendering
		if (markerElement.childElementCount > 0) {
			// if the marker has children, use them as the marker icon
			marker.setIcon(
				L.divIcon({
					html: markerElement,
					className: 'map-marker',
					iconSize: L.point(width, height)
				})
			);
		}
	});

	onDestroy(() => {
		const layerGroup = getLayerGroup?.();
		const map = getMap();
		const mapOrLayerGroup = layerGroup || map;
		mapOrLayerGroup.removeLayer(marker);
	});
</script>

<template>
	<div bind:this={markerElement}>
		{#if marker}
			<slot />
		{/if}
	</div>
</template>
