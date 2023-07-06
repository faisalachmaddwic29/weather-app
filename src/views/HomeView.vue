<template>
    <main class="container text-white">
        <div class="pt-4 mb-8 relative">
            <input
                type="text"
                v-model="searchQuery"
                @input="getSearchResult"
                placeholder="Cari Kota ataupun Negara"
                class="py-2 px-1 w-full bg-transparent border-b focus:border-b-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
            />

            <ul
                class="absolute bg-weather-secondary text-white w-full shadow-md top-[66px]"
                v-if="mapBoxSearchResult"
            >
                <p v-if="searchError" class="px-2 py-2">
                    {{ searchError.value }}
                </p>
                <p
                    v-else-if="!searchError && mapBoxSearchResult.length === 0"
                    class="px-2 py-2"
                >
                    Kota atau Negara tidak ditemukan
                </p>
                <template v-else>
                    <li
                        v-for="value in mapBoxSearchResult"
                        :key="value.id"
                        class="py-2 px-2 cursor-pointer border-b border-b-gray-950"
                        @click="previewCity(value)"
                    >
                        {{ value.place_name }}
                    </li></template
                >
            </ul>
        </div>

        <div class="flex flex-col gap-4">
            <Suspense>
                <CityList />
                <template #fallback>
                    <CityCardSkeleton />
                </template>
            </Suspense>
        </div>
    </main>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
import CityList from "../components/CityList.vue";
import CityCardSkeleton from "../components/CityCardSkeleton.vue";

const router = useRouter();

const previewCity = (searchResult) => {
    const [city, state] = searchResult.place_name.split(",");
    router.push({
        name: "cityView",
        params: { state: state.replaceAll(" ", ""), city: city },
        query: {
            lat: searchResult.geometry.coordinates[1],
            lng: searchResult.geometry.coordinates[0],
            preview: true,
        },
    });
};

const mapBoxApiKey =
    "pk.eyJ1IjoiZmFpc2FsYWNobWFkZGMiLCJhIjoiY2xhODQwcHNpMDAzZTNvbzR5NTVwMWZveCJ9.AgmPQWL0De9ZS5zLJ39Ihg";
const searchQuery = ref("");
const queryTimeout = ref(null);
const mapBoxSearchResult = ref(null);
const searchError = ref(null);

const getSearchResult = () => {
    clearTimeout(queryTimeout.value);
    queryTimeout.value = setTimeout(async () => {
        if (searchQuery.value !== "") {
            try {
                const result = await axios.get(
                    `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapBoxApiKey}&types=place`
                );
                mapBoxSearchResult.value = result.data.features;
                return;
            } catch (e) {
                console.log(e);
                searchError.value = e.message;
                console.log(searchError.value);
            }
        }

        mapBoxSearchResult.value = null;
    }, 300);
};
</script>
