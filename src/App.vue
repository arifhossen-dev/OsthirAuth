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

/**
 * After getting info from user
 * checking validation 
 * ✅ if everything is ok then
 * 🔑 Encoding the password using btoa() encoder
 * 🏬 then storing it in localStore
 * ✅ and showing welcome message 
 */
function register() {
  isRejectToLogin.value = false
  resetErrorMsg()
  if (isRegister.value && data.username && data.password) {
    if (data.password === confirmPass.value) {
      data.password = btoa(data.password)
      localStorage.setItem('authInfo', JSON.stringify(data))
      isAuth.value = true
    }
  }

  if (isRegister.value) {
    validation()
  }

  isRegister.value = true

}

/**
 * On user login request
 * checking for validation
 *  🥽 then fetching data from storage
 *  🔑 then decoding the password
 *  🔑 check if given credentials match
 *  👌  then make auth true
 *  🙏 and showing welcome message 
 *  ❌ Or throw an rejection
 */
function signIn() {
  validation()

  if (isRegister.value) {
    resetErrorMsg()
  }

  isRegister.value = false

  if (data.username && data.password) {

    let auth = JSON.parse(localStorage.getItem('authInfo'))
    const decodedPassword = atob(auth?.password ?? '')

    if (data.username === auth?.username && data.password === decodedPassword) {
      isAuth.value = true
    } else {
      isRejectToLogin.value = true
    }
  }
}

/**
 * The easiest validation I had ever done 😁
 */
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

/**
 * on user logout request 
 * resetting everything as default
 * and make auth false
 */
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
  <div class="relative">
    <section v-show="!isAuth" class="flex w-full min-h-screen">

      <div class="w-1/2"
        style="background-image: url(https://images.unsplash.com/photo-1591167636260-ab0670b955b3?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=774&q=80); background-repeat: no-repeat; background-size: cover;">
        <div class="mt-10 mb-5 border-4 border-l-0 bg-gray-700/60 border-white/50 rounded-r-3xl w-72">
          <h1 class="py-6 ml-10 text-6xl font-bold text-yellow-500">Osthir!</h1>
        </div>
      </div>


      <div class="flex flex-col items-center justify-center w-1/2 bg-gradient-to-r from-rose-100 to-teal-100">


        <div style="border-radius: 40px;" class="w-full max-w-lg p-5 border-[20px] bg-white/40 rounded-2xl">
          <form class="px-8 pt-6 pb-8 shadow-md bg-white/70 rounded-2xl">
            <div class="flex justify-center">
              <h2 class="inline-flex px-3 mb-5 text-xl font-bold text-center text-gray-900 border-b-4 border-yellow-300">
                Login or register</h2>
            </div>

            <!-- Alert message -->
            <div v-show="isRejectToLogin" class="px-5 py-2 mb-4 bg-red-100 border border-red-200 rounded-lg">
              ❗ Please provide valid credentials or <span @click.prevent="register"
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

              <button
                class="relative inline-flex items-center justify-start px-6 py-2 overflow-hidden font-medium transition-all border border-yellow-400 rounded hover:bg-white group"
                :class="!isRegister ? 'bg-yellow-300' : 'bg-white'" @click.prevent="signIn">

                <span class=""
                  :class="!isRegister
                    ? 'w-48 h-48 rounded rotate-[-40deg] bg-purple-600 absolute bottom-0 left-0 -translate-x-full ease-out duration-500 transition-all translate-y-full mb-9 ml-9 group-hover:ml-0 group-hover:mb-32 group-hover:translate-x-0'
                    : 'w-48 h-48 rounded rotate-[40deg] bg-yellow-300 absolute top-0 right-0 translate-x-full ease-out duration-500 transition-all -translate-y-full mt-9 mr-9 group-hover:mr-0 group-hover:mt-32 group-hover:translate-x-0'"></span>

                <span class="relative flex w-full text-left text-black transition-colors duration-300 ease-in-out"
                  :class="!isRegister ? 'group-hover:text-white' : 'group-hover:text-black'">
                  Sign In

                </span>
              </button>

              <span class="font-bold text-gray-700">OR</span>

              <button
                class="relative inline-flex items-center justify-start px-6 py-2 overflow-hidden font-medium transition-all border border-yellow-400 rounded hover:bg-white group"
                :class="isRegister ? 'bg-yellow-300' : 'bg-white'" @click.prevent="register">

                <span class=""
                  :class="isRegister
                    ? 'w-48 h-48 rounded rotate-[-40deg] bg-purple-600 absolute bottom-0 left-0 -translate-x-full ease-out duration-500 transition-all translate-y-full mb-9 ml-9 group-hover:ml-0 group-hover:mb-32 group-hover:translate-x-0'
                    : 'w-48 h-48 rounded rotate-[40deg] bg-yellow-300 absolute top-0 right-0 translate-x-full ease-out duration-500 transition-all -translate-y-full mt-9 mr-9 group-hover:mr-0 group-hover:mt-32 group-hover:translate-x-0'"></span>

                <span class="relative flex w-full text-left text-black transition-colors duration-300 ease-in-out"
                  :class="isRegister ? 'group-hover:text-white' : 'group-hover:text-black'">
                  Register

                </span>
              </button>
            </div>

          </form>
        </div>
      </div>

    </section>


    <!-- Welcome Section for valid user -->
    <section v-show="isAuth">
      <div
        class="flex items-center justify-center h-screen p-5 mx-auto rounded-lg bg-gradient-to-r from-rose-100 to-teal-100">
        <div class="flex flex-col items-center p-32 bg-white/70 rounded-3xl">
          <h2 class="font-bold text-center text-7xl">
            Welcome!
            <span class="text-green-600">
              {{ data.username }}
            </span>,

            <span class="flex justify-center my-4">
              To
            </span>

            <span class="inline-flex px-3 pt-2 pb-4 bg-yellow-400 rounded-lg">
              The Osthir 🎦 Theater
            </span>
          </h2>

          <button
            class="relative inline-flex items-center justify-start px-6 py-3 overflow-hidden font-medium transition-all bg-white border border-yellow-400 rounded mt-14 hover:bg-white group"
            @click="logout">

            <span
              class="w-48 h-48 rounded rotate-[-40deg] bg-purple-600 absolute bottom-0 left-0 -translate-x-full ease-out duration-500 transition-all translate-y-full mb-9 ml-9 group-hover:ml-0 group-hover:mb-32 group-hover:translate-x-0"></span>
            <span
              class="relative flex w-full text-left text-black transition-colors duration-300 ease-in-out group-hover:text-white">
              Logout
              <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                stroke="currentColor" class="w-6 h-6 ml-3">
                <path stroke-linecap="round" stroke-linejoin="round"
                  d="M15.75 9V5.25A2.25 2.25 0 0013.5 3h-6a2.25 2.25 0 00-2.25 2.25v13.5A2.25 2.25 0 007.5 21h6a2.25 2.25 0 002.25-2.25V15m3 0l3-3m0 0l-3-3m3 3H9" />
              </svg>

            </span>
          </button>
        </div>

      </div>
    </section>
    <p class="absolute inset-x-0 bottom-0 py-2 font-bold text-center text-white bg-black/60">
      &copy;2023 Osthir ITC. All rights reserved.
    </p>
  </div>
</template>
<style scoped></style>
