<script setup>
defineProps({
    tool: {
        type: Object,
        required: true,
    },
    category: {
        type: String,
        required: true,
    },
    pinned: {
        type: Boolean,
        default: false,
    },
    positionStyle: {
        type: Object,
        required: true,
    },
})
</script>

<template>
    <button
        class="tool-postit"
        :class="{ 'is-pinned': pinned }"
        :style="positionStyle"
        type="button"
        :aria-pressed="pinned"
        :aria-label="`${tool.name}, ${category}. Click to ${pinned ? 'unpin' : 'pin'} note.`"
        :title="tool.title || tool.name"
    >
        <span class="tool-postit-tape" aria-hidden="true"></span>
        <span class="tool-postit-meta">
            <span class="tool-postit-category">{{ category }}</span>
            <span class="tool-postit-index">✦</span>
        </span>
        <span class="tool-postit-content">
            <img :src="tool.image" :alt="tool.name" class="tool-image" />
            <span class="tool-postit-name">{{ tool.name }}</span>
            <span class="tool-postit-description">{{ tool.subtitle }}</span>
        </span>
    </button>
</template>
