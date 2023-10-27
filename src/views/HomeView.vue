<script setup>
import { ref } from "vue";
import AddressSearchBar from '@/components/AddressSearchBar.vue';
import MapDisplay from "@/components/MapDisplay.vue";
import LocationTable from "@/components/LocationTable.vue"


let locations = ref([])

// add the user's current location
function submitUserCoordinate(coor) {
  if (locations.value.findIndex((loc) => loc.address == "Your location") == -1) {
    locations.value.push({
      address: "Your location",
      latitude: coor.x,
      longitude: coor.y
    })
  } else {
    alert("Your location is already in the table.")
  }
}

// add a user input address
function submitAddress(location) {
  // only add location if it's not already in the table.
  if (locations.value.findIndex((loc) => loc.address == location.address) == -1) {
    locations.value.push(location)
  } else {
    alert("This address is already in the table.")
  }
}

// delete user selected locations
function deleteLocation(addresses) {
  addresses.forEach((address) => {
    const index = locations.value.findIndex((loc)=> {
      return loc.address == address
    })
    locations.value.splice(index, 1)
  })
}

</script>

<template>
  <v-container>
    <AddressSearchBar @submitUserCoor="(coor) => submitUserCoordinate(coor)"
      @submitSearch="(location) => submitAddress(location)"></AddressSearchBar>
    <v-spacer style="height: 20px;"></v-spacer>
    <MapDisplay :locations="locations"></MapDisplay>
    <v-spacer style="height: 20px;"></v-spacer>
    <LocationTable :locations="locations" @deleteLocation = "(a) => deleteLocation(a)"></LocationTable>
  </v-container>
</template>

<style>
.v-container {
  height: 100%;
}
</style>
