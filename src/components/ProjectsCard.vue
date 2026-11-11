<script setup>
const props = defineProps({
    project: {
        type: Object,
        required: true,
    },
    index: {
        type: Number,
        required: true,
    },
    slideClass: {
        type: String,
        required: true,
    },
    defaultProjectImage: {
        type: String,
        required: true,
    },
})

const emit = defineEmits(['select', 'preview', 'opened'])

function useProjectPlaceholder(event) {
    const image = event.currentTarget
    image.onerror = null
    image.src = props.defaultProjectImage
}
</script>

<template>
    <article
        class="carousel-slide-card standard-card p-4 p-md-5"
        :class="slideClass"
        :aria-hidden="slideClass !== 'active-slide'"
        @click="emit('select', index)"
    >
        <img
            class="project-card-visual"
            :src="project.imageBg || defaultProjectImage"
            :alt="`${project.title} preview`"
            loading="lazy"
            aria-label="Project preview image"
            @error="useProjectPlaceholder"
            @click.stop="emit('preview', project)"
        />
        <div>
            <div class="d-flex flex-wrap justify-content-between align-items-center gap-2 mb-2">
                <h3 class="project-card-title">{{ project.title }}</h3>
                <span class="project-badge">{{ project.badge }}</span>
            </div>
            <p class="project-card-copy">{{ project.description }}</p>
            <div class="d-flex flex-wrap mt-3">
                <strong>Stack Involved:</strong>
            </div>
            <div class="d-flex flex-wrap gap-2 mt-2">
                <span v-for="tag in project.tags" :key="tag" class="project-tag">{{ tag }}</span>
            </div>
            <div class="project-card-action-wrap">
                <a
                    :href="project.url || '#projects'"
                    class="project-card-action"
                    :aria-label="`View project ${project.title}`"
                    target="_blank"
                    rel="noopener noreferrer"
                    @click="emit('opened', project)"
                >
                    <span>View Project</span>
                </a>
            </div>
        </div>
    </article>
</template>