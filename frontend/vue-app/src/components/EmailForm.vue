<template>
  <form @submit.prevent="onSubmit" class="space-y-4">
    <div class="grid grid-cols-2 gap-4">
      <input v-model="recipient_name" placeholder="Recipient name" class="border p-2 rounded" />
      <select v-model="tone" class="border p-2 rounded">
        <option value="professional">Professional</option>
        <option value="friendly">Friendly</option>
        <option value="formal">Formal</option>
        <option value="casual">Casual</option>
        <option value="persuasive">Persuasive</option>
      </select>
    </div>

    <input v-model="purpose" placeholder="Purpose (subject / short)" class="w-full border p-2 rounded" />
    <textarea v-model="details" placeholder="Details / context" rows="4" class="w-full border p-2 rounded"></textarea>

    <div class="flex items-center gap-3">
      <button
        type="submit"
        :disabled="loading"
        class="px-4 py-2 bg-blue-600 text-white rounded disabled:bg-gray-400 disabled:cursor-not-allowed"
      >
        {{ loading ? 'Generating…' : 'Generate Email' }}
      </button>

      <button type="button" @click="clear" class="px-3 py-2 border rounded">Clear</button>
    </div>

    <div v-if="error" class="text-red-600">{{ error }}</div>

    <div v-if="email" class="mt-4 p-4 bg-gray-50 border rounded">
      <h2 class="font-semibold mb-2">Generated Email</h2>
      <pre class="whitespace-pre-wrap">{{ email }}</pre>
      <div class="mt-3 flex gap-2">
        <button @click="copy" class="px-3 py-1 border rounded">Copy</button>
        <button @click="download" class="px-3 py-1 border rounded">Download .txt</button>
      </div>
    </div>
  </form>
</template>

<script setup>
import { ref } from 'vue'

const recipient_name = ref('')
const purpose = ref('')
const details = ref('')
const tone = ref('professional')
const email = ref('')
const loading = ref(false)
const error = ref('')

async function onSubmit() {
  if (loading.value) return
  loading.value = true
  error.value = ''
  email.value = ''

  try {
    const res = await fetch('http://localhost:8000/api/email/generate/', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        recipient_name: recipient_name.value,
        purpose: purpose.value,
        details: details.value,
        tone: tone.value
      })
    })
    const data = await res.json()
    if (!res.ok) throw new Error(data.error || 'Server error')
    email.value = data.email
  } catch (err) {
    error.value = err.message
  } finally {
    loading.value = false
  }
}

function clear() {
  recipient_name.value = ''
  purpose.value = ''
  details.value = ''
  email.value = ''
}

function copy() {
  navigator.clipboard.writeText(email.value)
}

function download() {
  const blob = new Blob([email.value], { type: 'text/plain' })
  const url = URL.createObjectURL(blob)
  const a = document.createElement('a')
  a.href = url
  a.download = 'email.txt'
  a.click()
  URL.revokeObjectURL(url)
}
</script>

<style scoped>
/* keep it small — Tailwind recommended in real project */
</style>
