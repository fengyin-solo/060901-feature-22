<script setup lang="ts">
import type { Topic } from '@/types'
import { TOPIC_EMOJIS } from '@/types'
import { formatDate } from '@/utils/helpers'
import { computed } from 'vue'

const props = defineProps<{
  topic: Topic
  canDelete?: boolean
  isHost?: boolean
  roomStatus?: 'preparing' | 'playing' | 'ended'
  isContentHidden?: boolean
}>()

const emit = defineEmits<{
  (e: 'delete'): void
  (e: 'toggleVisibility'): void
}>()

const displayContent = computed(() => {
  if (props.isContentHidden && props.topic.hiddenBeforeStart) {
    return '🔒 开局后揭晓，保持惊喜～'
  }
  return props.topic.content
})

const isBlurred = computed(() => {
  return props.isContentHidden && props.topic.hiddenBeforeStart
})
</script>

<template>
  <div 
    class="topic-card bg-white rounded-xl p-4 shadow-md hover:shadow-lg transition-all border-l-4 relative overflow-hidden group"
    :style="{ borderLeftColor: topic.color }"
  >
    <div 
      class="absolute top-0 right-0 w-20 h-20 rounded-full -mr-8 -mt-8 opacity-10"
      :style="{ backgroundColor: topic.color }"
    ></div>
    
    <div class="relative z-10">
      <div class="flex justify-between items-start mb-2">
        <span 
          class="px-2 py-1 rounded-lg text-xs font-medium text-white"
          :style="{ backgroundColor: topic.color }"
        >
          {{ TOPIC_EMOJIS[topic.type] }} {{ topic.type }}
        </span>
        
        <div class="flex items-center gap-1">
          <span 
            v-if="topic.hiddenBeforeStart && roomStatus === 'preparing'"
            class="text-xs bg-amber-100 text-amber-700 px-2 py-0.5 rounded-full"
          >
            🎁 惊喜
          </span>
          
          <button 
            v-if="isHost && roomStatus === 'preparing'"
            class="opacity-0 group-hover:opacity-100 transition-opacity text-gray-400 hover:text-amber-500 p-1 text-sm"
            :title="topic.hiddenBeforeStart ? '取消隐藏' : '设为仅开局后可见'"
            @click.stop="emit('toggleVisibility')"
          >
            {{ topic.hiddenBeforeStart ? '👁️' : '🙈' }}
          </button>
          
          <button 
            v-if="canDelete"
            class="opacity-0 group-hover:opacity-100 transition-opacity text-gray-400 hover:text-red-500 p-1"
            @click.stop="emit('delete')"
          >
            ✕
          </button>
        </div>
      </div>
      
      <p 
        class="font-medium mb-3 leading-relaxed transition-all"
        :class="[
          isBlurred ? 'text-gray-400 italic select-none' : 'text-gray-800'
        ]"
      >
        {{ displayContent }}
      </p>
      
      <div class="flex justify-between items-center text-xs text-gray-500">
        <span>
          {{ topic.isAnonymous ? '🎭 匿名' : `👤 ${topic.author}` }}
        </span>
        <span>{{ formatDate(topic.createdAt) }}</span>
      </div>
      
      <div 
        v-if="topic.isFlipped"
        class="absolute inset-0 bg-green-500/10 flex items-center justify-center rounded-xl"
      >
        <span class="bg-green-500 text-white px-4 py-1 rounded-full text-sm font-medium transform rotate-12">
          ✓ 已聊过
        </span>
      </div>
    </div>
  </div>
</template>
