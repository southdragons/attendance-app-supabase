<script setup>
import { ref } from 'vue'

const props = defineProps(['date'])
const emit = defineEmits(['change'])

function update(e) {
  emit('change', new Date(e.target.value))
}

function formatDate(d) {
  const y = d.getFullYear()
  const m = String(d.getMonth() + 1).padStart(2, '0')
  const day = String(d.getDate()).padStart(2, '0')
  return `${y}-${m}-${day}`
}
</script>

<template>
  <div class="card">
    <input
      type="date"
      :value="formatDate(props.date)"
      @input="update"
      class="input"
    />
  </div>
</template>

<style scoped>
.card { margin-bottom: 25px; }
.input {
  width: 100%;
  height: 25%;
  font-size: 25px;
}
</style>