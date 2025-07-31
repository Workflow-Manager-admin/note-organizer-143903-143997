<template>
  <div>
    <form v-if="formMode==='login'" @submit.prevent="login" class="space-y-4">
      <h2 class="font-bold text-lg mb-1" :style="{ color: primary }">Sign in</h2>
      <input v-model="username" autocomplete="username" class="p-2 border rounded w-full" type="text" placeholder="Username" required />
      <input v-model="password" autocomplete="current-password" class="p-2 border rounded w-full" type="password" placeholder="Password" required />
      <div class="flex justify-between items-center">
        <button :style="{ background: accent }" class="mt-1 px-4 py-1 rounded text-white" type="submit">Sign in</button>
        <button type="button" class="text-sm underline text-blue-500" @click="formMode='register'">Register instead</button>
      </div>
    </form>
    <form v-else @submit.prevent="register" class="space-y-4">
      <h2 class="font-bold text-lg mb-1" :style="{ color: primary }">Register</h2>
      <input v-model="username" autocomplete="username" class="p-2 border rounded w-full" type="text" placeholder="Username" required />
      <input v-model="password" autocomplete="new-password" class="p-2 border rounded w-full" type="password" placeholder="Password" required />
      <div class="flex justify-between items-center">
        <button :style="{ background: accent }" class="mt-1 px-4 py-1 rounded text-white" type="submit">Register</button>
        <button type="button" class="text-sm underline text-blue-500" @click="formMode='login'">Sign in instead</button>
      </div>
    </form>
  </div>
</template>
<script setup lang="ts">
import { ref } from 'vue'
const emit = defineEmits(['login', 'register'])
const props = defineProps({
  primary: { type: String, default: '#4F46E5' },
  accent: { type: String, default: '#F59E42' }
})
const username = ref('')
const password = ref('')
const formMode = ref('login')
function login() { emit('login', { username: username.value, password: password.value }) }
function register() { emit('register', { username: username.value, password: password.value }) }
</script>
