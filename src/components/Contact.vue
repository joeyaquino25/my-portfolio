<template>
	
	<div class="container my-5" id="contact">
	    <div class="row justify-content-center gap-2 mt-5 mb-5">
	        <div class="col-10 order-2 order-md-1 col-md-5 my-1" id="contact-map-area">
	            <img src="/images/contact-map.png" class="img-fluid" id="contact-map-image">
	        </div>

	        <div class="col-10 order-1 order-md-2 col-md-5 my-1 " id="contact-form-area">

	            <form  class="px-3 pt-3 mx-auto align-items-center" id="contact-form" @submit.prevent="submitForm">   

	                <h2>Contact Me</h2> 
	                <p>Please let me know how I can help you.</p>
	             
	                <div>
	                  <label for="exampleFormControlInput1" class="form-label">Name</label>
	                  <input type="text" class="form-control" id="exampleFormControlInput1" placeholder="Your name">
	                </div>

	                <div class="pt-1">
	                  <label for="exampleFormControlInput1" class="form-label">Email address</label>
	                  <input type="email" class="form-control" id="exampleFormControlInput1" placeholder="your.email@gmail.com">
	                </div>

	                <div class="pt-1 mb-2">
	                  <label for="exampleFormControlTextarea1" class="form-label">Message</label>
	                  <textarea class="form-control" id="contact-textarea" rows="3"></textarea>
	                </div>

	                <div class="text-center form-footer">
	                     <div class="social-icons">
                            <a href="https://www.linkedin.com/in/charles-babbage-8291a6211/" id="linkedin"><i class="fab fa-linkedin"></i></a>
                            <a href="https://gitlab.com/cbabbage0991" id="gitlab"><i class="fab fa-gitlab"></i></a>
                            <a href="https://github.com/cbabbage0991" id="github"><i class="fab fa-github"></i></a>
                        </div>
                        <button type="submit" class="contact-form-button">Submit</button>
	                </div>

	                <!-- recaptcha checkbox -->
                    <div class="d-flex justify-content-end mt-2">
                        <div ref="recaptchaContainer"></div>
                    </div>
	                                                                               
	            </form>

	        </div>               
	    </div>
	</div>
	
</template>

<script setup>

	import {ref, onMounted, onBeforeUnmount} from "vue";

	import {Notyf} from "notyf";
	import "notyf/notyf.min.css";

	const notyf = new Notyf();

	const WEB3FORMS_ACCESS_KEY = "a8d25625-1509-49c1-83a6-c893a7d3252a";

	// email subject
	const subject = "New message from Portfolio Contact form";

	const name = ref("");
	const email = ref("");
	const message = ref("");

	const isLoading = ref(false);

	const submitForm = async()=> {

		// Check if reCAPTCHA token is present, return an error when not verified
		if(!recaptchaToken.value){
			notyf.error("Please verify that you are not a robot")
			return;
		}

		isLoading.value = true;


		try{
			const response = await fetch("https://api.web3forms.com/submit", {
				method: "POST",
				headers: {
					"Content-Type" : "application/json",
					Accept: "application/json"
				},
				body: JSON.stringify({
					access_key: WEB3FORMS_ACCESS_KEY,
					subject: subject,
					name: name.value,
					email: email.value,
					message: message.value
				})
			})

			const result = await response.json();

			if(result.success){
				console.log(result);
				isLoading.value = false;
				notyf.success("Message Sent!");
			}
			else{
				isLoading.value = false;
				notyf.error("Failed to send message");
			}
		}
		catch(error){
			console.log(error);
			isLoading.value = false;
			notyf.error("Failed to send message");
		}
		

	}
	


	const SITE_KEY = '6LdvaH8sAAAAAGX0yzbURLoi4fGK7xDS2cu1WqGU';  

	const recaptchaContainer = ref(null);
	const recaptchaWidgetId = ref(null);
	const recaptchaToken = ref('');

	// Callback called by reCAPTCHA when successful
	function onRecaptchaSuccess(token) {
		console.log(token)
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
	  // This code waits for the Google reCAPTCHA library to load, then renders the reCAPTCHA widget using onMounted hook. 
	  // The widget is rendered with grecaptcha.render(), which requires a sitekey. 
	  // Callback functions handle success and expiration events. 
	  // reCAPTCHA is reset upon form submission to clear the token.
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
