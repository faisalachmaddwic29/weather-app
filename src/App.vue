<template>
    <div class="flex flex-col min-h-screen font-lato bg-weather-primary">
        <SiteNavigations />
        <Transition name="page" mode="out-in">
            <RouterView v-slot="{ Component }">
                <component :is="Component" />
            </RouterView>
        </Transition>
    </div>
</template>

<script setup>
// import { RouterView } from "vue-router";
import SiteNavigations from "./components/SiteNavigations.vue";
</script>

<style>
.page-enter-active,
.page-leave-active {
    transition: 600ms ease all;
}

.page-enter-from,
.page-leave-to {
    opacity: 0;
}
</style>
