<template>
  <section v-if="note" :style="{ background: '#f9fafc', borderRadius: '8px', padding: '2rem', minHeight: '300px', boxShadow: '0 2px 8px #f1f5f9', margin: 'auto' }">
    <div class="flex items-center justify-between mb-2">
      <h2 class="text-xl font-semibold">{{ note.title }}</h2>
      <button class="text-xs px-2 py-0.5 bg-[var(--primary)] text-white rounded" @click="$emit('edit', note)">Edit</button>
    </div>
    <div class="text-sm text-gray-600 mb-2" v-if="note.updated_at">Last updated: {{ note.updated_at.slice(0, 10) }}</div>
    <pre class="whitespace-pre-wrap text-base text-gray-700">{{ note.content }}</pre>
    <div class="flex gap-2 mt-4">
      <span v-if="note.folder" class="text-xs bg-gray-100 px-2 py-0.5 rounded text-[var(--secondary)]">📁 {{ note.folder }}</span>
      <span
        v-for="tag in note.tags"
        :key="tag"
        class="text-xs bg-gray-100 px-2 py-0.5 rounded text-[var(--secondary)]"
      >🏷️ {{ tag }}</span>
    </div>
  </section>
</template>
<script setup lang="ts">
defineProps({
  note: { type: Object, default: null }
})
</script>
