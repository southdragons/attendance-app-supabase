<script setup>
defineProps(['loading'])  // ★これ追加
const emit = defineEmits(['submit'])
</script>

<template>
  <div class="fixed bottom-0 left-0 w-full p-3 bg-base-200 shadow-2xl">

    <button
      class="btn btn-primary w-full h-16 text-lg font-bold"
      :disabled="loading"
      @click="emit('submit')"
    >
      <span v-if="loading" class="loading loading-spinner"></span>
      <span v-else>送信する</span>
    </button>

  </div>
</template>