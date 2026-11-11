<script setup>
import { onMounted, onUnmounted, ref } from 'vue'
import projects from '../data/projects.json'
import ProjectsCard from './ProjectsCard.vue'

const defaultProjectImage = './images/projects/placeholder.png'
const currentIndex = ref(0)
const selectedProject = ref(null)
const lightboxVisible = ref(false)
const actionNoticeText = ref('')
const actionNoticeVisible = ref(false)
let lightboxTimeout
let lightboxFrame
let actionNoticeTimeout

function previousProject() {
  currentIndex.value = (currentIndex.value - 1 + projects.length) % projects.length
}

function nextProject() {
  currentIndex.value = (currentIndex.value + 1) % projects.length
}

function selectAdjacentProject(index) {
  const previousIndex = (currentIndex.value - 1 + projects.length) % projects.length
  const nextIndex = (currentIndex.value + 1) % projects.length

  if (index === previousIndex || index === nextIndex) {
    currentIndex.value = index
  }
}

function slideClass(index) {
  const previousIndex = (currentIndex.value - 1 + projects.length) % projects.length
  const nextIndex = (currentIndex.value + 1) % projects.length

  if (index === currentIndex.value) return 'active-slide'
  if (index === previousIndex) return 'prev-slide'
  if (index === nextIndex) return 'next-slide'
  return 'hidden-slide'
}

function openProjectLightbox(project) {
  clearTimeout(lightboxTimeout)
  cancelAnimationFrame(lightboxFrame)
  selectedProject.value = project
  lightboxFrame = requestAnimationFrame(() => {
    lightboxVisible.value = true
  })
}

function closeProjectLightbox() {
  lightboxVisible.value = false
  lightboxTimeout = setTimeout(() => {
    selectedProject.value = null
  }, 200)
}

function showActionNotice(text) {
  actionNoticeText.value = text
  actionNoticeVisible.value = true
  clearTimeout(actionNoticeTimeout)
  actionNoticeTimeout = setTimeout(() => {
    actionNoticeVisible.value = false
  }, 2500)
}

function notifyProjectOpened(project) {
  if (project.url) showActionNotice(`Live Preview launched for ${project.title}`)
}

function handleKeydown(event) {
  if (event.key === 'Escape' && selectedProject.value) closeProjectLightbox()
}

onMounted(() => window.addEventListener('keydown', handleKeydown))
onUnmounted(() => {
  window.removeEventListener('keydown', handleKeydown)
  clearTimeout(lightboxTimeout)
  clearTimeout(actionNoticeTimeout)
  cancelAnimationFrame(lightboxFrame)
})
</script>

<template>
<!-- Section 2: Projects -->
    <section id="projects" class="min-vh-100 d-flex flex-column justify-content-center py-5 overflow-hidden">
        <div class="container-xl my-auto">
            <!-- Section Title & Subtitle -->
            <div class="text-center mb-4">
            <h2 class="font-display display-6 fw-bold text-dark mb-2">
                My Projects
            </h2>
            <p class="text-secondary fs-6  mx-auto mb-0">
                A curated showcase of recent applications, systems, and digital product designs.
            </p>
            </div>

            <div class="section-spacer-1"></div>

            <!-- 3-Slide Preview Carousel Stage -->
            <div class="position-relative px-2 px-md-5">
            <button type="button" id="prevBtn" class="carousel-nav-btn position-absolute top-50 start-0 translate-middle-y ms-2 ms-md-4" aria-label="Previous project" @click="previousProject">
                <i class="bi bi-arrow-left"></i>
            </button>

            <div class="carousel-stage-container" id="carouselStage">
                <ProjectsCard
                    v-for="(project, index) in projects"
                    :key="project.id"
                    :project="project"
                    :index="index"
                    :slide-class="slideClass(index)"
                    :default-project-image="defaultProjectImage"
                    @select="selectAdjacentProject"
                    @preview="openProjectLightbox"
                    @opened="notifyProjectOpened"
                />
            </div>

            <button type="button" id="nextBtn" class="carousel-nav-btn position-absolute top-50 end-0 translate-middle-y me-2 me-md-4" aria-label="Next project" @click="nextProject">
                <i class="bi bi-arrow-right"></i>
            </button>
            </div>

            <div class="section-spacer-2"></div>
            
            <!-- Pagination Dots -->
            <div class="d-flex justify-content-center align-items-center gap-2 mt-5" id="paginationDots">
                <button
                    v-for="(project, index) in projects"
                    :key="project.id"
                    type="button"
                    class="pagination-dot"
                    :class="{ active: index === currentIndex }"
                    :aria-label="`Go to slide ${index + 1}`"
                    :aria-current="index === currentIndex ? 'true' : 'false'"
                    @click="currentIndex = index"
                ></button>
            </div>

            <div class="resume-download-wrap">
            <a
                href="./miranda-resume.pdf"
                download="Raniel-Miranda-Resume.pdf"
                class="resume-download-btn"
            >
                <svg class="resume-download-icon" fill="none" stroke="currentColor" viewBox="0 0 24 24" aria-hidden="true">
                <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"
                ></path>
                </svg>
                <span>Download Resume</span>
                <span class="resume-download-badge">PDF</span>
            </a>
            <span class="resume-download-file">Raniel-Miranda-Resume.pdf</span>
            </div>

            <div v-if="actionNoticeVisible" class="text-center mt-3">
                <span class="p-2 px-3 text-xs font-mono-code text-emerald-700 bg-emerald-50 rounded border border-emerald-200 d-inline-block">
                    <i class="bi bi-check2-circle me-1"></i>
                    {{ actionNoticeText }}
                </span>
            </div>

        </div>
    </section>

    <div
        v-if="selectedProject"
        class="project-lightbox"
        :class="{ show: lightboxVisible }"
        role="dialog"
        aria-modal="true"
        :aria-label="`${selectedProject.title} preview`"
        @click.self="closeProjectLightbox"
    >
        <div class="project-lightbox-panel">
            <img
                class="project-lightbox-image"
                :src="selectedProject.imageBg || defaultProjectImage"
                :alt="`${selectedProject.title} full preview`"
                loading="eager"
                @error="$event.currentTarget.src = defaultProjectImage"
            />
            <div class="project-lightbox-label">{{ selectedProject.title }}</div>
        </div>
    </div>
</template>