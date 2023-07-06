<template>
    <div class="flex flex-col flex-1 items-center">
        <!-- BANNER -->
        <div
            v-if="route.query.preview"
            class="text-white p-4 bg-weather-secondary w-full text-center"
        >
            Kamu sekarang melihat kota ini, tolong klik icon "+" untuk memulai
            melacak kota ini
        </div>

        <!-- WEATHER -->
        <div class="flex flex-col items-center text-white py-12">
            <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
            <p class="text-sm mb-12">
                {{
                    new Date(weatherData.currentTime).toLocaleDateString(
                        "id-ID",
                        { weekday: "short", day: "2-digit", month: "long" }
                    )
                }}
                {{
                    new Date(weatherData.currentTime).toLocaleTimeString(
                        "id-ID",
                        { timeStyle: "short" }
                    )
                }}
            </p>

            <p class="text-8xl mb-8">
                {{ Math.round(weatherData.current.temp) }}&deg;
            </p>

            <p>
                Feels like
                {{ Math.round(weatherData.current.feels_like) }}&deg;
            </p>
            <p class="capitalize">
                {{ weatherData.current.weather[0].description }}
            </p>
            <img
                class="w-[150px] h-auto"
                :src="`https://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`"
                alt="icon"
            />
        </div>
        <hr class="border-white border-opacity-10 border w-full" />

        <!-- HOURLY WEATHER -->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="mb-4">Hourly Weather</h2>
                <div class="flex gap-10 overflow-x-scroll">
                    <div
                        v-for="hour in weatherData.hourly"
                        :key="hour.dt"
                        class="flex flex-col gap-4 items-center"
                    >
                        <p class="whitespace-nowrap text-md">
                            {{
                                new Date(hour.currentTime).toLocaleTimeString(
                                    "en-US",
                                    { hour: "numeric", hour12: true }
                                )
                            }}
                        </p>

                        <img
                            class="w-[150px] h-[50px] object-cover"
                            :src="`https://openweathermap.org/img/wn/${hour.weather[0].icon}@2x.png`"
                            alt="icon"
                        />

                        <p class="text-xl">{{ Math.round(hour.temp) }}&deg;</p>
                    </div>
                </div>
            </div>
        </div>

        <!-- WEEKLY WEATHER -->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="mb-4">7 Hari Forecast</h2>
                <div
                    v-for="day in weatherData.daily"
                    :key="day.dt"
                    class="flex items-center"
                >
                    <p class="flex-1">
                        {{
                            new Date(day.dt * 1000).toLocaleTimeString(
                                "id-ID",
                                { weekday: "long", hour12: true }
                            )
                        }}
                    </p>

                    <img
                        class="w-[50px] h-[50px] object-cover"
                        :src="`https://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`"
                        alt="icon"
                    />

                    <div class="flex gap-2 flex-1 justify-end">
                        <p>H: {{ Math.round(day.temp.max) }}</p>
                        <p>L: {{ Math.round(day.temp.min) }}</p>
                    </div>
                </div>
            </div>
        </div>

        <div
            class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500"
            @click="removeCity"
        >
            <i class="fa-solid fa-trash"></i>
            <p>Remove City</p>
        </div>
    </div>
</template>

<script setup>
import axios from "axios";
import { useRoute, useRouter } from "vue-router";
const route = useRoute();
const StringLocalStorage = "savedCities";
// const weatherApiKey = "4ea0340992c7657b29b11f2049a8aedc";
// const weatherExclude = ["hourly", "daily"];

const getWeatherData = async () => {
    try {
        const weatherData = await axios.get(
            `https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`
        );

        // cal current data && time

        const localOffset = new Date().getTimezoneOffset() * 60000;
        const utc = weatherData.data.current.dt * 1000 + localOffset;

        weatherData.data.currentTime =
            utc + 1000 * weatherData.data.timezone_offset;

        //  cal hourly weather offset
        weatherData.data.hourly.forEach((hour) => {
            const utc = hour.dt * 1000 + localOffset;
            hour.currentTime = utc + 1000 + weatherData.data.timezone_offset;
        });

        await new Promise((res) => setTimeout(res, 10000));

        return weatherData.data;
    } catch (err) {
        console.log(err);
    }
};

const weatherData = await getWeatherData();

const router = useRouter();
const removeCity = () => {
    const cities = JSON.parse(localStorage.getItem(`${StringLocalStorage}`));
    const updateCities = cities.filter((city) => city.id !== route.query.id);
    localStorage.setItem(`${StringLocalStorage}`, JSON.stringify(updateCities));

    router.push({
        name: "home",
    });
};
</script>
