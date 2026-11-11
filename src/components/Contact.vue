<script setup>
import { onUnmounted, ref } from 'vue'

const form = ref(null)
const isSending = ref(false)
const showSuccess = ref(false)
const sentDetails = ref({ name: '', subject: '', email: '' })
const formValues = ref({ name: '', email: '', subject: '', message: '' })
let submissionTimeout

function submitForm() {
  if (!form.value?.reportValidity() || !formValues.value.name || !formValues.value.email) return

  isSending.value = true
  submissionTimeout = setTimeout(() => {
    sentDetails.value = {
      name: formValues.value.name,
      subject: formValues.value.subject || 'General Inquiry',
      email: formValues.value.email,
    }
    showSuccess.value = true
    isSending.value = false
  }, 700)
}

function resetForm() {
  clearTimeout(submissionTimeout)
  formValues.value = { name: '', email: '', subject: '', message: '' }
  sentDetails.value = { name: '', subject: '', email: '' }
  showSuccess.value = false
  isSending.value = false
}

onUnmounted(() => clearTimeout(submissionTimeout))
</script>

<template>
    <!-- Section 4: Contact Form -->
    <section id="contact" class="min-vh-100 d-flex flex-column justify-content-between pt-5">
        <div class="container-xl my-auto">
            <!-- Section Header -->
            <div class="text-center mb-5">
            <h2 class="font-display display-6 fw-bold text-dark mb-2">
                Contact Me
            </h2>
            <p class="text-secondary fs-6  mx-auto mb-0">
                Have an exciting idea, open position, or question? Send a message and let's collaborate.
            </p>
            </div>

            <!-- 4-Field Simplified Contact Form Card -->
            <div class="contact-card-shell mx-auto">
            <div class="standard-card p-4 p-md-5">
                <!-- Form Container -->
                <form v-if="!showSuccess" id="contactForm" ref="form" @submit.prevent="submitForm">
                <div class="row g-4">
                    <!-- Field 1: Name -->
                    <div class="col-md-6">
                    <label for="contactName" class="form-label text-xs fw-semibold text-dark mb-1">
                        Name
                    </label>
                    <input id="contactName" v-model="formValues.name" type="text" required placeholder="e.g. Elena Rostova" class="form-input-clean" />
                    </div>

                    <!-- Field 2: Email -->
                    <div class="col-md-6">
                    <label for="contactEmail" class="form-label text-xs fw-semibold text-dark mb-1">
                        Email
                    </label>
                    <input id="contactEmail" v-model="formValues.email" type="email" required placeholder="elena@company.com" class="form-input-clean" />
                    </div>

                    <!-- Field 3: Subject (Spans whole row) -->
                    <div class="col-12">
                    <label for="contactSubject" class="form-label text-xs fw-semibold text-dark mb-1">
                        Subject
                    </label>
                    <input id="contactSubject" v-model="formValues.subject" type="text" placeholder="Product collaboration / Contract Inquiry" class="form-input-clean" />
                    </div>

                    <!-- Field 4: Message (Spans whole row) -->
                    <div class="col-12">
                    <label for="contactMessage" class="form-label text-xs fw-semibold text-dark mb-1">
                        Message
                    </label>
                    <textarea id="contactMessage" v-model="formValues.message" rows="5" required placeholder="Write your note, timeline, or requirements here..." class="form-input-clean"></textarea>
                    </div>

                    <!-- Submit Button Centered -->
                    <div class="col-12 text-center pt-2">
                    <button type="submit" id="submitBtn" class="btn-blue-primary submit-btn px-4 py-2" :disabled="isSending">
                        <span v-if="isSending" class="spinner-border spinner-border-sm me-2" role="status"></span>
                        <span>{{ isSending ? 'Sending...' : 'Send Message' }}</span>
                        <i v-if="!isSending" class="bi bi-send-fill fs-6 ms-1"></i>
                    </button>
                    </div>
                </div>
                </form>

                <!-- Success Confirmation (Hidden by default) -->
                <div v-if="showSuccess" id="successNotice" class="text-center py-4">
                <div class="success-check-icon mx-auto rounded-circle d-flex align-items-center justify-content-center mb-3">
                    <i class="bi bi-check-lg fs-3"></i>
                </div>
                <h3 class="fs-4 fw-bold text-dark mb-2">Message Dispatched!</h3>
                <p class="text-secondary mb-4 mx-auto">
                    Thank you, <strong id="sentName">{{ sentDetails.name }}</strong>. Your note regarding &ldquo;<span id="sentSubject">{{ sentDetails.subject }}</span>&rdquo; has been sent. A confirmation has been routed to <strong id="sentEmail">{{ sentDetails.email }}</strong>.
                </p>
                <button type="button" id="resetBtn" class="btn-white-secondary" @click="resetForm">
                    Send Another Message
                </button>
                </div>
            </div>
            </div>
        </div>
    </section>
</template>