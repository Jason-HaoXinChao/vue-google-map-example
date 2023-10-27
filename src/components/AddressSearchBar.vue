<script setup>
import UserLocationButton from "@/components/UserLocationButton.vue"
import { ref, onMounted } from "vue"
import { Loader } from "@googlemaps/js-api-loader"
import axios from 'axios'

const GOOGLE_MAP_API_KEY = "AIzaSyApFAFT9UM0mhxrGyOo4d-ak_CC7fbkqPQ"

const loader = new Loader({
    apiKey: GOOGLE_MAP_API_KEY,
    version: "beta",
    libraries: ['core', 'maps', 'places', 'geocoding']
})

let geocoder

onMounted(async () => {
    // load places library
    loader.importLibrary('places').then(() => {
        new google.maps.places.Autocomplete(
            document.getElementById("autocomplete"),
            {
                bounds: new google.maps.LatLngBounds(
                    new google.maps.LatLng(43.651070, -79.347015)
                )
            }
        )
    }).catch((e) => {
        console.log("Failed to load places library from google map api.")
    })

    // load geocoder function
    loader.importLibrary('geocoding').then(() => {
        geocoder = new google.maps.Geocoder()
    }).catch((e) => {
        console.log("Failed to load geocoding library from google map api.")
    })
})

const emit = defineEmits(['submitSearch', 'submitUserCoor'])
const timezone = ref("N/A")
const localTime = ref("N/A")

// validate address with geocoding api, then send to parent
function submitAddress() {
    // use geocoder to validate the address
    const request = { address: document.getElementById('autocomplete').value }
    geocoder.geocode(request).then((result) => {
        const { results } = result;
        // return the address and coordinate
        emit(
            'submitSearch',
            {
                address: results[0].formatted_address,
                latitude: results[0].geometry.location.lat(),
                longitude: results[0].geometry.location.lng()
            }
        )
        
        // construct google timezone api request url
        const lat = results[0].geometry.location.lat()
        const lng = results[0].geometry.location.lng()
        const timestamp = Math.floor(Date.now()/1000)
        let timezoneUrl = `https://maps.googleapis.com/maps/api/timezone/json?location=${lat}%2C${lng}&timestamp=${timestamp}&key=${GOOGLE_MAP_API_KEY}`
        
        // get timezone and local time of last searched location
        axios.get(timezoneUrl).then((response) => {
            timezone.value = response.data.timeZoneName
            const dstOffset = response.data.dstOffset
            const offset = response.data.rawOffset
            const adjustedTimestamp = timestamp + dstOffset + offset
            const date = new Date(adjustedTimestamp*1000)
            localTime.value = `${date.getHours()}:${date.getMinutes()}`
        }).catch((e) => {
            console.log(e)
        })

    }).catch((e) => {
        console.log("Geocode was not successful for the following reason: " + e);
        alert("The address you have inputted is invalid.")
    });
}

// pass current user coordinate to parent
function submitCoordinate(coordinate) {
    emit(
        'submitUserCoor',
        coordinate
    )
}

</script>

<template>
    <v-row align="center" justify="space-around" no-gutters style="height: 100px">
        <v-col cols="8">
            <v-text-field id="autocomplete" label="Address" variant="outlined" class="align-center" hide-details="auto"
                clearable type="text" @keyup.lazy.enter="submitAddress"></v-text-field>
        </v-col>
    </v-row>
    <v-row align="center" justify="center" no-gutters>
        <v-col cols="3">
            <UserLocationButton @submitUserLocation="(loc) => submitCoordinate(loc)"></UserLocationButton>
        </v-col>
        <v-col cols="2">
            <v-btn variant="tonal" @click="submitAddress">
                <v-icon icon="mdi-map-search"></v-icon>
                Search
            </v-btn>
        </v-col>
        <v-col cols="3">
            <v-row :timezone="timezone">
                Last searched timezone: {{ timezone }}
            </v-row>
            <v-row :localTime="localTime">
                Last searched local time: {{ localTime }}
            </v-row>
        </v-col>
    </v-row>
</template>

