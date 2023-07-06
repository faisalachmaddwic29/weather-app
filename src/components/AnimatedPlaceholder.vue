<template>
    <div
        :class="{
            left,
            'bg-gradient-to-l from-gray-100 animate-pulse':
                'bg-gradient-to-r from-gray-100 animate-pulse',
        }"
    >
        &nbsp;
    </div>
</template>

<script setup>
defineProps({
    left: {
        type: Boolean,
        default: false,
    },
});
</script>
