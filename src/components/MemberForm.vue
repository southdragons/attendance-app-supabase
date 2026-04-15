<script setup>
defineProps(['members'])
const emit = defineEmits(['add','update','remove'])
</script>

<template>
  <div>
    <div
      v-for="(m, i) in members"
      :key="m.id"
      class="card bg-base-100 shadow-md border border-gray-200 mb-4"
    >
      <div class="card-body">

        <div class="font-bold text-lg mb-2 text-primary">
          {{ i + 1 }}人目
        </div>

        <div class="relative">
          <span class="absolute left-3 top-1/2 -translate-y-1/2 text-gray-400 text-lg">👤</span>
          <input
            v-model="m.name"
            placeholder="名前を入力してください"
            class="input w-full text-lg pl-10 border-2 border-gray-400 focus:border-primary bg-white rounded-lg"
          />
        </div>

        <div class="grid grid-cols-2 gap-2 mt-2">

          <button
            class="h-14 text-lg font-bold border-2 rounded-lg flex items-center justify-center active:scale-95 transition"
            :class="m.status === '欠席'
              ? 'bg-red-500 text-white border-red-500'
              : 'bg-white border-gray-400 text-gray-600 hover:border-red-300'"
            @click="emit('update', m.id, 'status', '欠席')"
          >
            ❌ 欠席
          </button>

          <button
            class="h-14 text-lg font-bold border-2 rounded-lg flex flex-col items-center justify-center active:scale-95 transition"
            :class="m.status === '10時以降参加'
              ? 'bg-yellow-400 text-white border-yellow-400'
              : 'bg-white border-gray-400 text-gray-600 hover:border-yellow-300'"
            @click="emit('update', m.id, 'status', '10時以降参加')"
          >
            ⏰ 10時以降参加
            <span class="text-xs font-normal">対象：小3以下</span>
          </button>

        </div>

        <button
          v-if="members.length > 1"
          class="btn btn-sm btn-outline btn-error mt-3"
          @click="emit('remove', m.id)"
        >
          削除
        </button>

      </div>
    </div>

    <button
      class="btn btn-outline w-full h-14 text-lg mt-2"
      @click="emit('add')"
    >
      ＋ 兄弟を追加
    </button>

  </div>
</template>