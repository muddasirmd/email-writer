<template>
  <div class="min-h-screen p-6 bg-gray-50 flex items-start justify-center">
    <div class="w-full max-w-2xl bg-white rounded shadow p-6">
      <h1 class="text-2xl font-bold mb-4">AI Email Writer</h1>
      <EmailForm />
    </div>
  </div>
</template>

<script setup>
import EmailForm from './components/EmailForm.vue'
</script>
