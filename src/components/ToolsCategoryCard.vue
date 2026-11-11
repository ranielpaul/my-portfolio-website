<script setup>
import ToolsItemCard from './ToolsItemCard.vue'

defineProps({
    group: {
        type: Object,
        required: true,
    },
})
</script>

<template>
    <div class="col-lg-4 col-md-6">
        <div class="standard-card p-4 h-100 d-flex flex-column">
            <div class="pb-3 mb-3 border-bottom border-light text-center">
                <h3 class="fs-5 fw-bold text-dark mb-0">{{ group.title || group.category }}</h3>
                <span class="text-xs font-mono-code">{{ group.subtitle }}</span>
            </div>

            <div class="row g-3">
                <ToolsItemCard
                    v-for="(tool, index) in group.items"
                    :key="tool.name"
                    :tool="tool"
                    :is-alone="Boolean(group.items && group.items.length % 2 !== 0 && index === group.items.length - 1)"
                />
            </div>
        </div>
    </div>
</template>