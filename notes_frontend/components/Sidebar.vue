<template>
  <aside class="hidden md:flex flex-col w-48 min-w-32 pt-6 pl-4 pr-2 bg-[#f8fafc] border-r border-gray-200">
    <h3 class="text-xs text-gray-400 mb-2 px-2">Folders</h3>
    <ul class="mb-6">
      <li
        v-for="f in folders"
        :key="f"
        class="cursor-pointer px-2 py-1 rounded hover:bg-[var(--secondary)] hover:text-white"
        :class="{ 'bg-[var(--secondary)] text-white font-semibold': selectedFolder === f }"
        @click="$emit('select-folder', f)"
      >📁 {{ f }}</li>
      <li v-if="!folders.length" class="px-2 py-1 text-gray-400">No folders</li>
    </ul>
    <h3 class="text-xs text-gray-400 mb-2 px-2">Tags</h3>
    <ul>
      <li
        v-for="t in tags"
        :key="t"
        class="cursor-pointer px-2 py-1 rounded hover:bg-[var(--secondary)] hover:text-white"
        :class="{ 'bg-[var(--secondary)] text-white font-semibold': selectedTag === t }"
        @click="$emit('select-tag', t)"
      >🏷️ {{ t }}</li>
      <li v-if="!tags.length" class="px-2 py-1 text-gray-400">No tags</li>
    </ul>
  </aside>
</template>
<script setup lang="ts">
defineProps({
  folders: { type: Array, default: () => [] },
  tags: { type: Array, default: () => [] },
  selectedFolder: { type: String, default: null },
  selectedTag: { type: String, default: null },
  secondary: { type: String, default: '#64748B' }
})
</script>
