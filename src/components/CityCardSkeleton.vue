<template>
    <div
        class="flex py-6 px-3 bg-weather-secondary rounded-md shadow-md cursor-pointer justify-between"
    >
        <div class="flex flex-col flex-1 gap-2">
            <AnimatedPlaceholder class="max-w-[50%]" />
            <AnimatedPlaceholder class="max-w-[40%]" />
        </div>

        <div class="flex flex-col shrink-0 gap-2">
            <AnimatedPlaceholder left="true" class="w-[50px] ml-auto" />
            <AnimatedPlaceholder left="true" class="w-[200px] ml-auto" />
        </div>
    </div>
</template>

<script setup>
import AnimatedPlaceholder from "./AnimatedPlaceholder.vue";
</script>
