<template>
  <div class="min-h-screen flex flex-col bg-[#f8fafc] text-gray-800 font-sans">
    <!-- Top Navigation Bar -->
    <TopNav
      :is-auth="!!user"
      :user="user"
      @logout="handleLogout"
      @show-auth="showAuthModal = true"
      class="z-10"
      :primary="theme.primary"
    />

    <div class="flex flex-1">
      <!-- Sidebar for Folders/Tags -->
      <Sidebar
        :folders="folders"
        :tags="tags"
        :selected-folder="selectedFolder"
        :selected-tag="selectedTag"
        @select-folder="setFolder"
        @select-tag="setTag"
        :secondary="theme.secondary"
      />

      <!-- Main Content -->
      <main class="flex-1 p-3 md:p-8 transition-all duration-200" :style="centralBg">
        <!-- Notes List and Filters -->
        <div v-if="user">
          <div class="flex items-center mb-4 gap-2 flex-wrap">
            <input
              v-model="search"
              class="border rounded-md p-2 flex-1 max-w-xs"
              type="text"
              placeholder="🔍 Search notes..."
              @input="fetchNotes"
            />
            <button
              @click="openCreateModal"
              class="bg-[var(--accent)] hover:bg-orange-400 text-white px-3 py-2 rounded-md transition-all duration-100"
            >
              + New Note
            </button>
            <button v-if="selectedFolder || selectedTag" @click="clearFilters" class="ml-2 text-sm underline text-gray-500">Clear filters</button>
          </div>

          <NotesList
            :notes="filteredNotes"
            :selected-note="selectedNote"
            @select="setNote"
            @edit="openEditModal"
            @delete="deleteNote"
          />

          <!-- Show selected note in detail for desktop -->
          <div v-if="selectedNote" class="hidden md:block mt-10">
            <NoteDetail
              :note="selectedNote"
              @edit="openEditModal"
            />
          </div>
        </div>

        <!-- Auth Forms -->
        <div v-else>
          <div class="max-w-md mx-auto mt-12">
            <AuthForm @login="onLogin" @register="onRegister" :primary="theme.primary" :accent="theme.accent"/>
          </div>
        </div>
      </main>
    </div>

    <!-- Modals -->
    <NoteModal
      v-if="showNoteModal"
      :note="modalNote"
      :mode="modalMode"
      :folders="folders"
      @close="closeNoteModal"
      @save="handleNoteModalSave"
    />

    <AuthModal v-if="showAuthModal" @close="showAuthModal = false" @login="onLogin" @register="onRegister" :primary="theme.primary" :accent="theme.accent"/>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import TopNav from './TopNav.vue'
import Sidebar from './Sidebar.vue'
import NotesList from './NotesList.vue'
import NoteDetail from './NoteDetail.vue'
import AuthModal from './AuthModal.vue'
import AuthForm from './AuthForm.vue'
import NoteModal from './NoteModal.vue'

// THEME COLORS
const theme = { primary: '#4F46E5', accent: '#F59E42', secondary: '#64748B' }
const centralBg = { background: '#fff', borderRadius: '15px', minHeight: '80vh', boxShadow: '0 2px 8px #eee' }

// STATE
const user = ref(null)
const notes = ref([])
const filteredNotes = computed(() => {
  let n = notes.value
  if (search.value) n = n.filter(note => note.title.toLowerCase().includes(search.value.toLowerCase()))
  if (selectedFolder.value) n = n.filter(note => note.folder === selectedFolder.value)
  if (selectedTag.value) n = n.filter(note => note.tags && note.tags.includes(selectedTag.value))
  return n
})

const search = ref('')
const folders = ref([])
const tags = ref([])
const selectedFolder = ref(null)
const selectedTag = ref(null)
const selectedNote = ref(null)

const showNoteModal = ref(false)
const showAuthModal = ref(false)
const modalNote = ref(null)
const modalMode = ref('create') // or "edit"

// API BASE URL (can be replaced with env var)
const API_URL = 'http://localhost:3001/api'

function fetchMe() {
  const token = localStorage.getItem('token')
  if (token) {
    // Assume we have an endpoint /auth/me/ for simplicity in the backend (optionally skip, since our backend may not)
    fetch(`${API_URL}/auth/me/`, { headers: { Authorization: `Token ${token}` } })
      .then(r => { if (r.ok) return r.json(); throw new Error() })
      .then(data => { user.value = data }).catch(() => { user.value = null })
  } else {
    user.value = null
  }
}

function fetchNotes() {
  if (!user.value) return
  let url = `${API_URL}/notes/`
  const params = []
  if (selectedFolder.value) params.push(`folder=${selectedFolder.value}`)
  if (selectedTag.value) params.push(`tag=${selectedTag.value}`)
  if (search.value) params.push(`search=${encodeURIComponent(search.value)}`)
  if (params.length) url += '?' + params.join('&')
  fetch(url, { headers: { Authorization: `Token ${localStorage.getItem('token')}` } })
    .then(r => r.json()).then(data => {
      notes.value = Array.isArray(data) ? data : data.results || []
      // Infer folders and tags
      folders.value = [...new Set(notes.value.map(n => n.folder).filter(Boolean))]
      tags.value = [...new Set(notes.value.flatMap(n => n.tags || []))]
    })
}

function setFolder(folder) { selectedFolder.value = folder; fetchNotes(); }
function setTag(tag) { selectedTag.value = tag; fetchNotes(); }
function clearFilters() { selectedFolder.value = null; selectedTag.value = null; fetchNotes(); }

function setNote(note) { selectedNote.value = note }

function openCreateModal() { modalNote.value = null; modalMode.value = 'create'; showNoteModal.value = true }
function openEditModal(note) { modalNote.value = note; modalMode.value = 'edit'; showNoteModal.value = true }
function closeNoteModal() { showNoteModal.value = false }
function handleNoteModalSave(note) {
  if (modalMode.value === 'create') createNote(note)
  else if (modalMode.value === 'edit') updateNote(note)
  closeNoteModal()
}

function createNote(note) {
  fetch(`${API_URL}/notes/`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Token ${localStorage.getItem('token')}`
    },
    body: JSON.stringify(note)
  }).then(r => r.ok && fetchNotes())
}
function updateNote(note) {
  fetch(`${API_URL}/notes/${note.id}/`, {
    method: 'PUT',
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Token ${localStorage.getItem('token')}`
    },
    body: JSON.stringify(note)
  }).then(r => r.ok && fetchNotes())
}
function deleteNote(note) {
  if (!confirm('Delete this note?')) return
  fetch(`${API_URL}/notes/${note.id}/`, {
    method: 'DELETE',
    headers: { Authorization: `Token ${localStorage.getItem('token')}` },
  }).then(r => r.ok && fetchNotes())
}

// --- AUTH ---

function onLogin({ username, password }) {
  fetch(`${API_URL}/auth/login/`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ username, password })
  }).then(async r => {
    if (!r.ok) throw new Error('Login failed')
    const data = await r.json()
    localStorage.setItem('token', data.token)
    fetchMe(); fetchNotes()
  }).catch(() => alert('Login failed'))
}

function onRegister({ username, password }) {
  fetch(`${API_URL}/auth/register/`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ username, password })
  }).then(async r => {
    if (!r.ok) throw new Error('Register failed')
    const data = await r.json()
    alert('Registration successful. Please log in.')
    showAuthModal.value = true
  }).catch(() => alert('Registration failed'))
}

function handleLogout() {
  fetch(`${API_URL}/auth/logout/`, {
    method: 'POST',
    headers: {
      Authorization: `Token ${localStorage.getItem('token')}`
    }
  }).finally(() => {
    localStorage.removeItem('token')
    user.value = null
    notes.value = []
    selectedNote.value = null
  })
}

onMounted(() => {
  fetchMe()
  fetchNotes()
})

// Expose for children/components if needed
</script>

<style scoped>
:root {
  --primary: #4F46E5;
  --accent: #F59E42;
  --secondary: #64748B;
}
</style>
