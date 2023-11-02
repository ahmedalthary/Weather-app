<template>
    <div class=" flex justify-between  bg-weather-secondary py-7 px-3
         text-white rounded-md shadow-md cursor-pointer hover:bg-white
          hover:text-weather-secondary duration-300 ">
        <div class="flex flex-col items-start">
            <p class=" text-xl">{{ trackCity.city }}</p>
            <p class=" text-sm">{{ trackCity.state }}</p>
        </div>
        <div class="flex flex-col items-end">
            <p class=" text-xl">{{ Math.round(trackCity.weather.main.temp) }}&#8451;</p>
            <p class=" text-xs">H: {{ Math.round(trackCity.weather.main.temp_max) }} L: {{
                Math.round(trackCity.weather.main.temp_min) }}</p>
        </div>

    </div>
</template>

<script setup>
defineProps(["trackCity"])

</script>

