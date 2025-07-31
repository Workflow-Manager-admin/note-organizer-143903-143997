---
theme: default
layout: cover
background: #f8fafc
colorScheme:
  primary: '#4F46E5'
  accent: '#F59E42'
  secondary: '#64748B'
---

# 📝 Minimal Notes App

<div class="text-md mt-4 text-secondary">
Welcome! Manage your notes efficiently.<br>
Sign up, organize, create, edit, and search your notes.
</div>

---

<!-- App Shell Layout -->
<template>
  <AppShell />
</template>

<script setup lang="ts">
import AppShell from './components/AppShell.vue'
</script>
