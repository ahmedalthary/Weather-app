<template>
    <div class=" flex flex-col flex-1">
        <!-- Banner  -->
        <div class=" bg-weather-secondary w-full " v-if="route.query.preview">
            <p class=" p-3 text-center text-white">You are currently previewing this city, click "+" icon to start tracking
                this city.
            </p>
        </div>
        <!-- Current weather  -->
        <div class=" flex flex-col text-white py-12 items-center">
            <h1 class=" text-3xl mb-2">{{ route.params.state }}</h1>
            <p class=" mb-12">
                <!-- {{
                    new Date(currentWeather.dt * 1000).toLocaleDateString("en-us", {
                        weekday: "short", day: "2-digit", month: "long"
                    })
                }}
                {{
                    new Date(currentWeather.dt * 1000).toLocaleTimeString("en-us", {
                        timeStyle: "short"
                    })
                }} -->
                {{
                    new Date(currentWeather.currentTime).toLocaleDateString("en-us", {
                        weekday: "short", day: "2-digit", month: "long"
                    })
                }}
                {{
                    new Date(currentWeather.currentTime).toLocaleTimeString("en-us", {
                        timeStyle: "short"
                    })
                }}
            </p>
            <p class=" text-6xl mb-5">{{ Math.round(currentWeather.main.temp) }} &#8451;</p>
            <p>Feels like {{ currentWeather.main.feels_like }} &#8451;</p>
            <p class=" capitalize mb-5">{{ currentWeather.weather[0].description }}</p>
            <img class=" w-[150px] h-auto"
                :src="`http://openweathermap.org/img/wn/${currentWeather.weather[0].icon}@2x.png`" alt="">
        </div>
        <hr class=" border-white border-opacity-10 border w-full">

        <!-- Hourly forecast -->
        <div class=" text-white pt-12 pb-[28px] container">
            <h1 class=" mb-4">Hourly Forecast</h1>
            <div class="flex overflow-x-scroll gap-10 pb-5  ">
                <div v-for="hourly in hourlyForecast.list" :key="hourly.dt" class=" flex flex-col gap-4 items-center">
                    <p class=" whitespace-nowrap text-sm">
                        <!-- {{
                            new Date(hourly.dt_txt).toLocaleTimeString("en-us", {
                                hour: "numeric"
                            })


                        }} -->
                        {{
                            new Date(hourly.currentTime).toLocaleTimeString("en-us", {
                                hour: "numeric"
                            })


                        }}
                    </p>
                    <img class=" w-auto h-[50px] object-cover"
                        :src="`http://openweathermap.org/img/wn/${hourly.weather[0].icon}@2x.png`" alt="">
                    <p class=" whitespace-nowrap">
                        {{ Math.round(hourly.main.temp) }} &#8451;
                    </p>
                </div>
            </div>

        </div>
        <hr class=" border-white border-opacity-10 border w-full">
        <!-- Daily forecast -->
        <div class=" text-white pt-12 pb-[28px] container">
            <h1 class=" mb-4">7 Day Forecast</h1>
            <div v-for="daily in DailyForecast.list" :key="daily.dt" class=" flex  items-center">
                <p class="flex-1">
                    {{
                        new Date(daily.dt * 1000).toLocaleDateString("en-us", {
                            weekday: "long"
                        })


                    }}
                </p>
                <img class=" w-[50px] h-[50px] object-cover "
                    :src="`http://openweathermap.org/img/wn/${daily.weather[0].icon}@2x.png`" alt="">
                <div class=" flex gap-2 flex-1 justify-end whitespace-nowrap">
                    <p>H: {{ Math.round(daily.temp.max) }}</p>
                    <p>L: {{ Math.round(daily.temp.min) }}</p>

                </div>
            </div>

        </div>

        <!-- Delete icon -->
        <div v-if="!route.query.preview" @click="deleteCity"
            class="flex items-center gap-2 justify-center py-12 text-white hover:text-weather-secondary duration-300 cursor-pointer">
            <p>Stop Tracking</p>
            <i class="fa-solid fa-trash"></i>
        </div>
    </div>
</template>

<!-- <script setup>
import axios from 'axios';
import { useRoute } from 'vue-router';
const route = useRoute();
const getWeatherData = async () => {

    try {
        const weatherData = await axios.get(`https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lon}&exclude={part}&appid=a0942e595e1a55ee7e2eb75899472935&units=imperial
        `)

        // cal current date & time
        const localOffset = new Date().getTimezoneOffset() * 60000;
        const utc = weatherData.data.current.dt * 1000 + localOffset;
        weatherData.data.currentTime =
            utc + 1000 * weatherData.data.timezone_offset;

        // cal hourly weather offset
        weatherData.data.hourly.forEach((hour) => {
            const utc = hour.dt * 1000 + localOffset;
            hour.currentTime =
                utc + 1000 * weatherData.data.timezone_offset;
        });

        return weatherData.data;
    } catch (err) {
        console.log(err);
    }
};
const weatherData = await getWeatherData();


</script> -->

<script setup>
import axios from "axios";
import { useRoute, useRouter } from "vue-router";
import { ref } from "vue";


const route = useRoute();
const latitude = route.query.lat;
const longitude = route.query.lon;

// const getWeatherData = async () => {
//     try {
//         const currentWeatherResponse = await fetch(
//             `https://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&appid=f5c3a8da8408aedcf080fc47dddc3f4f&units=metric`
//         );
//         const DailyForecastResponse = await fetch(
//             `https://api.openweathermap.org/data/2.5/forecast/daily?lat=${latitude}&lon=${longitude}&cnt=7&appid=f5c3a8da8408aedcf080fc47dddc3f4f&units=metric`
//         );
//         const hourlyForecastResponse = await fetch(
//             `https://pro.openweathermap.org/data/2.5/forecast/hourly?lat=${latitude}&lon=${longitude}&appid=f5c3a8da8408aedcf080fc47dddc3f4f&units=metric`
//         );
//         const w1 = await currentWeatherResponse.json()
//         const w2 = await hourlyForecastResponse.json()
//         const w3 = await DailyForecastResponse.json()
//         // cal current date & time
//         const localOffset = new Date().getTimezoneOffset() * 60000;
//         const utc = w1.dt * 1000 + localOffset;
//         w1.currentTime =
//             utc + 1000 * w1.timezone;

//         // cal hourly weather offset
//         w2.list.forEach((hour) => {
//             const utc = hour.dt * 1000 + localOffset;
//             hour.currentTime =
//                 utc + 1000 * w1.timezone;
//         });

//         const currentWeather = w1
//         const DailyForecast = w3
//         const hourlyForecast = w2
//         // console.log(currentWeather)
//         // console.log(DailyForecast)
//         // console.log(hourlyForecast)
//         return { currentWeather, DailyForecast, hourlyForecast };
//     } catch (err) {
//         console.error(err);
//     }
// };
// // Usage
// console.log(await getWeatherData())
// console.log( getWeatherData())

const getWeatherData = async () => {
    try {
        const currentWeatherResponse = await axios.get(
            `https://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&appid=f5c3a8da8408aedcf080fc47dddc3f4f&units=metric`
        );
        const DailyForecastResponse = await axios.get(
            `https://api.openweathermap.org/data/2.5/forecast/daily?lat=${latitude}&lon=${longitude}&cnt=7&appid=f5c3a8da8408aedcf080fc47dddc3f4f&units=metric`
        );
        const hourlyForecastResponse = await axios.get(
            `https://pro.openweathermap.org/data/2.5/forecast/hourly?lat=${latitude}&lon=${longitude}&appid=f5c3a8da8408aedcf080fc47dddc3f4f&units=metric`
        );

        // cal current date & time
        const localOffset = new Date().getTimezoneOffset() * 60000;
        const utc = currentWeatherResponse.data.dt * 1000 + localOffset;
        currentWeatherResponse.data.currentTime =
            utc + 1000 * currentWeatherResponse.data.timezone;

        // cal hourly weather offset
        hourlyForecastResponse.data.list.forEach((hour) => {
            const utc = hour.dt * 1000 + localOffset;
            hour.currentTime =
                utc + 1000 * currentWeatherResponse.data.timezone;
        });
        // Flicker Delay 
        await new Promise((res) => setTimeout(res, 1000))

        const currentWeather = currentWeatherResponse.data
        const DailyForecast = DailyForecastResponse.data
        const hourlyForecast = hourlyForecastResponse.data
        // console.log(currentWeather)
        // console.log(DailyForecast)
        // console.log(hourlyForecast)
        return { currentWeather, DailyForecast, hourlyForecast };
    } catch (err) {
        console.error(err);
    }
};
// Usage
const { currentWeather, DailyForecast, hourlyForecast } = await getWeatherData();

const saveCities = ref([])
const filteredSaveCities = ref([])
const router = useRouter()
const deleteCity = () => {
    if (localStorage.getItem("saveCities")) {
        saveCities.value = JSON.parse(localStorage.getItem("saveCities"))
        filteredSaveCities.value = saveCities.value.filter((el) => {
            return el.id !== route.query.id
        })

        localStorage.setItem
            ("saveCities", JSON.stringify(filteredSaveCities.value))

        router.push({
            name: "home"
        })
    }
}
// const getWeatherData = async () => {
//     try {
//         const response = await axios.get(
//             `http://api.weatherapi.com/v1/forecast.json?key=4d219871ad7a4581aee144416231510&q=${latitude},${longitude}&days=7&aqi=no&alerts=no`
//         );
//         const weatherData = response.data;
//         console.log(weatherData)
//         return weatherData;
//     } catch (err) {
//         console.error(err);
//     }
// };

// // Usage
// const weatherData = await getWeatherData();
</script>
<style scoped>
/* width */
::-webkit-scrollbar {
    height: 10px;
}

/* Handle */
::-webkit-scrollbar-thumb {
    background: #004c6f;
    border-radius: 10px;
    -webkit-border-radius: 10px;
    -moz-border-radius: 10px;
    -ms-border-radius: 10px;
    -o-border-radius: 10px;
}

/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
    background: #fff;
}
</style>