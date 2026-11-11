<script setup>
import { onMounted, onUnmounted, ref } from 'vue'

const words = ['Precision', 'Purpose', 'Performance']
const typedText = ref('')
let wordIndex = 0
let characterIndex = 0
let isDeleting = false
let typingTimeout

function typeLoop() {
    const word = words[wordIndex]
    characterIndex += isDeleting ? -1 : 1
    typedText.value = word.slice(0, characterIndex)

    if (!isDeleting && characterIndex === word.length) {
        isDeleting = true
        typingTimeout = setTimeout(typeLoop, 1200)
        return
    }

    if (isDeleting && characterIndex === 0) {
        isDeleting = false
        wordIndex = (wordIndex + 1) % words.length
        typingTimeout = setTimeout(typeLoop, 350)
        return
    }

    typingTimeout = setTimeout(typeLoop, isDeleting ? 60 : 90)
}

onMounted(typeLoop)
onUnmounted(() => clearTimeout(typingTimeout))
</script>

<template>
<!-- Section 1: Home / Landing -->
    <section id="landing" class="min-vh-90 d-flex align-items-center pt-4 pb-3 home-gradient-bg position-relative">
        <div class="home-ambient-glow" aria-hidden="true"></div>
        <div class="home-ambient-glow-secondary" aria-hidden="true"></div>
        <div class="container-xl position-relative landing-shell">
            <div class="row g-5 align-items-center">
            <!-- Left Column: Headline, Bio, and Buttons -->
            <div class="col-lg-6 text-center text-lg-start">
                <div class="d-flex flex-column gap-3 align-items-center align-items-lg-start text-center text-lg-start">
                <!-- Soft Blue Kicker Pill -->
                <div>
                    <span class="landing-kicker px-3 py-1 rounded-pill text-xs fw-semibold d-inline-block shadow-sm">
                    Software Engineer &amp; Full Stack Web Developer
                    </span>
                </div>

                <!-- Display Headline -->
                <h1 class="landing-title font-display display-4 text-center text-lg-start mb-1">
                    Crafting digital
                    products with
                    <span class="landing-highlight display-4 font-display d-block">
                    {{ typedText }}
                    </span>
                </h1>

                <!-- Narrative Subtext -->
                <p class="landing-description text-secondary fs-6 mb-3 pe-lg-4 mx-auto mx-lg-0 text-center text-lg-start">
                    I engineer resilient web architectures, performant user interfaces,
                    and intuitive interactive experiences using modern web ecosystems
                    and clean design standards.
                </p>

                <!-- 2 Main Action Buttons -->
                <div class="d-flex flex-wrap align-items-center gap-3 mb-4">
                    <a href="#projects" class="btn-blue-primary text-decoration-none">
                    <span>View Projects</span>
                    <i class="bi bi-arrow-right"></i>
                    </a>
                    <a href="#contact" class="btn-white-secondary text-decoration-none">
                    <span>Get in Touch</span>
                    </a>
                </div>

                <!-- Core Technology Focus -->
                <div class="pt-2 ">
                    <div class="tech-focus-label font-mono-code mb-2">
                    CORE TECHNOLOGY FOCUS
                    </div>
                    <div class="d-flex flex-wrap gap-2 justify-content-center justify-content-lg-start">
                    <div class="tech-pill px-3 py-1 rounded-2 bg-white  border border text-dark text-xs d-flex align-items-center gap-2 shadow-sm">
                        <i class="bi bi-layers text-indigo-500"></i>
                        <span>Full-Stack</span>
                    </div>
                    <div class="tech-pill px-3 py-1 rounded-2 bg-white  border border text-dark text-xs d-flex align-items-center gap-2 shadow-sm">
                        <i class="bi bi-window-sidebar text-blue-500"></i>
                        <span>UI Architecture</span>
                    </div>
                    <div class="tech-pill px-3 py-1 rounded-2 bg-white  border border text-dark text-xs d-flex align-items-center gap-2 shadow-sm">
                        <i class="bi bi-cloud-arrow-up text-sky-500"></i>
                        <span>Cloud &amp; API</span>
                    </div>
                    </div>
                </div>
                </div>
            </div>

            <!-- Right Column: Fake VS Code Editor Recreation -->
            <div class="col-lg-6">
                <div class="vscode-card">
                <!-- Header -->
                <div class="vscode-header">
                    <div class="traffic-dots">
                    <span class="dot-red"></span>
                    <span class="dot-yellow"></span>
                    <span class="dot-green"></span>
                    </div>
                    <div class="font-mono-code text-xs text-light">
                    miranda-portfolio-v1.tsx
                    </div>
                </div>

                <!-- Syntax Highlighted Code Body -->
                <div class="vscode-body">
                    <div class ="section-spacer-2"></div>
                    <div>
                    <span class="code-keyword">const</span> <span class="code-variable">Software_developer</span> = {
                    </div>
                    <div class="ps-4">
                    <span class="code-prop">name:</span> <span class="code-string">'Raniel Paul'</span>,
                    </div>
                    <div class="ps-4">
                    <span class="code-prop">role:</span> <span class="code-string">'Software Enginee &amp; Full Stack Web Developer'</span>,
                    </div>
                    <div class="ps-4">
                    <span class="code-prop">skills:</span> [
                    <span class="code-string">'Bootstrap'</span>,
                    <span class="code-string">'React'</span>,
                    <span class="code-string">'Python'</span>,
                    <span class="code-string">'C#'</span>],
                    </div>
                    <div class="ps-4">
                    <span class="code-prop">passion:</span> <span class="code-string">'Building exquisite user software'</span>
                    </div>
                    <div>};</div>
                    <div class ="section-spacer-2"></div>
                </div>

                <!-- Card Footer -->
                <div class="vscode-footer">
                    <div>
                    <div class="vscode-metric-label font-mono-code">
                        System Performance
                    </div>
                    <div class="text-white font-mono-code text-xs fw-semibold d-flex align-items-center gap-2">
                        <span>99.98%</span>
                        <span class="vscode-metric-gain">▲ 100 Efficiency</span>
                    </div>
                    </div>

                    <div class="vscode-optimal-pill px-2 py-1 rounded text-xs font-mono-code fw-semibold d-flex align-items-center gap-1">
                    OPTIMAL
                    </div>
                </div>
                </div>
            </div>
            </div>
        </div>
    </section>
</template>