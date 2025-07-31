<template>
  <div class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-40 z-30">
    <div class="bg-white shadow-xl p-6 rounded-xl w-full max-w-md mx-2">
      <h3 class="font-semibold text-lg mb-3">{{ mode === 'create' ? 'Create New Note' : 'Edit Note' }}</h3>
      <form @submit.prevent="save">
        <label class="block mb-1">Title</label>
        <input v-model="form.title" class="border rounded w-full p-2 mb-2" placeholder="Note title" required />

        <label class="block mb-1">Content</label>
        <textarea v-model="form.content" class="border rounded w-full p-2 mb-2" placeholder="Your note..." rows="5" required />

        <label class="block mb-1">Folder</label>
        <input v-model="form.folder" class="border rounded w-full p-2 mb-2" placeholder="Folder (optional)" list="folder-list" />
        <datalist id="folder-list">
          <option v-for="f in folders" :key="f" :value="f" />
        </datalist>

        <label class="block mb-1">Tags (comma separated)</label>
        <input v-model="tagsString" class="border rounded w-full p-2 mb-4" placeholder="tag1, tag2" />

        <div class="flex justify-end gap-2">
          <button class="px-4 py-1 rounded text-gray-600 hover:bg-gray-100" type="button" @click="$emit('close')">Cancel</button>
          <button class="px-4 py-1 rounded bg-[var(--primary)] text-white hover:bg-indigo-700" type="submit">{{ mode === 'create' ? 'Create' : 'Save' }}</button>
        </div>
      </form>
    </div>
  </div>
</template>
<script setup lang="ts">
import { ref, watch } from 'vue'
const props = defineProps({
  note: { type: Object, default: null },
  folders: { type: Array, default: () => [] },
  mode: { type: String, default: 'create' }
})
const emit = defineEmits(['close', 'save'])
const form = ref({ title: '', content: '', folder: '', tags: [] })
const tagsString = ref('')

watch(() => props.note, (n) => {
  if (n) {
    form.value = { ...n, tags: n.tags ?? [] }
    tagsString.value = (n.tags ?? []).join(', ')
  } else {
    form.value = { title: '', content: '', folder: '', tags: [] }
    tagsString.value = ''
  }
}, { immediate: true })

watch(tagsString, (val) => {
  form.value.tags = val.split(',').map(s => s.trim()).filter(Boolean)
})

function save() {
  emit('save', { ...form.value, tags: form.value.tags, folder: form.value.folder?.trim() || null })
}
</script>
