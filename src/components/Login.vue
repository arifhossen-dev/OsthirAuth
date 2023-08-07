<script setup>
import { ref, reactive, computed } from 'vue'

const isRegister = ref(false)
const confirmPass = ref()
const isAuth = ref(false)

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
  resetError()
  if (isRegister.value && data.username && data.password) {
    if (data.password === confirmPass.value) {
      localStorage.setItem('authInfo', JSON.stringify(data))
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
    resetError()
  }

  isRegister.value = false

  if (data.username && data.password) {
    let auth = JSON.parse(localStorage.getItem('authInfo'))
    if (data.username === auth.username && data.password === auth.password) {
      isAuth.value = true
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

function resetError() {
  errorMsg.username = ''
  errorMsg.password = ''
  errorMsg.confirmPass = ''
}

</script>

<template>
  <section v-show="!isAuth" class="flex w-full min-h-screen">
    <div class="w-1/2"
      style="background-image: url(https://images.unsplash.com/photo-1690555575753-7aa27df96b52?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=774&q=80); background-repeat: no-repeat; background-size: cover;">
      <h1 class="mt-10 mb-5 ml-10 text-2xl text-white">Osthir!</h1>
    </div>
    <div class="flex flex-col items-center justify-center w-1/2 bg-gray-200">
      {{ errorMsg }}
      <h2 class="mb-5 text-xl">Login or register</h2>
      <div class="w-full max-w-sm">
        <form class="px-8 pt-6 pb-8 mb-4 bg-white rounded shadow-md">
          <div class="px-5 py-2 mb-4 bg-red-100 border border-red-200 rounded-lg">
            ❌ Please provide valid credentials
          </div>
          {{ isRegister }}
          <div class="mb-4">
            <label class="block mb-2 text-sm font-bold" :class="errorMsg.username ? 'text-red-400' : 'text-gray-700'"
              for="username">
              Username
            </label>
            <input id="username"
              class="w-full px-3 py-2 leading-tight border rounded shadow appearance-none focus:outline-none focus:shadow-outline"
              :class="errorMsg.username ? 'border-red-400 text-red-500' : 'text-gray-700'" v-model="data.username"
              type="text" placeholder="Username">

            <p v-show="errorMsg.username" class="text-red-400">{{ errorMsg.username }}</p>
          </div>

          <div class="mb-4">
            <label class="block mb-2 text-sm font-bold" :class="errorMsg.password ? 'text-red-400' : 'text-gray-700'"
              for="password">
              Password
            </label>
            <input id="password"
              class="w-full px-3 py-2 leading-tight border rounded shadow appearance-none focus:outline-none focus:shadow-outline"
              :class="errorMsg.password ? 'border-red-400 text-red-500' : 'text-gray-700'" type="password"
              v-model="data.password" placeholder="******************">
            <p v-show="errorMsg.password" class="text-red-400">{{ errorMsg.password }}</p>
          </div>

          <div v-show="isRegister" class="mb-4">
            <label class="block mb-2 text-sm font-bold" :class="errorMsg.confirmPass ? 'text-red-400' : 'text-gray-700'"
              for="confirmPassword">
              Confirm Password
            </label>
            <input
              class="w-full px-3 py-2 mb-3 leading-tight border rounded shadow appearance-none focus:outline-none focus:shadow-outline"
              :class="errorMsg.confirmPass ? 'border-red-400 text-red-500' : 'text-gray-700'" id="confirmPassword"
              type="password" v-model="confirmPass" placeholder="******************">
            <p v-show="errorMsg.confirmPass" class="text-red-400">{{ errorMsg.confirmPass }}</p>
          </div>

          <div class="flex items-center justify-between mt-2">
            <button @click="signIn" class="px-4 py-2 font-bold focus:outline-none focus:shadow-outline"
              :class="!isRegister ? 'text-white bg-orange-600 rounded hover:bg-orange-700' : 'text-orange-600 align-baseline hover:text-orange-800'"
              type="button">
              Sign In
            </button>
            <a class="inline-block px-4 py-2 text-sm font-bold"
              :class="isRegister ? 'text-white bg-orange-600 rounded hover:bg-orange-700' : 'text-orange-600 align-baseline hover:text-orange-800'"
              @click="register" href="#">
              Or Register
            </a>
          </div>

        </form>
        <p class="text-xs text-center text-gray-500">
          &copy;2020 Acme Corp. All rights reserved.
        </p>
      </div>
    </div>
  </section>

  <section v-show="isAuth">
    <div class="flex items-center justify-center h-screen p-5 mx-auto rounded-lg max-w-7xl bg-slate-100">
      <h2 class="text-4xl font-bold">Welcome! <span class="text-green-600">{{ data.username }}</span></h2>
    </div>
  </section>
</template>
<style scoped></style>
