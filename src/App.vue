<script setup>
import { ref, reactive } from 'vue'

const isRegister = ref(false)
const confirmPass = ref()
const isAuth = ref(false)
const isRejectToLogin = ref(false)

const data = reactive({
  username: '',
  password: ''
})

const errorMsg = reactive({
  username: '',
  password: '',
  confirmPass: ''
})

function register() {
  isRejectToLogin.value = false
  resetErrorMsg()
  if (isRegister.value && data.username && data.password) {
    if (data.password === confirmPass.value) {
      localStorage.setItem('authInfo', JSON.stringify(data))
      isAuth.value = true
    }
  }

  if (isRegister.value) {
    validation()
  }

  isRegister.value = true

}

function signIn() {
  validation()

  if (isRegister.value) {
    resetErrorMsg()
  }

  isRegister.value = false

  if (data.username && data.password) {

    let auth = JSON.parse(localStorage.getItem('authInfo'))
    if (data.username === auth?.username && data.password === auth?.password) {
      isAuth.value = true
    } else {
      isRejectToLogin.value = true
    }
  }
}

function validation() {
  errorMsg.username = data.username ? '' : "Username filed is required!"
  errorMsg.password = data.password ? '' : "Password filed is required!"
  if (isRegister.value) {
    errorMsg.confirmPass = data.confirmPass ? '' : "Password doesn't mach!"
  }
}

function resetErrorMsg() {
  errorMsg.username = ''
  errorMsg.password = ''
  errorMsg.confirmPass = ''
}

function logout() {
  isAuth.value = false
  resetErrorMsg()
  isRegister.value = false

  // reset the data object
  data.username = ''
  data.password = ''
  confirmPass.value = ''
}
</script>

<template>
  <section v-show="!isAuth" class="flex w-full min-h-screen">

    <div class="w-1/2"
      style="background-image: url(https://images.unsplash.com/photo-1591167636260-ab0670b955b3?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=774&q=80); background-repeat: no-repeat; background-size: cover;">
      <div class="mt-10 mb-5 border-4 border-l-0 bg-gray-700/60 border-white/50 rounded-r-3xl w-72">
        <h1 class="py-6 ml-10 text-6xl font-bold text-yellow-500">Osthir!</h1>
      </div>
    </div>


    <div class="flex flex-col items-center justify-center w-1/2 bg-gray-200">

      <h2 class="mb-5 text-xl">Login or register</h2>

      <div class="w-full max-w-md">
        <form class="px-8 pt-6 pb-8 mb-4 bg-white rounded shadow-md">

          <!-- Alert message -->
          <div v-show="isRejectToLogin" class="px-5 py-2 mb-4 bg-red-100 border border-red-200 rounded-lg">
            ❗ Please provide valid credentials or <span @click="register"
              class="font-bold text-green-600 cursor-pointer">Register</span>
          </div>

          <div class="mb-4">
            <label class="block mb-2 text-sm font-bold" :class="errorMsg.username ? 'text-red-400' : 'text-gray-700'"
              for="username">
              Username
            </label>
            <input id="username"
              class="w-full px-3 py-2 leading-tight border rounded shadow appearance-none focus:outline-none focus:shadow-outline"
              :class="errorMsg.username ? 'border-red-400' : 'text-gray-700'" v-model="data.username" type="text"
              placeholder="Username">

            <p v-show="errorMsg.username" class="text-red-400">{{ errorMsg.username }}</p>
          </div>

          <div class="mb-4">
            <label class="block mb-2 text-sm font-bold" :class="errorMsg.password ? 'text-red-400' : 'text-gray-700'"
              for="password">
              Password
            </label>
            <input id="password"
              class="w-full px-3 py-2 leading-tight border rounded shadow appearance-none focus:outline-none focus:shadow-outline"
              :class="errorMsg.password ? 'border-red-400' : 'text-gray-700'" type="password" v-model="data.password"
              placeholder="******************">

            <p v-show="errorMsg.password" class="text-red-400">{{ errorMsg.password }}</p>
          </div>

          <div v-show="isRegister" class="mb-4">
            <label class="block mb-2 text-sm font-bold" :class="errorMsg.confirmPass ? 'text-red-400' : 'text-gray-700'"
              for="confirmPassword">
              Confirm Password
            </label>
            <input
              class="w-full px-3 py-2 mb-3 leading-tight border rounded shadow appearance-none focus:outline-none focus:shadow-outline"
              :class="errorMsg.confirmPass ? 'border-red-400' : 'text-gray-700'" id="confirmPassword" type="password"
              v-model="confirmPass" placeholder="******************">

            <p v-show="errorMsg.confirmPass" class="text-red-400">{{ errorMsg.confirmPass }}</p>
          </div>

          <!-- Form submission buttons -->
          <div class="flex items-center justify-between mt-2">
            <button @click="signIn" class="px-4 py-2 font-bold focus:outline-none focus:shadow-outline"
              :class="!isRegister ? 'text-white bg-orange-600 rounded hover:bg-orange-700' : 'text-orange-600 align-baseline hover:text-orange-800'"
              type="button">
              Sign In
            </button>

            <span class="font-bold text-gray-700">OR</span>

            <button class="inline-block px-4 py-2 font-bold"
              :class="isRegister ? 'text-white bg-orange-600 rounded hover:bg-orange-700' : 'text-orange-600 align-baseline hover:text-orange-800'"
              @click="register">
              Register
            </button>
          </div>

        </form>
        <p class="text-xs text-center text-gray-500">
          &copy;2020 Acme Corp. All rights reserved.
        </p>
      </div>
    </div>

  </section>


  <!-- Welcome Section for valid user -->
  <section v-show="isAuth">
    <div class="flex flex-col items-center justify-center h-screen p-5 mx-auto rounded-lg max-w-7xl bg-slate-100">
      <h2 class="text-4xl font-bold">
        Welcome! <span class="text-green-600">{{ data.username }}</span>, To Osthir ✔ Theater
      </h2>

      <button
        class="relative inline-flex items-center justify-start px-6 py-3 overflow-hidden font-medium transition-all bg-white border border-yellow-400 rounded mt-14 hover:bg-white group"
        @click="logout">

        <span
          class="w-48 h-48 rounded rotate-[-40deg] bg-purple-600 absolute bottom-0 left-0 -translate-x-full ease-out duration-500 transition-all translate-y-full mb-9 ml-9 group-hover:ml-0 group-hover:mb-32 group-hover:translate-x-0"></span>
        <span
          class="relative flex w-full text-left text-black transition-colors duration-300 ease-in-out group-hover:text-white">
          Logout
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"
            class="w-6 h-6 ml-3">
            <path stroke-linecap="round" stroke-linejoin="round"
              d="M15.75 9V5.25A2.25 2.25 0 0013.5 3h-6a2.25 2.25 0 00-2.25 2.25v13.5A2.25 2.25 0 007.5 21h6a2.25 2.25 0 002.25-2.25V15m3 0l3-3m0 0l-3-3m3 3H9" />
          </svg>

        </span>
      </button>

    </div>
  </section>
</template>
<style scoped></style>
