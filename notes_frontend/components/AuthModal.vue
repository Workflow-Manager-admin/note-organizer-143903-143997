<template>
  <div class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-40 z-40">
    <div class="bg-white shadow-lg rounded-xl p-6 w-full max-w-md mx-2">
      <button class="absolute right-2 top-2 text-gray-500" @click="$emit('close')" title="Close">&times;</button>
      <AuthForm @login="$emit('login', $event)" @register="$emit('register', $event)" :primary="primary" :accent="accent"/>
    </div>
  </div>
</template>
<script setup lang="ts">
import AuthForm from './AuthForm.vue'
defineProps({
  primary: { type: String, default: '#4F46E5' },
  accent: { type: String, default: '#F59E42' }
})
</script>
