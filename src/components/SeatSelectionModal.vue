<script setup lang="ts">
import { ref, computed } from 'vue'
import SeatMap from './SeatMap.vue'
import type { Seat } from '../types/booking'

interface Props {
  visible: boolean
  seats: Seat[]
  selectedSeat: string | null
}

interface Emits {
  (e: 'update:visible', value: boolean): void
  (e: 'select-seat', seatId: string): void
  (e: 'confirm'): void
  (e: 'find-partner'): void
}

const props = defineProps<Props>()
const emit = defineEmits<Emits>()

// 选择座位处理
const handleSeatSelect = (seatId: string) => {
  emit('select-seat', seatId)
}

// 确认选择
const confirm = () => {
  if (props.selectedSeat) {
    emit('confirm')
  }
}

// 关闭模态框
const close = () => {
  emit('update:visible', false)
}

// 打开查找伙伴功能
const openFindPartner = () => {
  emit('find-partner')
}

// 是否可以确认（必须选中座位）
const canConfirm = computed(() => !!props.selectedSeat)
</script>

<template>
  <Teleport to="body">
    <Transition name="modal">
      <div
        v-if="visible"
        class="fixed inset-0 z-50 flex items-center justify-center bg-gradient-to-b from-black/60 to-gray-600/60 backdrop-blur-[12.5px] p-4"
        @click.self="close"
      >
        <!-- 座位选择卡片 -->
        <div class="w-full max-w-[340px] relative animate-scale-in">
          <!-- 图例 -->
          <div class="flex items-center gap-8 mb-6">
            <div class="flex items-center gap-2">
              <div class="w-5 h-5 rounded bg-gray-light"></div>
              <span class="text-sm font-medium text-white">Occupied</span>
            </div>
            <div class="flex items-center gap-2">
              <div class="w-5 h-5 rounded bg-success"></div>
              <span class="text-sm font-medium text-white">Available</span>
            </div>
          </div>

          <!-- 关闭按钮 -->
          <button
            @click="close"
            class="absolute -top-2 -right-2 w-12 h-12 rounded-full bg-gray-dark flex items-center justify-center hover:opacity-90 transition-opacity z-10"
          >
            <svg
              width="24"
              height="24"
              viewBox="0 0 24 24"
              fill="none"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                d="M6 6L18 18M18 6L6 18"
                stroke="white"
                stroke-width="2"
                stroke-linecap="round"
              />
            </svg>
          </button>

          <!-- 座位地图卡片 -->
          <div class="bg-white rounded-3xl p-6">
            <!-- 座位地图 -->
            <SeatMap :seats="seats" :selected-seat="selectedSeat" @select-seat="handleSeatSelect" />
          </div>

          <!-- 当前选择和操作 -->
          <div v-if="selectedSeat" class="mt-6 text-center">
            <div class="text-sm font-medium text-white mb-2 tracking-tight">Your Seat</div>
            <div class="text-5xl font-semibold text-white mb-6 tracking-tight">{{ selectedSeat }}</div>

            <!-- 操作按钮 -->
            <div class="flex gap-3">
              <!-- 查找伙伴按钮 -->
              <button
                @click="openFindPartner"
                class="w-14 h-14 rounded-xl bg-gray-dark flex items-center justify-center hover:opacity-90 transition-opacity"
              >
                <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                  <path d="M15.7322 16.4564C14.5895 16.794 13.239 16.9498 11.6547 16.9498H3.86333C2.27896 16.9498 0.928542 16.794 -0.214167 16.4564C0.0715417 13.0801 3.53854 10.418 7.759 10.418C11.9794 10.418 15.4465 13.0801 15.7322 16.4564Z" stroke="white" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" transform="translate(4, 2)"/>
                  <path d="M12.4079 2.12012C12.4079 4.69137 10.3302 6.78195 7.759 6.78195C5.18775 6.78195 3.11008 4.69137 3.11008 2.12012C3.11008 -0.451125 5.18775 -2.54879 7.759 -2.54879C10.3302 -2.54879 12.4079 -0.451125 12.4079 2.12012Z" stroke="white" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" transform="translate(4, 6)"/>
                  <circle cx="18" cy="8" r="2" stroke="white" stroke-width="1.5"/>
                  <line x1="16.5" y1="9.5" x2="14" y2="12" stroke="white" stroke-width="1.5" stroke-linecap="round"/>
                </svg>
              </button>

              <!-- 确认按钮 -->
              <button
                @click="confirm"
                class="flex-1 h-14 rounded-xl bg-success text-white text-xl font-normal leading-[100%] tracking-[-1px] hover:opacity-90 transition-opacity"
              >
                Confirm
              </button>
            </div>
          </div>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<style scoped>
/* 模态框动画 */
.modal-enter-active,
.modal-leave-active {
  transition: opacity 0.3s ease;
}

.modal-enter-from,
.modal-leave-to {
  opacity: 0;
}

.animate-scale-in {
  animation: scale-in 0.3s ease-out;
}

@keyframes scale-in {
  from {
    transform: scale(0.9);
    opacity: 0;
  }
  to {
    transform: scale(1);
    opacity: 1;
  }
}
</style>
