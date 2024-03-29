<template>
    <!-- Sticky header with navigation -->
    <header class="sticky top-0 bg-weather-secondary shadow-xl">
        <nav class="container flex flex-col sm:flex-row items-center gap-4 text-white py-6">
            <!-- Logo and application name with link to home -->
            <RouterLink :to="{ name: 'home' }">
                <div class="flex items-center gap-3">
                    <img src="/favicon.png" alt="Sun">
                    <!-- Adjust the width and height as needed -->
                    <p class="text-2xl">The Local Weather</p>
                </div>

            </RouterLink>

            <!-- Info and add city icons -->
            <div class="flex gap-3 flex-1 justify-end">
                <i class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer"
                    @click="toggleModal"></i>
                <!-- Show add city icon if in preview mode -->
                <i class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer"
                    @click="addCity" v-if="route.query.preview"></i>
            </div>

            <!-- Modal for about and instructions -->
            <BaseModal :modalActive="modalActive" @close-modal="toggleModal">
                <div class="text-black">
                    <h1 class="text-2xl mb-1">About:</h1>
                    <p class="mb-4">The Local Weather allows you to track the current and future weather of cities of
                        your choosing.</p>

                    <h2 class="text-2xl">How it works:</h2>
                    <ol class="list-decimal list-inside mb-4">
                        <li>Search for your city by entering the name into the search bar.</li>
                        <li>Select a city within the results, this will take you to the current weather for your
                            selection.</li>
                        <li>Track the city by clicking on the "+" icon in the top right. This will save the city to view
                            at a later time on the home page.</li>
                    </ol>

                    <h2 class="text-2xl">Removing a city</h2>
                    <p>If you no longer wish to track a city, simply select the city within the home page. At the bottom
                        of the page, there will be an option to delete the city.</p>
                </div>
            </BaseModal>
        </nav>
    </header>
</template>

<script setup>
import { ref } from "vue";
import { uid } from "uid";
import { RouterLink, useRoute, useRouter } from "vue-router";
import BaseModal from "./BaseModal.vue";

// State for managing saved cities and modal visibility
const savedCities = ref([]);
const route = useRoute();
const router = useRouter();
const modalActive = ref(null);

// Adds current city to saved cities list and updates local storage
const addCity = () => {
    // Retrieve existing saved cities from local storage
    if (localStorage.getItem('savedCities')) {
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'));
    }
    // Create object for new city with unique ID and coordinates
    const locationObj = {
        id: uid(),
        state: route.params.state,
        city: route.params.city,
        coords: { lat: route.query.lat, lng: route.query.lng }
    };
    savedCities.value.push(locationObj);
    localStorage.setItem('savedCities', JSON.stringify(savedCities.value));

    // Update URL to reflect saved city (removes preview parameter)
    let query = { ...route.query };
    delete query.preview;
    query.id = locationObj.id;
    router.replace({ query });
};

// Toggles the visibility of the modal
const toggleModal = () => {
    modalActive.value = !modalActive.value;
};
</script>
