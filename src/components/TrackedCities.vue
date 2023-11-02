<template>
    <div class=" flex flex-col gap-4 ">
        <p v-if="!trackedCities.length" class="text-white">No locations added. To start tracking a location, search in the
            field above.</p>
        <div v-for="trackCity in trackedCities" :key="trackCity.id">
            <CityCard :trackCity="trackCity" @click="previewTrackedCity(trackCity)" />
        </div>
    </div>
</template>

<script setup>
import axios from "axios";
import { ref } from "vue";
import CityCard from "./CityCard.vue"
import { useRouter } from "vue-router"

const trackedCities = ref([])
const getTrackedCitiesWeather = async () => {

    if (localStorage.getItem("saveCities")) {
        try {
            trackedCities.value = JSON.parse(localStorage.getItem("saveCities"))
            let request = []
            trackedCities.value.forEach((city) => {
                const trackedCitiesResponse = axios.get(
                    `https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lon}&appid=f5c3a8da8408aedcf080fc47dddc3f4f&units=metric`
                );
                request.push(trackedCitiesResponse)

            })
            // Flicker Delay 
            await new Promise((res) => setTimeout(res, 1000))
            const trackedCitiesWeather = await Promise.all(request)

            trackedCitiesWeather.forEach((cityWeather, index) => {
                trackedCities.value[index].weather = cityWeather.data
            })
        } catch (err) {
            console.error(err);
        }

    }
}
await getTrackedCitiesWeather()

const router = useRouter()
const previewTrackedCity = (trackCity) => {
    const state = trackCity.state
    const city = trackCity.city
    router.push({
        name: "cityView",
        params: { state: state.replaceAll(" ", ""), city: city ? city.replaceAll(" ", "") : "None", },
        query: {
            lat: trackCity.coords.lat,
            lon: trackCity.coords.lon,
            id: trackCity.id
        }
    })
}

</script>
