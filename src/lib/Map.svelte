<script lang="ts">
	import type { LatLngTuple, Map } from 'leaflet';
	import 'leaflet/dist/leaflet.css';
	import 'leaflet.markercluster/dist/MarkerCluster.css';
	import 'leaflet.markercluster/dist/MarkerCluster.Default.css';
	import { createEventDispatcher, setContext } from 'svelte';

	let leafletLoaded = false;
	let L: typeof import('leaflet');

	export let latLng: LatLngTuple;
	export let zoom: number;

	const dispatch = createEventDispatcher();
	let map: Map;

	setContext('map', () => map);
	let container: HTMLElement;

	// reset view whenever coords or zoom change
	$: map?.setView(latLng, zoom);

	function resizeMap() {
		map?.invalidateSize();
	}

	function onLoad() {
		leafletLoaded = true;
		L = window.L;
		map = L.map(container)
			.on('add', (e) => dispatch('add', e))
			.on('animationend', (e) => dispatch('animationend', e))
			.on('autopanstart', (e) => dispatch('autopanstart', e))
			.on('baselayerchange', (e) => dispatch('baselayerchange', e))
			.on('click', (e) => dispatch('click', e))
			.on('contextmenu', (e) => dispatch('contextmenu', e))
			.on('dblclick', (e) => dispatch('dblclick', e))
			.on('down', (e) => dispatch('down', e))
			.on('drag', (e) => dispatch('drag', e))
			.on('dragend', (e) => dispatch('dragend', e))
			.on('dragstart', (e) => dispatch('dragstart', e))
			.on('error', (e) => dispatch('error', e))
			.on('keydown', (e) => dispatch('keydown', e))
			.on('keypress', (e) => dispatch('keypress', e))
			.on('keyup', (e) => dispatch('keyup', e))
			.on('layeradd', (e) => dispatch('layeradd', e))
			.on('layerremove', (e) => dispatch('layerremove', e))
			.on('load', (e) => dispatch('load', e))
			.on('loading', (e) => dispatch('loading', e))
			.on('locationerror', (e) => dispatch('locationerror', e))
			.on('locationfound', (e) => dispatch('locationfound', e))
			.on('mousedown', (e) => dispatch('mousedown', e))
			.on('mousemove', (e) => dispatch('mousemove', e))
			.on('mouseout', (e) => dispatch('mouseout', e))
			.on('mouseover', (e) => dispatch('mouseover', e))
			.on('mouseup', (e) => dispatch('mouseup', e))
			.on('move', (e) => dispatch('move', e))
			.on('moveend', (e) => dispatch('moveend', e))
			.on('movestart', (e) => dispatch('movestart', e))
			.on('overlayadd', (e) => dispatch('overlayadd', e))
			.on('overlayremove', (e) => dispatch('overlayremove', e))
			.on('popupclose', (e) => dispatch('popupclose', e))
			.on('popupopen', (e) => dispatch('popupopen', e))
			.on('preclick', (e) => dispatch('preclick', e))
			.on('predrag', (e) => dispatch('predrag', e))
			.on('remove', (e) => dispatch('remove', e))
			.on('resize', (e) => dispatch('resize', e))
			.on('spiderfied', (e) => dispatch('spiderfied', e))
			.on('tileabort', (e) => dispatch('tileabort', e))
			.on('tileerror', (e) => dispatch('tileerror', e))
			.on('tileload', (e) => dispatch('tileload', e))
			.on('tileloadstart', (e) => dispatch('tileloadstart', e))
			.on('tileunload', (e) => dispatch('tileunload', e))
			.on('tooltipclose', (e) => dispatch('tooltipclose', e))
			.on('tooltipopen', (e) => dispatch('tooltipopen', e))
			.on('unload', (e) => dispatch('unload', e))
			.on('unspiderfied', (e) => dispatch('unspiderfied', e))
			.on('update', (e) => dispatch('update', e))
			.on('viewreset', (e) => dispatch('viewreset', e))
			.on('zoom', (e) => dispatch('zoom', e))
			.on('zoomanim', (e) => dispatch('zoomanim', e))
			.on('zoomend', (e) => dispatch('zoomend', e))
			.on('zoomlevelschange', (e) => dispatch('zoomlevelschange', e))
			.on('zoomstart', (e) => dispatch('zoomstart', e))
			.setView(latLng, zoom)
			.setMinZoom(5)
			.setMaxZoom(20);

		L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
			attribution: `&copy;<a href="https://www.openstreetmap.org/copyright" target="_blank">OpenStreetMap</a>`,
			maxZoom: 20
		}).addTo(map);
	}

	function leafletLoader(node: HTMLElement) {
		import('leaflet').then(() => {
			import('leaflet.markercluster').then(onLoad);
		});
	}
</script>

<svelte:window on:resize={resizeMap} use:leafletLoader />

<div class="Map" bind:this={container} style="height: 100%; width: 100%">
	{#if map}
		<slot {map} />
	{/if}
</div>

<style>
	/* .Map :global(.leaflet-div-icon) {
		background-color: unset;
		border: none;
	} */
</style>
