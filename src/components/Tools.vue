<script setup>
import { computed, ref, watch } from 'vue'
import tools from '../data/tools.json'
import ToolsCategoryCard from './ToolsCategoryCard.vue'

const categories = ['All', 'Frontend', 'Backend', 'Tools']
const activeCategory = ref('All')
const visibleCount = ref(3)
const pinnedToolName = ref('')

const selectedTools = computed(() => tools.flatMap((group) => {
    const category = group.title || group.category
    return group.items
        .filter(() => activeCategory.value === 'All' || category === activeCategory.value || (activeCategory.value === 'Tools' && category === 'Tools & Others'))
        .map((tool) => ({ ...tool, category }))
}))

const categoryStamps = computed(() => {
    let categoryEnd = 0

    return tools.map((group) => {
        const category = group.title || group.category
        const count = group.items.length
        const stamp = {
            label: category === 'Tools & Others' ? 'Tools' : category,
            value: categoryEnd + count,
            position: `${((categoryEnd + count / 2) / selectedTools.value.length) * 100}%`,
        }
        categoryEnd += count
        return stamp
    })
})

const visibleTools = computed(() => selectedTools.value.slice(0, visibleCount.value))
const sliderProgress = computed(() => selectedTools.value.length
    ? `${(visibleCount.value / selectedTools.value.length) * 100}%`
    : '0%')

watch(visibleTools, (notes) => {
    if (pinnedToolName.value && !notes.some((note) => note.name === pinnedToolName.value)) {
        pinnedToolName.value = ''
    }
})

function selectCategory(category) {
    if (activeCategory.value === category) return

    activeCategory.value = category
    visibleCount.value = 0
    pinnedToolName.value = ''
}

function togglePinnedTool(name) {
    pinnedToolName.value = pinnedToolName.value === name ? '' : name
}
</script>

<template>
    <section id="tools" class="py-5 my-auto">
        <div class="container-xl">
            <div class="text-center mb-5">
                <span class="tool-section-pill px-3 py-1 rounded-pill text-xs fw-semibold d-inline-block mb-2">
                    Technical Ecosystem
                </span>
                <h2 class="font-display display-6 fw-bold text-dark mb-2">
                    Tools &amp; Technologies
                </h2>
                <p class="text-secondary fs-6 mx-auto mb-0">
                    A classroom board of the technologies I use to design, build, and ship.
                </p>
            </div>

            <div class="tool-reveal-control">
                <!-- add stamps that show where frontend/backend/tools appear -->
                <div class="tool-reveal-labels">
                    <label for="tool-reveal-slider" class="fw-semibold text-dark">Add notes to the board</label>
                    <span class="font-mono-code text-secondary">{{ visibleCount }} / {{ selectedTools.length }}</span>
                </div>
                <input
                    id="tool-reveal-slider"
                    v-model.number="visibleCount"
                    class="tool-reveal-slider"
                    type="range"
                    min="0"
                    :max="selectedTools.length"
                    step="1"
                    :style="{ '--slider-progress': sliderProgress }"
                    :aria-valuetext="`${visibleCount} of ${selectedTools.length} notes on the board`"
                    :disabled="selectedTools.length === 0"
                />
                <div class="tool-category-stamps" role="group" aria-label="Jump to a technology category">
                    <button
                        v-for="stamp in categoryStamps"
                        :key="stamp.label"
                        class="tool-category-stamp"
                        :class="{ 'is-reached': visibleCount >= stamp.value }"
                        type="button"
                        :style="{ left: stamp.position }"
                        :aria-pressed="visibleCount === stamp.value"
                        :aria-label="`Reveal notes through ${stamp.label}`"
                        @click="visibleCount = stamp.value"
                    >
                        <span class="tool-category-stamp-mark" aria-hidden="true">✦</span>
                        <span>{{ stamp.label }}</span>
                    </button>
                </div>
                <div class="tool-reveal-hint text-secondary">
                    Drag the slider or use the arrow keys to reveal each note.
                </div>
            </div>

            <ToolsCategoryCard
                :key="activeCategory"
                :category="activeCategory"
                :notes="visibleTools"
                :total-count="selectedTools.length"
                :pinned-tool-name="pinnedToolName"
                @toggle-pin="togglePinnedTool"
            />

        </div>
    </section>
</template>
