<template>
  <nav :style="{ background: primary, color: 'white' }" class="flex items-center px-6 py-3 justify-between shadow-sm">
    <div class="flex items-center gap-3">
      <span class="text-2xl font-bold tracking-tight">Minimal Notes</span>
      <span class="bg-white px-2 py-0.5 ml-2 rounded text-xs font-semibold text-[var(--primary)]" style="color: #4F46E5;">beta</span>
    </div>
    <div class="flex items-center gap-4">
      <span v-if="isAuth && user" class="text-white font-semibold">{{ user.username }}</span>
      <button
        v-if="isAuth"
        @click="$emit('logout')"
        class="ml-2 bg-white hover:bg-gray-200 text-[var(--primary)] px-3 py-1 rounded transition-all"
      >Log out</button>
      <button
        v-else
        @click="$emit('show-auth')"
        class="ml-2 bg-[var(--accent)] hover:bg-orange-500 text-white px-3 py-1 rounded transition-all"
      >Sign in / Register</button>
    </div>
  </nav>
</template>
<script setup lang="ts">
defineProps({
  isAuth: Boolean,
  user: { type: Object, default: null },
  primary: { type: String, default: '#4F46E5' }
})
</script>
