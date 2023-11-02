<template>
  <main class=" container flex flex-col gap-6 py-5">
    <div class=" text-white">
      <input v-model="searchQuery" @input="getSearchResult" type="text" class=" bg-transparent  w-full border-b
         focus:border-weather-secondary
          focus:outline-none focus:shadow-[0px_1px_0_0_#004E71] 
          py-2 px-1 " placeholder="Search for city or state">
      <ul v-if="searchResults" class=" shadow-md w-full bg-weather-secondary py-5 px-4 text-white">
        <li v-if="searchError" class="py-2 px-1">Sorry, something went wrong, please try again.</li>
        <li v-if="!searchError && searchResults.length === 0" class="py-2 px-1">No result match your query, try a
          different term.</li>
        <template v-else>
          <li class=" py-2 px-1 cursor-pointer hover:bg-weather-primary duration-300 "
            v-for="searchResult in searchResults" :key="searchResult.place_id" @click="previewCity(searchResult)">
            {{ searchResult.display_name }}
          </li>
        </template>
      </ul>

    </div>

    <div class="flex flex-col gap-4">
      <Suspense>
        <TrackedCities />
        <template #fallback>
          <CityCardSkeleton />
        </template>
      </Suspense>
    </div>
  </main>
</template>
<script setup>
import axios from "axios"
import { ref } from "vue";
import { useRouter } from "vue-router"
import TrackedCities from "../components/trackedCities.vue"
import CityCardSkeleton from "../components/CityCardSkeleton.vue";
const router = useRouter()
const previewCity = (searchResult) => {
  console.log(searchResult)
  const [state, city] = searchResult.display_name.split(",")
  router.push({
    name: "cityView",
    params: { state: state.replaceAll(" ", ""), city: city ? city.replaceAll(" ", "") : "None", },
    query: {
      lat: searchResult.lat,
      lon: searchResult.lon,
      preview: true
    }
  })
}
const searchQuery = ref("")
const queryTimeout = ref(null)
const searchResults = ref(null)
const searchError = ref(null)
// const error = ref(null)
const getSearchResult = () => {
  clearTimeout(queryTimeout.value)
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      try {
        const queryResult = await axios(`https://nominatim.openstreetmap.org/search?q=${searchQuery.value}&format=json`)
        searchResults.value = queryResult.data
      } catch {
        searchError.value = true
      }
      return


      // try {
      //   const queryResult = await axios(`https://nominatim.openstreetmap.org/search?q=${searchQuery.value}&format=json`)
      //   if (queryResult.status != 200) {
      //     throw Error("there is no city or state in this name")
      //   }
      //   searchResults.value = queryResult.data
      //   console.log(searchResults.value)
      //   return
      // } catch (err) {
      //   error.value = err.message
      // }
      // console.log(searchResults.value)
    }
    searchResults.value = null
  }, 300)
}

</script>

