<script setup>
import { toRaw, ref } from "vue";
import { VDataTable } from 'vuetify/labs/VDataTable'

const props = defineProps({
    locations: Array
})
const locations = props.locations
const selected = ref([])

// header for table
const headers = [
    {
        title: 'Location',
        align: 'start',
        sortable: false,
        value: 'address'
    },
    {
        title: 'Latitude',
        value: 'latitude'
    },
    {
        title: 'Longitude',
        value: 'longitude'
    }
]

// pass the selected addresses to parent to delete
const emit = defineEmits(['deleteLocation'])
function deleteSelected() {
    const selectedAddresses = toRaw(selected.value)
    emit('deleteLocation', selectedAddresses)
    selected.value = []
}

</script>

<template>
    <v-row align="center" justify="center" no-gutters style="height: 100px">
        <v-btn variant="tonal" @click="deleteSelected" :disabled="selected.length==0">Delete Selected Locations</v-btn>
    </v-row>
    <v-row align="start" justify="center" no-gutters style="height: 800px">
        <v-data-table v-model="selected" :items="locations" :headers="headers" item-value="address" show-select class="elevation-1" items-per-page="10" style="width: 80%;">
        </v-data-table>
    </v-row>
</template>
