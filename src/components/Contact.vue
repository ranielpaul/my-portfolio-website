<script setup>
    import { onUnmounted, ref, onMounted, onBeforeUnmount } from 'vue'
    import axios from 'axios';
    import { Notyf } from 'notyf';
    import 'notyf/notyf.min.css';

	const notyf = new Notyf();

    // Form
    const WEB3FORMS_ACCESS_KEY ='8295ec4a-e929-4df6-b7ae-31be2aca2f8c';
    const form = ref(null)

    // Form Statuses
    const isSending = ref(false)
    const showSuccess = ref(false)

    // Form data
    const subject = 'New message from Portfolio Contact Form';
    const sentDetails = ref({ name: '', subject: '', email: '' })
    const formValues = ref({ name: '', email: '', subject: '', message: '' })
    let submissionTimeout
    
    const submitForm = async () => {
        if (!form.value?.reportValidity() || !formValues.value.name || !formValues.value.email) return

        if (!recaptchaToken.value) {
                notyf.error('Please verify that you are not a robot.');
                return;
            }		

        isSending.value = true

		try {
			const response = await axios.post('https://api.web3forms.com/submit', {
				access_key: WEB3FORMS_ACCESS_KEY,
				subject: subject,
                name: formValues.value.name,
                subject: formValues.value.subject || 'General Inquiry',
                email: formValues.value.email,
			}, {
				headers: {
					'Content-Type': 'application/json',
					Accept: 'application/json'
				}
			});

			const result = response.data;

			if (result.success) {
				notyf.success('Message Sent!');
			}
		}
		catch (error) {
			notyf.error("Failed to send email!");
		}
		finally {
			isSending.value = false;
		}
	};

    function resetForm() {
        clearTimeout(submissionTimeout)
        formValues.value = { name: '', email: '', subject: '', message: '' }
        sentDetails.value = { name: '', subject: '', email: '' }
        showSuccess.value = false
        isSending.value = false
    }

    onUnmounted(() => clearTimeout(submissionTimeout))

    // Captcha 

	const SITE_KEY='6LcqphouAAAAABIZ9o8-LNTi6EeMaduBGxumdI6v'
	const SECRET_KEY='6LcqphouAAAAAJ27niEKF9ccKLJCBjzxeZI0d4Ph'	
        
	const recaptchaContainer = ref(null);
	const recaptchaWidgetId = ref(null);
	const recaptchaToken = ref('');

	// Callback called by reCAPTCHA when successful
	function onRecaptchaSuccess(token) {
	recaptchaToken.value = token;
	}

	// Callback when expired
	function onRecaptchaExpired() {
	recaptchaToken.value = '';
	}

	// Function to render the reCAPTCHA widget
	function renderRecaptcha() {
	if (!window.grecaptcha) {
		console.error('reCAPTCHA not loaded');
		return;
	}

	recaptchaWidgetId.value = window.grecaptcha.render(recaptchaContainer.value, {
		sitekey: SITE_KEY,
		size: 'normal', // or 'compact'
		callback: onRecaptchaSuccess,
		'expired-callback': onRecaptchaExpired,
	});
	}

	// Function to reset reCAPTCHA 
	function resetRecaptcha() {
		if (recaptchaWidgetId.value !== null) {
			window.grecaptcha.reset(recaptchaWidgetId.value);
			recaptchaToken.value = '';
		}
	}

	onMounted(() => {
		const interval = setInterval(() => {
			if (window.grecaptcha && window.grecaptcha.render) {
			renderRecaptcha();
			clearInterval(interval);
			}
		}, 100);

		onBeforeUnmount(() => {
			clearInterval(interval);
		});
	});

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
                    
                <!-- recaptcha checkbox -->
                <div class="col-12 mt-2 d-flex justify-content-center">
                    <div ref="recaptchaContainer"></div>
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