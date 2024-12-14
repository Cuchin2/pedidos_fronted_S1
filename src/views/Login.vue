<template>
    <div class="min-h-screen page-bg flex items-center justify-center p-4">
        <div class="bg-white rounded-2xl shadow-lg w-full max-w-sm p-8 space-y-6">
            <!-- Header -->
            <div class="text-center">
                <svg class="mx-auto h-10 w-10 text-blue-500" xmlns="http://www.w3.org/2000/svg" fill="none"
                    viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                        d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z" />
                </svg>
                <h2 class="mt-4 text-2xl font-bold text-gray-900">Welcome Back</h2>
                <p class="mt-2 text-sm text-gray-500">Sign in to continue</p>
            </div>

            <!-- Login Form -->
            <form @submit.prevent="handleLogin" class="space-y-5">
                <div class="floating-input">
                    <input id="email" v-model="email" type="email" required placeholder=" "
                        class="mt-1 block w-full px-3 bg-gray-50 border border-gray-200 rounded-lg shadow-sm focus:outline-none focus:ring-1 focus:ring-blue-500 focus:border-blue-500 text-gray-900">
                    <label for="email" class="floating-label">Email</label>
                </div>

                <div class="floating-input">
                    <input id="password" v-model="password" type="password" required placeholder=" "
                        class="mt-1 block w-full px-3 bg-gray-50 border border-gray-200 rounded-lg shadow-sm focus:outline-none focus:ring-1 focus:ring-blue-500 focus:border-blue-500 text-gray-900">
                    <label for="password" class="floating-label">Password</label>
                </div>

                <div class="flex items-center justify-between">
                    <div class="flex items-center">
                        <input id="remember" v-model="remember" type="checkbox"
                            class="h-4 w-4 text-blue-500 focus:ring-blue-500 border-gray-300 rounded">
                        <label for="remember" class="ml-2 block text-sm text-gray-600">
                            Remember me
                        </label>
                    </div>

                    <a href="https://example.com/forgot-password"
                        class="text-sm font-medium text-blue-500 hover:text-blue-600">
                        Forgot password?
                    </a>
                </div>

                <button type="submit" :disabled="isLoading"
                    class="btn-primary w-full flex justify-center py-2.5 px-4 border border-transparent rounded-lg shadow-sm text-sm font-medium text-white bg-blue-500 hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500 disabled:opacity-50 disabled:cursor-not-allowed">
                    <span v-if="isLoading">
                        <svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg"
                            fill="none" viewBox="0 0 24 24">
                            <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4">
                            </circle>
                            <path class="opacity-75" fill="currentColor"
                                d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z">
                            </path>
                        </svg>
                        Signing in...
                    </span>
                    <span v-else>Sign in</span>
                </button>
            </form>

            <!-- Register Link -->
            <div class="text-center">
                <p class="text-sm text-gray-500">
                    Don't have an account?
                    <a href="https://example.com/register" class="font-medium text-blue-500 hover:text-blue-600">
                        Create one now
                    </a>
                </p>
            </div>

            <!-- Error Message -->
            <div v-if="errorMessage" class="bg-red-50 border-l-4 border-red-400 p-4 rounded-r-lg">
                <div class="flex">
                    <div class="flex-shrink-0">
                        <svg class="h-5 w-5 text-red-400" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"
                            fill="currentColor">
                            <path fill-rule="evenodd"
                                d="M10 18a8 8 0 100-16 8 8 0 000 16zM8.707 7.293a1 1 0 00-1.414 1.414L8.586 10l-1.293 1.293a1 1 0 101.414 1.414L10 11.414l1.293 1.293a1 1 0 001.414-1.414L11.414 10l1.293-1.293a1 1 0 00-1.414-1.414L10 8.586 8.707 7.293z"
                                clip-rule="evenodd" />
                        </svg>
                    </div>
                    <div class="ml-3">
                        <p class="text-sm text-red-600">{{ errorMessage }}</p>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const email = ref<string>('')
const password = ref<string>('')
const remember = ref<boolean>(false)
const isLoading = ref<boolean>(false)
const errorMessage = ref<string>('')

const handleLogin = async (): Promise<void> => {
    isLoading.value = true
    errorMessage.value = ''

    try {
        // Simulate API call
        await new Promise<void>(resolve => setTimeout(resolve, 2000))

        console.log('Login attempt:', {
            email: email.value,
            password: password.value,
            remember: remember.value
        })

        // Here you would typically make an API call to your backend
        if (email.value !== 'demo@example.com' || password.value !== 'password') {
            throw new Error('Invalid email or password')
        }

        // Redirect on success
        window.location.href = 'https://example.com/dashboard'

    } catch (error: unknown) {
        if (error instanceof Error) {
            errorMessage.value = error.message
        } else {
            errorMessage.value = 'An unknown error occurred'
        }
        console.error('Login failed:', error)
    } finally {
        isLoading.value = false
    }
}
</script>


<style>
.page-bg {
    background: linear-gradient(145deg, #f0f2f5 0%, #ffffff 100%);
}

.fade-enter-active,
.fade-leave-active {
    transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
    opacity: 0;
}

.floating-input {
    position: relative;
    margin-bottom: 1.5rem;
}

.floating-label {
    position: absolute;
    left: 0.75rem;
    top: 50%;
    transform: translateY(-50%);
    transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
    pointer-events: none;
    color: #6B7280;
}

.floating-input input:focus~.floating-label,
.floating-input input:not(:placeholder-shown)~.floating-label {
    transform: translateY(-200%) scale(0.85);
    color: #3B82F6;
}

.floating-input input {
    padding-top: 0.5rem;
    padding-bottom: 0.5rem;
    height: 2.5rem;
    transition: all 0.2s ease-in-out;
}

.floating-input input:focus {
    box-shadow: 0 0 0 2px rgba(59, 130, 246, 0.1);
    border-color: #3B82F6;
}

.btn-primary {
    transition: all 0.3s ease;
    transform: translateY(0);
}

.btn-primary:hover {
    transform: translateY(-1px);
    box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
}
</style>