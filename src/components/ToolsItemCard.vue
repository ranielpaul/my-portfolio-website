<script setup>
import { ref } from 'vue'

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

const emit = defineEmits(['drag', 'click'])
const isDragging = ref(false)
let activePointerId = null
let startPoint
let startCenter
let boardRect
let noteSize
let suppressClick = false
let clickResetTimer

function startDrag(event) {
    if (!event.isPrimary || event.button !== 0) return

    const board = event.currentTarget.closest('.tool-board-surface')
    if (!board) return

    const noteRect = event.currentTarget.getBoundingClientRect()
    boardRect = board.getBoundingClientRect()
    noteSize = { width: noteRect.width, height: noteRect.height }
    startPoint = { x: event.clientX, y: event.clientY }
    startCenter = {
        x: noteRect.left + noteRect.width / 2,
        y: noteRect.top + noteRect.height / 2,
    }
    activePointerId = event.pointerId
    event.currentTarget.setPointerCapture(event.pointerId)
}

function moveNote(event) {
    if (event.pointerId !== activePointerId) return

    const deltaX = event.clientX - startPoint.x
    const deltaY = event.clientY - startPoint.y
    if (!isDragging.value && Math.hypot(deltaX, deltaY) < 4) return

    isDragging.value = true
    suppressClick = true
    event.preventDefault()

    const centerX = Math.min(
        boardRect.width - noteSize.width / 2,
        Math.max(noteSize.width / 2, startCenter.x + deltaX - boardRect.left),
    )
    const centerY = Math.min(
        boardRect.height - noteSize.height / 2,
        Math.max(noteSize.height / 2, startCenter.y + deltaY - boardRect.top),
    )

    emit('drag', {
        left: `${(centerX / boardRect.width) * 100}%`,
        top: `${(centerY / boardRect.height) * 100}%`,
    })
}

function endDrag(event) {
    if (event.pointerId !== activePointerId) return

    activePointerId = null
    isDragging.value = false
    window.clearTimeout(clickResetTimer)
    clickResetTimer = window.setTimeout(() => {
        suppressClick = false
    }, 0)
}

function handleClick(event) {
    if (suppressClick) {
        event.preventDefault()
        event.stopPropagation()
        suppressClick = false
        return
    }

    emit('click')
}
</script>

<template>
    <button
        class="tool-postit"
        :class="{ 'is-pinned': pinned, 'is-dragging': isDragging }"
        :style="positionStyle"
        type="button"
        :aria-pressed="pinned"
        :aria-label="`${tool.name}, ${category}. Drag to move. Click to ${pinned ? 'unpin' : 'pin'} note.`"
        :title="tool.title || tool.name"
        @pointerdown="startDrag"
        @pointermove="moveNote"
        @pointerup="endDrag"
        @pointercancel="endDrag"
        @lostpointercapture="endDrag"
        @click="handleClick"
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
