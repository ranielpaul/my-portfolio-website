<script setup>
defineProps({
    tool: {
        type: Object,
        required: true,
    },
    isAlone: {
        type: Boolean,
        default: false,
    },
})
</script>

<template>
    <div :class="[isAlone ? 'col-12' : 'col-6', 'tool-item']">
        <div
            class="p-3 rounded-3 bg-light border d-flex flex-column align-items-center justify-content-center text-center tool-icon-tile h-100"
            :title="tool.title || tool.name"
        >
            <img :src="tool.image" :alt="tool.name" class="mb-2 tool-image" />
            <span class="text-xs fw-bold text-dark font-display">{{ tool.name }}</span>
            <span class="font-mono-code tool-img-text">{{ tool.subtitle }}</span>
        </div>
    </div>
</template>
