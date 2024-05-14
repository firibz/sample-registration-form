<template>
    <div class="registration-form">
        <h2>Step: {{ stepTitles[currentStep - 1] }}</h2>
        <div v-if="currentStep === 1">
            <label for="username">Username:</label>
            <input
                    type="text"
                    id="username"
                    name="username"
                    v-model="formData.username"
                    :class="{ error: hasError('username') }"
                    @keyup.enter="nextStep"
                    @update:model-value="validateUsername"
            />
            <div v-if="hasError('username')">{{ errors.username }}</div>
        </div>
        <div v-else-if="currentStep === 2">
            <label for="email">Email:</label>
            <input
                    type="text"
                    id="email"
                    name="email"
                    v-model="formData.email"
                    :class="{ error: hasError('email') }"
                    @keyup.enter="nextStep"
            />
            <div v-if="hasError('email')">{{ errors.email }}</div>
        </div>
        <div v-else>
            <p>Review your information:</p>
            <p>Username: {{ formData.username }}</p>
            <p>Email: {{ formData.email }}</p>
        </div>
        <div class="buttons">
            <button id="btn-prev" :disabled="currentStep === 1" @click="prevStep">Previous</button>
            <button id="btn-next" :disabled="currentStep === 3" @click="nextStep">Next</button>
        </div>
    </div>
</template>

<script>
import { ref, computed } from 'vue';

export default {
    setup() {
        const currentStep = ref(1);
        const formData = ref({ username: '', email: '' });
        const errors = ref({});
        const stepTitles = ['username', 'email', 'review'];

        const hasError = (field) => errors.value.hasOwnProperty(field);
        const emailRegex = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/

        const canNext = computed(() => {
            switch (currentStep.value) {
                case 1:
                    return formData.value.username.length > 0;
                case 2:
                    return formData.value.email.length > 0;
                default:
                    return true;
            }
        });

        const validateEmail = (email) => {
            let hasError = false;
            if(!email){
                hasError = true;
            }
            if(!emailRegex.test(email)){
                hasError = true;
            }

            if(hasError){
                errors.value['email'] =  'Invalid email address.'
                return false;
            }
            else {
                delete errors.value['email'];
                return true;
            }
        };
        const validateUsername = (username) => {
            let hasError = false;
            if(!username){
                hasError = true;
            }
            else if(/\s/.test(username)){
                hasError = true
            }
            else if(username.length<=3){
                hasError = true
            }
            else if(username.length>50){
                hasError = true
            }

            if(hasError){
                errors.value['username'] =  'Invalid Username.'
                return false;
            }
            else {
                delete errors.value['username'];
                return true;
            }
        };

        const nextStep = () => {
            switch (currentStep.value) {
                case 1:
                    if(validateUsername(formData.value.username)){
                        currentStep.value++;
                    }
                    break
                case 2:
                    if(validateEmail(formData.value.email)){
                        currentStep.value++;
                    }
                    break;
                default:
                    currentStep.value++;
            }
        };

        const prevStep = () => {
            if (currentStep.value > 1) {
                currentStep.value--;
            }
        };

        return { currentStep, formData, errors, stepTitles, hasError, canNext, nextStep, prevStep, validateUsername, validateEmail };
    },
};
</script>