<script setup>
import { onMounted, onUnmounted, ref } from 'vue'

const activeHash = ref('#landing')
const navItems = [
  { hash: '#landing', label: 'Home' },
  { hash: '#projects', label: 'Projects' },
  { hash: '#tools', label: 'Tools' },
  { hash: '#contact', label: 'Contact' },
]

function updateActiveNav() {
  const offset = window.innerHeight * 0.25
  const sections = document.querySelectorAll('main section[id]')
  let visibleHash = '#landing'

  sections.forEach((section) => {
    const rect = section.getBoundingClientRect()
    if (rect.top <= offset && rect.bottom > offset) {
      visibleHash = `#${section.id}`
    }
  })

  activeHash.value = visibleHash
}

function activateNav(hash) {
  activeHash.value = hash
}

onMounted(() => {
  window.addEventListener('scroll', updateActiveNav, { passive: true })
  window.addEventListener('load', updateActiveNav)
  updateActiveNav()
})

onUnmounted(() => {
  window.removeEventListener('scroll', updateActiveNav)
  window.removeEventListener('load', updateActiveNav)
})
</script>

<template>
    <header class="site-header py-3 px-md-4 bg-white  sticky-top border-bottom border-light shadow-sm">
      <div class="container-xl d-flex align-items-center gap-4 gap-md-0 justify-content-md-between">
        <!-- Left: Brand Badge & Name -->
        <a href="#landing" class="brand-link d-flex align-items-center gap-2 text-decoration-none" @click="activateNav('#landing')">
          <div class="brand-badge d-flex align-items-center justify-content-center text-white fw-bold rounded-2 text-xs">
            RM
          </div>
          <span class="d-none d-sm-flex fs-6 fw-bold text-dark ">
            Raniel Paul Miranda
          </span>
        </a>

        <!-- Center: Nav Links -->
        <nav id="mainNav" class=" nav-pill-container" aria-label="Main Navigation">
          <a
            v-for="item in navItems"
            :key="item.hash"
            class="nav-pill-btn text-decoration-none"
            :class="{ active: activeHash === item.hash }"
            :href="item.hash"
            :aria-current="activeHash === item.hash ? 'page' : 'false'"
            @click="activateNav(item.hash)"
          >{{ item.label }}</a>
        </nav>

        <!-- Right: Available for Hire Status Badge -->
        <div class="d-none d-sm-flex align-items-center">
          <div class="status-badge-hire">
            <span class="pulsing-green-dot"></span>
            <span>AVAILABLE FOR HIRE</span>
          </div>
        </div>
      </div>
    </header>
</template>