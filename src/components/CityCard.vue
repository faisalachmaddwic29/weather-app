<template>
    <div
        class="flex py-6 px-3 bg-weather-secondary rounded-md shadow-md cursor-pointer"
    >
        <div class="flex flex-col flex-1">
            <h2 class="text-3xl">{{ city.city }}</h2>
            <h3>{{ city.state }}</h3>
        </div>

        <div class="flex flex-col gap-2">
            <p class="text-3xl self-end">
                {{ Math.round(city.weather.current.temp) }}&deg;
            </p>
            <div class="flex gap-2 items-start">
                <p class="capitalize">
                    {{ city.weather.current.weather[0].description }}
                </p>
                <img
                    class="w-[40px] h-[30px]"
                    :src="`https://openweathermap.org/img/wn/${city.weather.current.weather[0].icon}@2x.png`"
                    alt="icon"
                />
            </div>
        </div>
    </div>
</template>

<script setup>
defineProps({
    city: {
        type: Object,
        default: () => {},
    },
});
</script>
