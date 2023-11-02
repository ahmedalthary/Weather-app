<template>
    <header class="sticky top-0 bg-weather-primary shadow-lg">
        <nav class=" flex flex-col gap-4 sm:flex-row items-center justify-between container py-5 text-white">
            <RouterLink :to="{ name: 'home' }">
                <div class="flex items-center gap-3 text-2xl flex-1 cursor-pointer ">
                    <i class="fa-solid fa-sun"></i>
                    <span>The local weather</span>
                </div>
            </RouterLink>
            <div class="flex gap-3">
                <i @click="toggleModal"
                    class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-300 cursor-pointer "></i>
                <i v-if="route.query.preview" @click="addCity"
                    class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-300 cursor-pointer">

                </i>
            </div>
            <modal @close-modal="toggleModal" :modalStatus="modalStatus">
                <div>
                    <h2 class=" font-medium text-lg">About:</h2>
                    <p>The local weather allow you to track the current and future weather of cities of
                        your
                        choosing</p>
                </div>
                <div>
                    <h2 class=" font-medium text-lg">How it works:</h2>
                    <ol class=" list-decimal list-inside">
                        <li>Search for your city by entering the name into the search bar.</li>
                        <li>Select a city within the results, this will take you to the current weather for
                            your selection.
                        </li>
                        <li>Track the city by clicking on the "+" icon in the top right. This will save the
                            city to view at
                            later
                            time on the home page.</li>
                    </ol>
                </div>
                <div>
                    <h2 class=" font-medium text-lg">Removing a city:</h2>
                    <p>When you no longer wish to track a city, simply select the city within the home page. At the bottom
                        of
                        the
                        page, there will be am option to delete the city. </p>
                </div>

            </modal>
        </nav>
    </header>
</template>

<script setup>
import { RouterLink, useRoute, useRouter } from 'vue-router';
import modal from "./modal.vue"
import { ref } from "vue";
import { uid } from "uid"
const route = useRoute()
const router = useRouter()
const saveCities = ref([])

const addCity = () => {
    if (localStorage.getItem("saveCities")) {
        saveCities.value = JSON.parse(localStorage.getItem("saveCities"))

    }
    const cityObj = {
        id: uid(),
        state: route.params.state,
        city: route.params.city,
        coords: {
            lat: route.query.lat,
            lon: route.query.lon

        }

    }
    saveCities.value.push(cityObj)
    localStorage.setItem
        ("saveCities", JSON.stringify(saveCities.value))
    let query = Object.assign({}, route.query)
    delete query.preview
    query.id = cityObj.id
    router.replace({ query })
}


const modalStatus = ref(null)
const toggleModal = () => {
    modalStatus.value = !modalStatus.value
} 
</script>
