<script setup>

import { ref, nextTick } from 'vue';

// Reactive array to store each digit
const code = ref(['', '', '', '', '', '']);

// Verification ref to scope our querySelector
const verification = ref(null);

const onlyNumbers = (event) => {
    const key = event.key;
    // Allow control keys like Backspace, Arrow keys, etc.
    if (event.ctrlKey || event.metaKey || event.altKey) return;

    if (!/[0-9]/.test(key)) {
        event.preventDefault();
    }
};

// When input gets focus, select its content to force overwrite
const selectInput = (event) => {
    event.target.select();
};

// When a digit is entered, move focus to the next input
const handleInput = async (index, event) => {
    // With text selected on focus, any new key press overwrites the existing value.
    if (code.value[index].length === 1 && index < code.value.length - 1) {
    await nextTick(); // Wait for the DOM update
    const inputs = verification.value.querySelectorAll('input');
    inputs[index + 1]?.focus();
    }
};

// Handle paste event: fill inputs with pasted digits
const handlePaste = async (index, event) => {
    event.preventDefault();
    const pastedData = event.clipboardData.getData('text');
    const digits = pastedData.replace(/\D/g, '');
    if (!digits) return;
    // Fill code array starting from the current index
    for (let i = 0; i < digits.length && index + i < code.value.length; i++) {
    code.value[index + i] = digits[i];
    }
    // Wait for the DOM update, then focus on the next input after the pasted digits
    await nextTick();
    const inputs = verification.value.querySelectorAll('input');
    const nextIndex = Math.min(index + digits.length, code.value.length - 1);
    inputs[nextIndex]?.focus();
};

// If backspace is pressed on an empty input, focus the previous one
const handleBackspace = (index, event) => {
    if (event.key === 'Backspace' && code.value[index] === '' && index > 0) {
    const inputs = verification.value.querySelectorAll('input');
    inputs[index - 1]?.focus();
    }
};

// Retrieve the full code by joining the digits
const submitCode = () => {
    const verificationCode = code.value.join('');
    // You can have your API call here...
    // 
    // 
    // 
    console.log('Verification code:', verificationCode);
};
</script>

<template>
    <div ref="verification" class="h-auto w-full flex justify-center mb-20">
        <form @submit.prevent="submitCode"
            class="h-auto w-100 mt-10 flex flex-col items-center rounded-2xl ring-2 ring-slate-00">

            <h1 class="flex justify-center mt-10 font-gist text-xl font-medium mb-2">Verify Your Email</h1>
            <p class="
                w-75
                flex justify-center 
                mt-2 mb-2
                font-gist font-medium 
                text-xs text-slate-700 text-left
            ">We’ve sent a verification code to your email. Please enter the code below to confirm your email address and complete your registration.</p>
            <div class="flex space-x-2 mt-5">
                <input
                    v-for="(_, index) in code"
                    :key="index"
                    type="text"
                    maxlength="1"
                    :id="index"
                    inputmode="numeric"
                    pattern="[0-9]*"
                    @focus="selectInput"
                    @keypress="onlyNumbers($event)"
                    v-model="code[index]"
                    autocomplete="off"
                    class="w-12 h-12 text-center text-lg font-semibold border-2 border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-slate-500"
                    @input="handleInput(index,$event)"
                    @paste="handlePaste(index, $event)"
                    @keydown.backspace="handleBackspace(index, $event)"
                />
            </div>

            <button 
                type="submit" 
                class="
                flex justify-center items-center 
                w-55 h-8 
                mt-8 mb-10
                text-sm font-outfit text-white
                ring-2 rounded-md 
                transition-all duration-100 ease-in-out hover:bg-slate-800
                cursor-pointer 
                bg-slate-950
            ">
                Verify
            </button>
        </form>
    </div>
</template>