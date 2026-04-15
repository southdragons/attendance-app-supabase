<script setup>
import { ref, onMounted, watch } from 'vue'
import { supabase } from '../lib/supabase'
import DatePicker from '../components/DatePicker.vue'
import MemberForm from '../components/MemberForm.vue'
import SubmitBar from '../components/SubmitBar.vue'

const userId = ref('')
const members = ref([{ id: Date.now(), name: '', status: null }])
const selectedDate = ref((() => {
  const now = new Date()
  now.setHours(12, 0, 0, 0)
  return now
})())
const loading = ref(false)
const toast = ref('')

function initUserId() {
  const saved = localStorage.getItem('userId')
  if (saved) userId.value = saved
  else {
    const id = 'user-' + Math.random().toString(36).substring(2, 10)
    localStorage.setItem('userId', id)
    userId.value = id
  }
}

function addMember() {
  members.value.push({ id: Date.now(), name: '', status: null })
}

function updateMember(id, key, val) {
  members.value = members.value.map(m =>
    m.id === id ? { ...m, [key]: val } : m
  )
}

function removeMember(id) {
  if (members.value.length === 1) return
  members.value = members.value.filter(m => m.id !== id)
}

function formatDate(d) {
  const y = d.getFullYear()
  const m = String(d.getMonth() + 1).padStart(2, '0')
  const day = String(d.getDate()).padStart(2, '0')
  return `${y}-${m}-${day}`
}

async function submit() {
  const valid = members.value.filter(m => m.name && m.status)
  if (!valid.length) {
    toast.value = '入力してください'
    return
  }
  loading.value = true
  try {
    const rows = valid.map(m => ({
      user_id: userId.value,
      name: m.name,
      brother: m.brother || null,
      date: formatDate(selectedDate.value),
      status: m.status
    }))
    const { error } = await supabase.from('attendance').insert(rows)
    if (error) throw error
    toast.value = '送信完了'
    members.value = members.value.map(m => ({
      id: Date.now(),
      name: m.name,
      status: null
    }))
  } catch (e) {
    console.error(e)
    toast.value = 'エラーが発生しました'
  } finally {
    loading.value = false
  }
}

function loadMembers() {
  const saved = localStorage.getItem('members')
  if (saved) members.value = JSON.parse(saved)
}

onMounted(() => {
  initUserId()
  loadMembers()
})

watch(members, (val) => {
  localStorage.setItem('members', JSON.stringify(val))
}, { deep: true })
</script>

<template>
  <div class="max-w-md mx-auto px-3 pb-28">
    <div class="text-xl font-bold mb-3 flex items-center gap-2">
      ⚾ 出欠連絡
    </div>
    <DatePicker
      :date="selectedDate"
      @change="d => selectedDate = d"
    />
    <MemberForm
      :members="members"
      @add="addMember"
      @update="updateMember"
      @remove="removeMember"
    />
    <router-link
      to="/history"
      class="btn btn-outline w-full mt-4"
    >
      📄 履歴を見る
    </router-link>
    <SubmitBar
      :loading="loading"
      @submit="submit"
    />
    <div v-if="toast" class="toast toast-top toast-center z-50">
      <div class="alert alert-success">
        <span>{{ toast }}</span>
      </div>
    </div>
  </div>
</template>