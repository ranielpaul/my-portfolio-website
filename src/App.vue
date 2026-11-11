<script setup>
  import Navbar from './components/Navbar.vue'
  import HomeLanding from './components/HomeLanding.vue'
  import Projects from './components/Projects.vue'
  import Tools from './components/Tools.vue'
  import Contact from './components/Contact.vue'
  import Footer from './components/Footer.vue'
  
</script>

<template>
  <Navbar />
  
  <main class="flex-grow-1">
    <HomeLanding />
    <Projects />
    <Tools />
    <Contact />
  </main>

  <Footer />

</template>
