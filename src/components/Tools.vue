<script setup>
import tools from '../data/tools.json'
import ToolsCategoryCard from './ToolsCategoryCard.vue'
</script>

<template>
    <!-- Section 3: Tools & Technologies -->
    <section id="tools" class="py-5 my-auto">
        <div class="container-xl">
            <!-- Section Header -->
            <div class="text-center mb-5">
            <span class="tool-section-pill px-3 py-1 rounded-pill text-xs fw-semibold d-inline-block mb-2">
                Technical Ecosystem
            </span>
            <h2 class="font-display display-6 fw-bold text-dark mb-2">
                Tools &amp; Technologies
            </h2>
            <p class="text-secondary fs-6  mx-auto mb-0">
                Categorized stack of client libraries, backend architectures, databases, and development tooling.
            </p>
            </div>

            <div class="row g-4 justify-content-center text-center">
                <ToolsCategoryCard v-for="group in tools" :key="group.category" :group="group" />
            </div>
        </div>
    </section>
</template>
