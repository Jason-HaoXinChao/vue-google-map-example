<script setup>
import { watch, onMounted, toRaw } from "vue";
import { Loader } from "@googlemaps/js-api-loader"

const googleMapAPIKey = "AIzaSyApFAFT9UM0mhxrGyOo4d-ak_CC7fbkqPQ"

const loader = new Loader({
    apiKey: googleMapAPIKey,
    version: "beta",
    libraries: ['core', 'maps', 'places', 'geocoding']
})

const mapOptions = {
    center: {
        lat: 43.74869861797512,
        lng: -79.28961306136794
    },
    zoom: 12
};

let map
let markerFunction

onMounted(async () => {
    // initialize empty map
    loader.importLibrary('maps').then(({ Map }) => {
        map = new Map(document.getElementById("map"), mapOptions)
    }).catch((e) => {
        console.log("Failed to load map library from google map api.")
    })

    // load marker function
    loader.importLibrary('marker').then(({ Marker }) => {
        markerFunction = Marker
    }).catch((e) => {
        console.log("Failed to load marker library from google map api.")
    })
})

let markers = []
// create a new marker on the map
function addMarker(location) {
    const coordinate = { lat: location.latitude, lng: location.longitude }
    const marker = new markerFunction({
        map: map,
        position: coordinate,
        title: location.address
    })
    map.setCenter(coordinate)
    markers.push(marker)
}

const props = defineProps({
    locations: Array
})
const locations = props.locations

// watcher to update markers
watch(() => locations.length, () => {
    removeAllMarkers()
    markers = []
    toRaw(locations).forEach((location) => {
        addMarker(location)
    })
})

// remove all markers from the map and memory
function removeAllMarkers() {
    markers.forEach((marker) => {
        marker.setMap(null)
    })
    markers = []
}
</script>

<template>
    <v-row align="center" justify="center" no-gutters style="height: 800px">
        <div id="map"></div>
    </v-row>
</template>

<style>
#map {
    height: 100%;
    width: 80%;
}
</style>