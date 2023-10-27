<script setup>
import { onMounted, ref } from "vue";

const geolocationAvailable = ref(false)
const userDenyLocation = ref(false)
const loading = ref(false)
let location = {
    x: 0,
    y: 0
}

// Check if geolocation is available to determine if button should be enabled
onMounted(() => {
    if (navigator && "geolocation" in navigator) {
        geolocationAvailable.value = true
    } else {
        geolocationAvailable.value = false
    }
    console.log("geolocation availability: " + geolocationAvailable.value)
})

// prompt user for getting current location.
function getGeoLocation() {
    loading.value = true
    navigator.geolocation.getCurrentPosition(onSuccess, onError, options)
}

const emit = defineEmits(['submitUserLocation'])


function onSuccess(position) {
    location.x = position.coords.latitude
    location.y = position.coords.longitude
    userDenyLocation.value = false
    loading.value = false
    emit('submitUserLocation', location)
}

function onError() {
    console.log("Failed to retrieve user location")
    userDenyLocation.value = true
    loading.value = false
}

const options = {
    enableHighAccuracy: true,
    maximumAge: 30000,
    timeout: 27000
}
</script>

<template>
    <v-btn variant="tonal" @click="getGeoLocation" :disabled="!geolocationAvailable || userDenyLocation">
        <v-icon v-if="!loading" icon="mdi-map-marker"></v-icon>
        <v-progress-circular indeterminate size="18" v-if="loading"></v-progress-circular>
        Use my location
    </v-btn>
</template>
