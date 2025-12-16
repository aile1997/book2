<script setup lang="ts">
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'
import { useSeats } from '../composables/useSeats'
import SeatMap from '../components/SeatMap.vue'
import FindPartnerModal from '../components/FindPartnerModal.vue'
import SeatSelectionModal from '../components/SeatSelectionModal.vue'
import SuccessModal from '../components/SuccessModal.vue'
import type { TimeSlot } from '../types/booking'

const router = useRouter()

// ========== 数据层 ==========

// 使用座位管理组合式函数
const { seats, selectedSeat, selectSeat, clearSelection } = useSeats()

// 邀请的伙伴列表
const invitedPartners = ref<string[]>([])

// Coins 消耗
const coinCost = ref(10)

// 模态框状态
const showSeatModal = ref(false)
const showFindPartnerModal = ref(false)
const showSuccessModal = ref(false)

// 时间段数据
const timeSlots = ref<TimeSlot[]>([
  {
    id: '1',
    date: '11.20',
    weekday: 'Wed.',
    times: [
      { id: '1-1', time: '09:00 - 12:00', selected: true },
      { id: '1-2', time: '12:00 - 18:00', selected: false },
    ],
  },
  {
    id: '2',
    date: '11.21',
    weekday: 'Thu.',
    times: [
      { id: '2-1', time: '09:00 - 12:00', selected: false },
      { id: '2-2', time: '12:00 - 18:00', selected: false },
    ],
  },
])

// ========== 计算属性 ==========

// 当前选中的日期和时间
const selectedDateTime = computed(() => {
  for (const slot of timeSlots.value) {
    const selectedTime = slot.times.find((t) => t.selected)
    if (selectedTime) {
      return {
        date: slot.date,
        weekday: slot.weekday,
        time: selectedTime.time,
      }
    }
  }
  return null
})

// ========== 事件处理层 ==========

// 切换时间段选择
const toggleTimeSlot = (dateIndex: number, timeIndex: number) => {
  timeSlots.value.forEach((slot) => {
    slot.times.forEach((time) => {
      time.selected = false
    })
  })
  timeSlots.value[dateIndex].times[timeIndex].selected = true
}

// 打开座位选择模态框
const openSeatModal = () => {
  showSeatModal.value = true
}

// 选择座位（从模态框）
const handleSeatSelect = (seatId: string) => {
  selectSeat(seatId)
}

// 确认座位选择
const confirmSeatSelection = () => {
  showSeatModal.value = false
}

// 重新选择座位
const reselectSeat = () => {
  clearSelection()
  openSeatModal()
}

// 移除邀请伙伴
const removePartner = (partner: string) => {
  invitedPartners.value = invitedPartners.value.filter((p) => p !== partner)
}

// 打开查找伙伴模态框
const openFindPartnerModal = () => {
  showFindPartnerModal.value = true
}

// 从座位选择打开查找伙伴
const openFindPartnerFromSeat = () => {
  showSeatModal.value = false
  showFindPartnerModal.value = true
}

// 确认邀请伙伴
const confirmPartnerInvite = () => {
  showFindPartnerModal.value = false
}

// 预定座位
const bookNow = () => {
  if (!selectedSeat.value) {
    alert('请先选择座位')
    return
  }
  if (!selectedDateTime.value) {
    alert('请选择日期和时间')
    return
  }
  showSuccessModal.value = true
}

// 返回首页
const goBack = () => {
  router.back()
}

// 从成功页返回首页
const backToHome = () => {
  showSuccessModal.value = false
  router.push('/')
}
</script>

<template>
  <div class="relative min-h-screen bg-white overflow-hidden">
    <!-- ========== 背景图层 ========== -->
    <div class="absolute inset-0 w-full h-full">
      <!-- Figma 背景图 (旋转的办公室照片) -->
      <div class="absolute inset-0">
        <img
          src="https://api.builder.io/api/v1/image/assets/TEMP/82bec9dbdda63618707f633af0c7c4829ba41636?width=1624"
          alt=""
          class="w-full h-full object-cover"
          style="transform: rotate(-90deg) scale(1.5); transform-origin: center;"
        />
      </div>
      
      <!-- 渐变遮罩 -->
      <div class="absolute inset-0 bg-gradient-to-b from-transparent via-white/40 to-white"></div>
    </div>

    <!-- ========== 内容层 ========== -->
    <div class="relative z-10 min-h-screen flex flex-col">
      <!-- ========== 顶部导航栏 ========== -->
      <div class="sticky top-0 z-30 bg-white/90 backdrop-blur-sm border-b border-gray-light">
        <div class="flex items-center justify-between px-8 py-5">
          <button
            @click="goBack"
            class="flex items-center justify-center w-10 h-10 rounded-full hover:bg-gray-light transition-colors"
          >
            <svg
              width="24"
              height="24"
              viewBox="0 0 24 24"
              fill="none"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                d="M15 19.9201L8.47997 13.4001C7.70997 12.6301 7.70997 11.3701 8.47997 10.6001L15 4.08008"
                stroke="#292D32"
                stroke-width="1.5"
                stroke-miterlimit="10"
                stroke-linecap="round"
                stroke-linejoin="round"
              />
            </svg>
          </button>
          <h1 class="text-xl font-normal text-black tracking-tight">Booking Seats</h1>
          <button
            class="flex items-center justify-center w-14 h-14 rounded-full bg-gray-dark hover:opacity-90 transition-opacity"
          >
            <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
              <path d="M15.7322 16.4564C14.5895 16.794 13.239 16.9498 11.6547 16.9498H3.86333C2.27896 16.9498 0.928542 16.794 -0.214167 16.4564C0.0715417 13.0801 3.53854 10.418 7.759 10.418C11.9794 10.418 15.4465 13.0801 15.7322 16.4564Z\" stroke=\"white\" stroke-width=\"1.5\" stroke-linecap=\"round\" stroke-linejoin=\"round\" transform=\"translate(4, 2)\"/>
              <path d=\"M12.4079 2.12012C12.4079 4.69137 10.3302 6.78195 7.759 6.78195C5.18775 6.78195 3.11008 4.69137 3.11008 2.12012C3.11008 -0.451125 5.18775 -2.54879 7.759 -2.54879C10.3302 -2.54879 12.4079 -0.451125 12.4079 2.12012Z\" stroke=\"white\" stroke-width=\"1.5\" stroke-linecap=\"round\" stroke-linejoin=\"round\" transform=\"translate(4, 6)\"/>
            </svg>
          </button>
        </div>
      </div>

      <!-- ========== 主要内容区域 ========== -->
      <div class="flex-1 px-8 py-6 pb-32 max-w-2xl mx-auto w-full">
        <!-- ========== 座位信息 ========== -->
        <section class="mb-6">
          <h2 class="text-sm font-normal text-black mb-3 tracking-tight">Your Seat</h2>

          <div class="flex items-end justify-between">
            <div v-if="selectedSeat" class="text-4xl font-semibold text-black tracking-tight">
              {{ selectedSeat }}
            </div>
            <div v-else class="text-2xl font-normal text-gray tracking-tight">
              No seat selected
            </div>
            
            <button
              v-if="selectedSeat"
              @click="reselectSeat"
              class="px-6 py-3 border border-gray-light rounded-lg text-sm font-normal text-gray-dark hover:border-gray-dark transition-colors"
            >
              Re-select
            </button>
            <button
              v-else
              @click="openSeatModal"
              class="px-6 py-3 bg-gray-dark text-white rounded-lg text-sm font-normal hover:opacity-90 transition-opacity"
            >
              Select Seat
            </button>
          </div>

          <!-- 座位地图缩略图 -->
          <div v-if="selectedSeat" class="mt-6">
            <img
              src="https://api.builder.io/api/v1/image/assets/TEMP/7a402a9c3874128b62ffc1a94682466ff5104518?width=436"
              alt="Seat Map"
              class="w-full max-w-[218px] h-auto rounded-2xl shadow-card"
            />
          </div>
        </section>

        <!-- ========== 分隔线 ========== -->
        <div class="border-t border-gray-light my-6"></div>

        <!-- ========== 日期和时间选择 ========== -->
        <section class="mb-6">
          <h2 class="text-sm font-normal text-black mb-3 tracking-tight">Date & Time</h2>

          <div class="space-y-6">
            <div v-for="(slot, dateIndex) in timeSlots" :key="slot.id" class="flex gap-6 items-start">
              <!-- 日期显示 -->
              <div class="w-16 flex-shrink-0">
                <div class="text-2xl font-semibold text-gray-dark tracking-tight">{{ slot.date }}</div>
                <div class="text-xs text-gray mt-1 tracking-tight">{{ slot.weekday }}</div>
              </div>

              <!-- 时间段选择 -->
              <div class="flex-1 space-y-3">
                <button
                  v-for="(time, timeIndex) in slot.times"
                  :key="time.id"
                  @click="toggleTimeSlot(dateIndex, timeIndex)"
                  class="w-full px-4 py-3.5 rounded-xl text-sm font-normal transition-all tracking-tight"
                  :class="[
                    time.selected
                      ? 'bg-gray-dark text-white shadow-md'
                      : 'border border-gray-light text-gray-dark hover:border-gray-dark',
                  ]"
                >
                  {{ time.time }}
                </button>
              </div>
            </div>
          </div>
        </section>

        <!-- ========== 分隔线 ========== -->
        <div class="border-t border-gray-light my-6"></div>

        <!-- ========== 邀请伙伴 ========== -->
        <section class="mb-6">
          <h2 class="text-sm font-normal text-black mb-3 tracking-tight">Invite Co-worker</h2>

          <div class="flex flex-wrap gap-2">
            <!-- 已邀请的伙伴标签 -->
            <button
              v-for="partner in invitedPartners"
              :key="partner"
              class="inline-flex items-center gap-2 px-3 py-2 bg-primary-light rounded-full border border-primary-light hover:bg-primary/10 transition-colors"
            >
              <span class="text-sm font-normal text-primary-dark">{{ partner }}</span>
            </button>

            <!-- 添加伙伴按钮 -->
            <button
              @click="openFindPartnerModal"
              class="inline-flex items-center gap-2 px-4 py-2 border border-dashed border-gray-light rounded-full hover:border-gray-dark transition-colors"
            >
              <svg
                width="16"
                height="16"
                viewBox="0 0 16 16"
                fill="none"
                xmlns="http://www.w3.org/2000/svg"
              >
                <path
                  d="M8 3.5V12.5M3.5 8H12.5"
                  stroke="#B9B9B9"
                  stroke-width="1.5"
                  stroke-linecap="round"
                />
              </svg>
              <span class="text-sm font-normal text-gray">Add</span>
            </button>
          </div>
        </section>
      </div>

      <!-- ========== 底部固定操作栏 ========== -->
      <div class="fixed bottom-0 left-0 right-0 bg-white border-t border-gray-light px-8 py-4 z-20 shadow-lg">
        <div class="flex items-center justify-between max-w-2xl mx-auto">
          <button
            @click="bookNow"
            :disabled="!selectedSeat"
            class="w-full max-w-xs h-14 bg-gray-dark text-white text-xl font-normal rounded-lg hover:opacity-90 transition-all disabled:opacity-50 disabled:cursor-not-allowed shadow-lg tracking-tight mx-auto"
            :class="selectedSeat ? 'border-2 border-primary-light' : ''"
          >
            Book Now
          </button>
        </div>
      </div>
    </div>

    <!-- ========== 模态框组件 ========== -->

    <!-- 座位选择模态框 -->
    <SeatSelectionModal
      v-model:visible="showSeatModal"
      :seats="seats"
      :selected-seat="selectedSeat"
      @select-seat="handleSeatSelect"
      @confirm="confirmSeatSelection"
      @find-partner="openFindPartnerFromSeat"
    />

    <!-- 查找伙伴模态框 -->
    <FindPartnerModal
      v-model:visible="showFindPartnerModal"
      v-model:selected-partners="invitedPartners"
      @confirm="confirmPartnerInvite"
    />

    <!-- 成功模态框 -->
    <SuccessModal v-model:visible="showSuccessModal" @back="backToHome" />
  </div>
</template>

<style scoped>
::-webkit-scrollbar {
  width: 6px;
}

::-webkit-scrollbar-track {
  background: #f1f1f1;
  border-radius: 10px;
}

::-webkit-scrollbar-thumb {
  background: #cccccc;
  border-radius: 10px;
}

::-webkit-scrollbar-thumb:hover {
  background: #b9b9b9;
}
</style>