<script lang="ts">
	import '../global.css';
	import Map from '$lib/Map.svelte';
	import type L from 'leaflet';
	import type { LatLngTuple } from 'leaflet';
	import MarkerClusterGroup from '$lib/MarkerClusterGroup.svelte';
	import Marker from '$lib/Marker.svelte';
	import MarkerIcon from '$lib/MarkerIcon.svelte';
	import Popup from '$lib/Popup.svelte';
	import { scaleSequential } from 'd3-scale';
	import { interpolateRainbow } from 'd3-scale-chromatic';
	import Polyline from '$lib/Polyline.svelte';

	let newMarkerCoords: LatLngTuple;
	const initialView: L.LatLngTuple = [48.86750658335676, 2.3638381549875467];
	let markerLocations: L.LatLngTuple[] = [
		[48.86750658335676, 2.3638381549875467],
		[48.86670856440179, 2.365128993988037],
		[48.86601338915285, 2.366335988044739],
		[48.8651699909871, 2.367741465568543],
		[48.86440068654065, 2.3690289258956914],
		[48.863747827160694, 2.370107173919678],
		[48.8629890879917, 2.3713946342468266],
		[48.86229739016812, 2.3725721240043645],
		[48.86207329213923, 2.3729449510574345],
		[48.86098454964116, 2.374776899814606],
		[48.8606139835832, 2.375377714633942],
		[48.85974931877853, 2.3768422007560734],
		[48.858711701295746, 2.3785829544067387],
		[48.85784700362906, 2.3800474405288696],
		[48.85767759172399, 2.3801010847091675],
		[48.85765994461759, 2.380460500717163],
		[48.85735994285669, 2.380852103233338],
		[48.85666463774542, 2.3820081353187565],
		[48.85654110537013, 2.3822307586669926],
		[48.856186389283806, 2.382818162441254],
		[48.855875790049694, 2.3833277821540837],
		[48.855334001545785, 2.3842531442642216],
		[48.85469514242145, 2.385315299034119],
		[48.85326209174815, 2.3877131938934326],
		[48.85275027799501, 2.3885822296142583],
		[48.85242553827996, 2.389107942581177],
		[48.852136094584644, 2.3895961046218877],
		[48.85162779916786, 2.390465140342713],
		[48.850593543255656, 2.3921924829483037],
		[48.85038880747024, 2.3925411701202397],
		[48.849089774704346, 2.394729852676392],
		[48.84878619153661, 2.3952448368072514],
		[48.8483837645005, 2.395910024642945]
	];

	$: halfIndex = Math.floor(markerLocations.length / 2);
	$: firstHalf = markerLocations.slice(0, halfIndex);
	$: secondHalf = markerLocations.slice(halfIndex);

	$: colors = scaleSequential(interpolateRainbow).domain([0, markerLocations.length - 1]);
	$: lines = markerLocations.slice(1).map((latLng, i) => {
		let prev = markerLocations[i];
		return {
			latLngs: [prev, latLng],
			color: colors(i)
		};
	});

	function onMapClick(e: CustomEvent<L.LeafletMouseEvent>) {
		console.log('map clicked');
		newMarkerCoords = [e.detail.latlng.lat, e.detail.latlng.lng];
		markerLocations = [...markerLocations, newMarkerCoords];
		console.log(markerLocations);
	}
</script>

<Map latLng={initialView} zoom={18} on:click={onMapClick} on:zoom={() => console.log('map zoom')}>
	<!-- use stringification of latLngs as key to identify lines -->
	{#each lines as { latLngs, color } (JSON.stringify(latLngs))}
		<Polyline {latLngs} lineStyle={{ color, opacity: 1, weight: 5 }} />
	{/each}

	<MarkerClusterGroup>
		<!-- use stringification of latLng as key to identify markers -->
		{#each firstHalf as latLng, i (JSON.stringify(latLng))}
			<Marker {latLng} width={30} height={30}></Marker>
		{/each}
	</MarkerClusterGroup>

	{#each secondHalf as latLng, i (JSON.stringify(latLng))}
		<Marker {latLng} width={30} height={30}>
			<MarkerIcon />
			<Popup>
				<div>popup text</div>
				<div>
					{latLng[0]}
				</div>
				<div>
					{latLng[1]}
				</div>
			</Popup>
		</Marker>
	{/each}
</Map>
