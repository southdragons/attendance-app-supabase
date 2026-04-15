<script setup>
import { ref, onMounted } from 'vue'
import { supabase } from '../lib/supabase'
import HistoryList from '../components/HistoryList.vue'

const userId = ref('')
const records = ref([])
const loading = ref(true)

function initUserId() {
  const saved = localStorage.getItem('userId')
  if (saved) userId.value = saved
}

async function fetchRecords() {
  loading.value = true
  try {
    const now = new Date()
    const today = `${now.getFullYear()}-${String(now.getMonth() + 1).padStart(2, '0')}-${String(now.getDate()).padStart(2, '0')}`

    const { data, error } = await supabase
      .from('attendance')
      .select('*')
      .eq('user_id', userId.value)
      .gte('date', today)
      .order('date', { ascending: false })
    if (error) throw error
    records.value = data || []
  } catch (e) {
    console.error(e)
  }
  loading.value = false
}

async function deleteRecord(id) {
  const { error } = await supabase
    .from('attendance')
    .delete()
    .eq('id', id)
  if (!error) fetchRecords()
}

onMounted(() => {
  initUserId()
  fetchRecords()
})
</script>

<template>
  <div class="max-w-md mx-auto px-3 pb-10 bg-base-200 min-h-screen">
    <div class="text-xl font-bold mb-4">
      📄 履歴
    </div>
    <div v-if="loading" class="text-center py-10">
      <span class="loading loading-spinner loading-lg text-primary"></span>
      <div class="mt-3 text-sm text-gray-500">読み込み中...</div>
    </div>
    <div v-else>
      <HistoryList
        :records="records"
        @delete="deleteRecord"
      />
    </div>
    <router-link to="/" class="btn btn-primary text-white w-full mt-6">
      ← 入力画面へ戻る
    </router-link>
  </div>
</template>