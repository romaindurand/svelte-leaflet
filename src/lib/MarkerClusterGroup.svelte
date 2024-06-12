<script lang="ts">
	import type { Map, MarkerCluster, MarkerClusterGroup } from 'leaflet';
	import { getContext, onMount, setContext, tick } from 'svelte';
	const L = globalThis.window.L;

	const getMap = getContext<() => Map>('map');
	let markers: MarkerClusterGroup;

	setContext('layerGroup', () => markers);
	onMount(async () => {
		const map = getMap();
		markers = L.markerClusterGroup({ iconCreateFunction: undefined });
		map.addLayer(markers);
	});

	// TODO add prop to set iconCreateFunction
	function iconCreateFunction(cluster: MarkerCluster) {
		const count = cluster.getChildCount();
		return L.divIcon({ html: '<b>' + count + '</b>' });
	}
</script>

<slot />
