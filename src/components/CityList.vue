<template>
    <div v-for="city in savedCities" :key="city.id">
        <CityCard :city="city" @click="goToCityView(city)" />
    </div>
</template>

<script setup>
import axios from "axios";
import CityCard from "./CityCard.vue";
import { ref } from "vue";
import { useRouter } from "vue-router";

const StringLocalStorage = "savedCities";
const savedCities = ref([]);
const getCities = async () => {
    if (localStorage.getItem(`${StringLocalStorage}`)) {
        savedCities.value = JSON.parse(
            localStorage.getItem(`${StringLocalStorage}`)
        );

        const requests = [];
        savedCities.value.forEach((city) => {
            requests.push(
                axios.get(`
                    https://api.openweathermap.org/data/2.5/onecall?lat=${city.coords.lat}&lon=${city.coords.lng}&exclude={part}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`)
            );
        });

        const weatherData = await Promise.all(requests);
        console.log(weatherData);

        new Promise((resolve, reject) => {
            setTimeout(function () {
                console.log(reject);
                console.log("One: Completed");
                resolve();
            }, 1000);
        });

        new Promise((resolve, reject) => {
            setTimeout(function () {
                console.log(reject);
                console.log("Two: Completed");
                resolve();
            }, 2000);
        });

        // flicker delay
        // await new Promise((res) => setTimeout(console.log(res.toString()), 10));

        weatherData.forEach((value, index) => {
            savedCities.value[index].weather = value.data;
        });
    }
};

await getCities();

const router = useRouter();
const goToCityView = (city) => {
    router.push({
        name: "cityView",
        params: { state: city.state, city: city.city },
        query: {
            id: city.id,
            lat: city.coords.lat,
            lng: city.coords.lng,
        },
    });
};
// const cities =
</script>
