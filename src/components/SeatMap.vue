<script setup lang="ts">
import { ref } from 'vue'
import type { Seat } from '../types/booking'

interface Props {
  seats: Seat[]
  selectedSeat: string | null
  showTooltip?: boolean
}

interface Emits {
  (e: 'select-seat', seatId: string): void
}

const props = defineProps<Props>()
const emit = defineEmits<Emits>()

const hoveredSeat = ref<string | null>(null)

const handleSeatClick = (seat: Seat) => {
  if (seat.status === 'available' || seat.status === 'selected') {
    emit('select-seat', seat.id)
  }
}

const getSeatColor = (seat: Seat): string => {
  if (seat.status === 'selected') return '#A78BFA'
  if (seat.status === 'available') return '#38D87B'
  return '#CCCCCC'
}

const getSeatsByTable = (table: 'A' | 'B' | 'C', position: 'left' | 'right') => {
  return props.seats.filter((s) => s.table === table && s.position === position)
}

const getSeatY = (index: number): number => {
  return index * 24
}

const isSeatSelected = (seatId: string): boolean => {
  return props.selectedSeat === seatId
}
</script>

<template>
  <div class="relative w-full max-w-[280px] mx-auto">
    <!-- 使用Figma背景图 -->
    <div class="relative bg-white rounded-3xl overflow-hidden shadow-lg">
      <!-- 背景图片 -->
      <div class="absolute inset-0 opacity-5">
        <img
          src="https://api.builder.io/api/v1/image/assets/TEMP/7a402a9c3874128b62ffc1a94682466ff5104518?width=436"
          alt=""
          class="w-full h-full object-cover"
        />
      </div>

      <!-- 内容区域 -->
      <div class="relative p-6">
        <!-- 标签区域 -->
        <div class="absolute top-6 left-6 text-gray text-xs font-medium">
          <div class="flex items-center gap-2">
            <svg width="16" height="16" viewBox="0 0 16 16" fill="none">
              <path
                d="M8 2L8 14M8 2L4 6M8 2L12 6"
                stroke="#CCCCCC"
                stroke-width="1.5"
                stroke-linecap="round"
                stroke-linejoin="round"
              />
            </svg>
            <span class="text-gray-lighter">Entrance</span>
          </div>
        </div>

        <div class="absolute top-8 right-6 text-gray text-xs font-medium space-y-6">
          <div class="flex items-center gap-2">
            <svg width="16" height="12" viewBox="0 0 16 12" fill="none">
              <rect x="2" y="2" width="12" height="8" fill="#CCCCCC" opacity="0.3" rx="2" />
            </svg>
            <span class="text-gray-lighter">Bar</span>
          </div>
          <div class="flex items-center gap-2">
            <svg width="20" height="16" viewBox="0 0 20 16" fill="none">
              <rect x="2" y="2" width="16" height="12" fill="#CCCCCC" opacity="0.3" rx="2" />
            </svg>
            <span class="text-gray-lighter">Lounge</span>
          </div>
        </div>

        <div class="absolute bottom-28 left-6 flex items-center gap-2 text-gray text-xs font-medium">
          <svg width="16" height="16" viewBox="0 0 16 16" fill="none">
            <rect x="2" y="2" width="12" height="12" fill="#CCCCCC" opacity="0.3" rx="2" />
          </svg>
          <span class="text-gray-lighter">Fitness</span>
        </div>

        <!-- SVG 座位地图 -->
        <svg viewBox="0 0 268 340" class="w-full h-auto" xmlns="http://www.w3.org/2000/svg">
          <!-- 区域 A -->
          <g id="area-a">
            <rect x="30" y="80" width="30" height="138" fill="#EAEAEA" rx="4" />
            <text x="45" y="155" text-anchor="middle" font-size="24" font-weight="500" fill="#CCCCCC">
              A
            </text>

            <!-- 左侧座位 -->
            <g v-for="seat in getSeatsByTable('A', 'left')" :key="seat.id">
              <rect
                :x="5"
                :y="80 + getSeatY(seat.index) - 8"
                width="18"
                height="18"
                rx="9"
                :fill="getSeatColor(seat)"
                :class="[
                  'transition-all duration-200',
                  seat.status === 'available' || seat.status === 'selected'
                    ? 'cursor-pointer hover:opacity-80'
                    : 'cursor-not-allowed',
                ]"
                @click="handleSeatClick(seat)"
                @mouseenter="hoveredSeat = seat.id"
                @mouseleave="hoveredSeat = null"
              />
              <g v-if="isSeatSelected(seat.id)">
                <circle :cx="14" :cy="80 + getSeatY(seat.index)" r="7" fill="white" />
                <path
                  :d="`M 11 ${80 + getSeatY(seat.index)} L 13 ${82 + getSeatY(seat.index)} L 17 ${78 + getSeatY(seat.index)}`"
                  stroke="#A78BFA"
                  stroke-width="2"
                  fill="none"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                />
              </g>
            </g>

            <!-- 右侧座位 -->
            <g v-for="seat in getSeatsByTable('A', 'right')" :key="seat.id">
              <rect
                :x="62"
                :y="80 + getSeatY(seat.index) - 8"
                width="18"
                height="18"
                rx="9"
                :fill="getSeatColor(seat)"
                :class="[
                  'transition-all duration-200',
                  seat.status === 'available' || seat.status === 'selected'
                    ? 'cursor-pointer hover:opacity-80'
                    : 'cursor-not-allowed',
                ]"
                @click="handleSeatClick(seat)"
                @mouseenter="hoveredSeat = seat.id"
                @mouseleave="hoveredSeat = null"
              />
              <g v-if="isSeatSelected(seat.id)">
                <circle :cx="71" :cy="80 + getSeatY(seat.index)" r="7" fill="white" />
                <path
                  :d="`M 68 ${80 + getSeatY(seat.index)} L 70 ${82 + getSeatY(seat.index)} L 74 ${78 + getSeatY(seat.index)}`"
                  stroke="#A78BFA"
                  stroke-width="2"
                  fill="none"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                />
              </g>
            </g>
          </g>

          <!-- 分隔线 -->
          <line x1="100" y1="70" x2="100" y2="260" stroke="#EAEAEA" stroke-width="2" />

          <!-- 区域 B (上半部分) -->
          <g id="area-b">
            <rect x="120" y="80" width="30" height="68" fill="#EAEAEA" rx="4" />
            <text x="135" y="119" text-anchor="middle" font-size="24" font-weight="500" fill="#CCCCCC">
              B
            </text>

            <!-- 左侧座位 (使用不同形状) -->
            <g v-for="seat in getSeatsByTable('B', 'left').slice(0, 3)" :key="seat.id">
              <path
                v-if="seat.index < 3"
                :d="`M ${95} ${80 + getSeatY(seat.index) - 8} 
                     h 18 v 18 h -18 
                     a 9 9 0 0 1 0 -18 z`"
                :fill="getSeatColor(seat)"
                :class="[
                  'transition-all duration-200',
                  seat.status === 'available' || seat.status === 'selected'
                    ? 'cursor-pointer hover:opacity-80'
                    : 'cursor-not-allowed',
                ]"
                @click="handleSeatClick(seat)"
                @mouseenter="hoveredSeat = seat.id"
                @mouseleave="hoveredSeat = null"
              />
              <g v-if="isSeatSelected(seat.id)">
                <circle :cx="104" :cy="80 + getSeatY(seat.index)" r="7" fill="white" />
                <path
                  :d="`M 101 ${80 + getSeatY(seat.index)} L 103 ${82 + getSeatY(seat.index)} L 107 ${78 + getSeatY(seat.index)}`"
                  stroke="#A78BFA"
                  stroke-width="2"
                  fill="none"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                />
              </g>
            </g>

            <!-- 右侧座位 (使用不同形状) -->
            <g v-for="seat in getSeatsByTable('B', 'right').slice(0, 3)" :key="seat.id">
              <path
                v-if="seat.index < 3"
                :d="`M ${152} ${80 + getSeatY(seat.index) - 8} 
                     h 18 
                     a 9 9 0 0 1 0 18 
                     h -18 z`"
                :fill="getSeatColor(seat)"
                :class="[
                  'transition-all duration-200',
                  seat.status === 'available' || seat.status === 'selected'
                    ? 'cursor-pointer hover:opacity-80'
                    : 'cursor-not-allowed',
                ]"
                @click="handleSeatClick(seat)"
                @mouseenter="hoveredSeat = seat.id"
                @mouseleave="hoveredSeat = null"
              />
              <g v-if="isSeatSelected(seat.id)">
                <circle :cx="161" :cy="80 + getSeatY(seat.index)" r="7" fill="white" />
                <path
                  :d="`M 158 ${80 + getSeatY(seat.index)} L 160 ${82 + getSeatY(seat.index)} L 164 ${78 + getSeatY(seat.index)}`"
                  stroke="#A78BFA"
                  stroke-width="2"
                  fill="none"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                />
              </g>
            </g>
          </g>

          <!-- 区域 C (下半部分) -->
          <g id="area-c">
            <rect x="120" y="152" width="30" height="68" fill="#EAEAEA" rx="4" />
            <text x="135" y="191" text-anchor="middle" font-size="24" font-weight="500" fill="#CCCCCC">
              C
            </text>

            <!-- 左侧座位 (使用不同形状) -->
            <g v-for="seat in getSeatsByTable('C', 'left').slice(0, 3)" :key="seat.id">
              <path
                v-if="seat.index < 3"
                :d="`M ${95} ${152 + getSeatY(seat.index) - 8} 
                     h 18 v 18 h -18 
                     a 9 9 0 0 1 0 -18 z`"
                :fill="getSeatColor(seat)"
                :class="[
                  'transition-all duration-200',
                  seat.status === 'available' || seat.status === 'selected'
                    ? 'cursor-pointer hover:opacity-80'
                    : 'cursor-not-allowed',
                ]"
                @click="handleSeatClick(seat)"
                @mouseenter="hoveredSeat = seat.id"
                @mouseleave="hoveredSeat = null"
              />
              <g v-if="isSeatSelected(seat.id)">
                <circle :cx="104" :cy="152 + getSeatY(seat.index)" r="7" fill="white" />
                <path
                  :d="`M 101 ${152 + getSeatY(seat.index)} L 103 ${154 + getSeatY(seat.index)} L 107 ${150 + getSeatY(seat.index)}`"
                  stroke="#A78BFA"
                  stroke-width="2"
                  fill="none"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                />
              </g>
            </g>

            <!-- 右侧座位 (使用不同形状) -->
            <g v-for="seat in getSeatsByTable('C', 'right').slice(0, 3)" :key="seat.id">
              <path
                v-if="seat.index < 3"
                :d="`M ${152} ${152 + getSeatY(seat.index) - 8} 
                     h 18 
                     a 9 9 0 0 1 0 18 
                     h -18 z`"
                :fill="getSeatColor(seat)"
                :class="[
                  'transition-all duration-200',
                  seat.status === 'available' || seat.status === 'selected'
                    ? 'cursor-pointer hover:opacity-80'
                    : 'cursor-not-allowed',
                ]"
                @click="handleSeatClick(seat)"
                @mouseenter="hoveredSeat = seat.id"
                @mouseleave="hoveredSeat = null"
              />
              <g v-if="isSeatSelected(seat.id)">
                <circle :cx="161" :cy="152 + getSeatY(seat.index)" r="7" fill="white" />
                <path
                  :d="`M 158 ${152 + getSeatY(seat.index)} L 160 ${154 + getSeatY(seat.index)} L 164 ${150 + getSeatY(seat.index)}`"
                  stroke="#A78BFA"
                  stroke-width="2"
                  fill="none"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                />
              </g>
            </g>
          </g>

          <!-- 会议室 -->
          <g id="meeting-rooms">
            <g id="meeting-a">
              <rect x="20" y="270" width="70" height="35" fill="#EAEAEA" opacity="0.2" rx="6" />
              <svg x="28" y="278" width="18" height="18" viewBox="0 0 20 20" fill="none">
                <circle cx="7" cy="6" r="2.5" fill="#CCCCCC" />
                <circle cx="13" cy="6" r="2.5" fill="#CCCCCC" />
              </svg>
              <text x="50" y="290" font-size="10" font-weight="500" fill="#CCCCCC">Meeting</text>
              <text x="50" y="299" font-size="10" font-weight="500" fill="#CCCCCC">Room A</text>
            </g>
            <g id="meeting-b">
              <rect x="115" y="270" width="70" height="35" fill="#EAEAEA" opacity="0.2" rx="6" />
              <svg x="123" y="278" width="18" height="18" viewBox="0 0 20 20" fill="none">
                <circle cx="7" cy="6" r="2.5" fill="#CCCCCC" />
                <circle cx="13" cy="6" r="2.5" fill="#CCCCCC" />
              </svg>
              <text x="145" y="290" font-size="10" font-weight="500" fill="#CCCCCC">Meeting</text>
              <text x="145" y="299" font-size="10" font-weight="500" fill="#CCCCCC">Room B</text>
            </g>
          </g>
        </svg>
      </div>
    </div>

    <!-- 图例 -->
    <div class="flex items-center justify-center gap-6 mt-4">
      <div class="flex items-center gap-2">
        <div class="w-5 h-5 rounded-md bg-gray-lighter"></div>
        <span class="text-sm font-medium text-gray-dark">Occupied</span>
      </div>
      <div class="flex items-center gap-2">
        <div class="w-5 h-5 rounded-md bg-success"></div>
        <span class="text-sm font-medium text-gray-dark">Available</span>
      </div>
    </div>
  </div>
</template>
