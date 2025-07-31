<template>
  <div>
    <div v-if="!notes.length" class="py-16 text-center text-gray-400">No notes found.</div>
    <div class="grid gap-4 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4">
      <div
        v-for="n in notes"
        :key="n.id"
        class="border rounded-lg p-4 bg-white cursor-pointer hover:border-[var(--primary)] transition flex flex-col gap-1 shadow-sm"
        :class="{ 'border-[var(--primary)]': selectedNote && selectedNote.id === n.id }"
        @click="$emit('select', n)"
        tabindex="0"
      >
        <div class="flex justify-between items-center">
          <strong class="truncate" :title="n.title">{{ n.title }}</strong>
          <span class="text-xs text-gray-400" v-if="n.updated_at">{{ n.updated_at.slice(0, 10) }}</span>
        </div>
        <div class="text-xs text-gray-500 truncate">{{ n.content?.replace(/\n/g, ' ').slice(0, 120) }}</div>
        <div class="flex gap-1 mt-2 flex-wrap">
          <span v-if="n.folder" class="text-xs bg-gray-100 px-2 py-0.5 rounded text-[var(--secondary)]">📁 {{ n.folder }}</span>
          <span v-for="tag in n.tags" :key="tag" class="text-xs bg-gray-100 px-2 py-0.5 rounded text-[var(--secondary)]">🏷️ {{ tag }}</span>
        </div>
        <div class="flex gap-2 mt-2 ml-auto">
          <button @click.stop="$emit('edit', n)" class="text-xs px-2 py-0.5 rounded bg-[var(--primary)] text-white">Edit</button>
          <button @click.stop="$emit('delete', n)" class="text-xs px-2 py-0.5 rounded bg-red-400 text-white">Delete</button>
        </div>
      </div>
    </div>
  </div>
</template>
<script setup lang="ts">
defineProps({
  notes: { type: Array, default: () => [] },
  selectedNote: { type: Object, default: null }
})
</script>
