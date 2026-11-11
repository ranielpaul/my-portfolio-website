<script setup>
import { computed } from 'vue'
import ToolsItemCard from './ToolsItemCard.vue'

const props = defineProps({
    category: {
        type: String,
        required: true,
    },
    notes: {
        type: Array,
        required: true,
    },
    totalCount: {
        type: Number,
        required: true,
    },
    pinnedToolName: {
        type: String,
        default: '',
    },
})

const emit = defineEmits(['toggle-pin'])

const notePalettes = [
    { note: '#fff2a8', tape: '#ffe56d' },
    { note: '#cceeff', tape: '#8dd8ff' },
    { note: '#ffd9e8', tape: '#ffb9d5' },
    { note: '#d8f8dc', tape: '#a9eab1' },
    { note: '#e8ddff', tape: '#cbb4ff' },
    { note: '#ffe2c6', tape: '#ffc28e' },
]

function createRandom(seed) {
    let value = 0
    for (const character of seed) {
        value = Math.imul(value ^ character.charCodeAt(0), 16777619)
    }

    return () => {
        value += 0x6D2B79F5
        let next = value
        next = Math.imul(next ^ (next >>> 15), next | 1)
        next ^= next + Math.imul(next ^ (next >>> 7), next | 61)
        return ((next ^ (next >>> 14)) >>> 0) / 4294967296
    }
}

function createLayout(count, columns, category) {
    const rows = 6
    const random = createRandom(`${category}-${columns}`)
    const horizontalCenters = {
        2: [39, 61],
        3: [27, 50, 73],
        4: [23, 41, 59, 77],
    }[columns]
    const slots = Array.from({ length: columns * rows }, (_, index) => ({
        column: index % columns,
        row: Math.floor(index / columns),
        priority: random(),
    }))

    slots.sort((a, b) => {
        const centerColumn = (columns - 1) / 2
        const centerRow = (rows - 1) / 2
        const distanceA = (a.column - centerColumn) ** 2 + ((a.row - centerRow) * 0.8) ** 2 + a.priority * 1.8
        const distanceB = (b.column - centerColumn) ** 2 + ((b.row - centerRow) * 0.8) ** 2 + b.priority * 1.8
        return distanceA - distanceB
    })

    return Array.from({ length: count }, (_, index) => {
        const slot = slots[index % slots.length]
        const layer = Math.floor(index / slots.length)
        const horizontalJitter = (random() - 0.5) * 7 + (layer ? (random() - 0.5) * 4 : 0)
        const verticalJitter = (random() - 0.5) * 8 + (layer ? (random() - 0.5) * 7 : 0)
        const top = 27 + slot.row * 9.2 + verticalJitter
        return {
            left: `${horizontalCenters[slot.column] + horizontalJitter}%`,
            top: `${Math.min(75, Math.max(25, top))}%`,
            rotation: `${(random() - 0.5) * 8}deg`,
        }
    })
}

const layouts = computed(() => ({
    desktop: createLayout(props.totalCount, 4, props.category),
    tablet: createLayout(props.totalCount, 3, props.category),
    mobile: createLayout(props.totalCount, 2, props.category),
}))

function noteStyle(index) {
    const position = {
        desktop: layouts.value.desktop[index],
        tablet: layouts.value.tablet[index],
        mobile: layouts.value.mobile[index],
    }
    const palette = notePalettes[index % notePalettes.length]

    return {
        '--note-left': position.desktop.left,
        '--note-top': position.desktop.top,
        '--note-left-tablet': position.tablet.left,
        '--note-top-tablet': position.tablet.top,
        '--note-left-mobile': position.mobile.left,
        '--note-top-mobile': position.mobile.top,
        '--note-rotation': position.desktop.rotation,
        '--note-rotation-tablet': position.tablet.rotation,
        '--note-rotation-mobile': position.mobile.rotation,
        '--note-color': palette.note,
        '--tape-color': palette.tape,
        zIndex: index + 1,
    }
}
</script>

<template>
    <div class="tool-board-frame" aria-label="Technology notes whiteboard">
        <div class="tool-board-surface">
            <div class="tool-board-heading" aria-hidden="true">
                <span>TECHNOLOGY LAB</span>
                <span v-if="category !== 'All'">{{ category }}</span>
            </div>

            <div class="tool-board-focus" aria-hidden="true">
                <span></span>
            </div>

            <p v-if="notes.length === 0" class="tool-board-empty">
                Choose a category, then move the slider to pin your first note.
            </p>

            <TransitionGroup
                name="tool-note"
                tag="div"
                class="tool-board-notes"
                aria-live="polite"
                aria-relevant="additions"
            >
                <ToolsItemCard
                    v-for="(note, index) in notes"
                    :key="`${category}-${note.name}`"
                    :tool="note"
                    :category="note.category"
                    :pinned="pinnedToolName === note.name"
                    :position-style="noteStyle(index)"
                    @click="emit('toggle-pin', note.name)"
                />
            </TransitionGroup>
        </div>
        <div class="tool-board-tray" aria-hidden="true">
            <span class="tool-board-eraser"></span>
            <span class="tool-board-marker tool-board-marker-blue"></span>
            <span class="tool-board-marker tool-board-marker-green"></span>
            <span class="tool-board-marker tool-board-marker-red"></span>
        </div>
    </div>
</template>
