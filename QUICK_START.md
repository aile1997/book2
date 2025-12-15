# 快速开始指南 (Quick Start Guide)

## 🚀 快速启动

### 1. 安装依赖

```bash
npm install
```

### 2. 启动开发服务器

```bash
npm run dev
```

### 3. 访问预订页面

打开浏览器，访问：`http://localhost:5173/booking`

## 📖 功能使用指南

### 选择座位

1. 点击 **"点击选择座位"** 按钮
2. 在弹出的座位地图中：
   - 🟢 绿色座位 = 可用
   - ⚪ 灰色座位 = 已占用
3. 点击任意绿色座位
4. 点击 **"Confirm"** 确认选择

### 选择日期和时间

1. 查看可用日期（如：11.20 Wed.）
2. 点击时间段按钮（如：09:00 - 12:00）
3. 已选中的时间段会变成绿色

### 邀请伙伴

1. 点击 **"添加伙伴"** 按钮
2. 在弹出的窗口中：
   - **搜索模式：** 输入姓名搜索，点击结果添加
   - **桌子视图：** 点击 "Show All" 查看全部伙伴
     - 切换 Table A/B/C 标签
     - 查看每个桌子的座位分布
3. 点击 **"Confirm"** 确认选择
4. 点击标签上的 ❌ 可以移除已邀请的伙伴

### 完成预订

1. 确认预订摘要信息：
   - 座位号
   - 日期时间
   - 邀请的伙伴数量
   - 需要消耗的积分
2. 点击底部的 **"立即预订"** 按钮
3. 查看成功确认页面

## 🎨 界面说明

### 座位地图

```
┌─────────────────────────┐
│  Entrance      Bar      │
│                         │
│  [A] | [B] | [C]       │  ← 三个桌子区域
│  ○○   ○○   ○○          │  ← 左侧座位
│  ●●   ●●   ●●          │  ← 右侧座位
│                         │
│  Fitness    Lounge      │
└─────────────────────────┘
```

### 座位状态

- 🟢 **可用座位** - 可以点击选择
- ⚪ **已占用** - 不能选择
- 🟣 **已选中** - 你当前选择的座位

### 时间段选择

```
11.20  Wed.
  [09:00 - 12:00] ← 点击选择
  [12:00 - 18:00]

11.21  Thu.
  [09:00 - 12:00]
  [12:00 - 18:00]
```

## 💡 使用技巧

### 1. 重新选择座位

如果想更换座位，点击 **"重新选择"** 按钮即可。

### 2. 批量邀请伙伴

在查找伙伴窗口中：

1. 使用搜索快速找到特定伙伴
2. 使用桌子视图查看整个团队的座位

### 3. 查看预订摘要

选择完座位、时间和伙伴后，页面下方会自动显示预订摘要。

### 4. 移动端使用

在手机上访问时：

- 所有按钮都经过触摸优化
- 模态框会从底部滑出
- 字体大小自动调整

## 🔧 开发者提示

### 组件导入

```vue
<script setup lang="ts">
import SeatMap from '@/components/SeatMap.vue'
import FindPartnerModal from '@/components/FindPartnerModal.vue'
import { useSeats } from '@/composables/useSeats'
</script>
```

### 使用组合式函数

```typescript
const {
  seats, // 所有座位数据
  selectedSeat, // 当前选中的座位
  selectSeat, // 选择座位方法
  clearSelection, // 清除选择方法
} = useSeats()
```

### 自定义座位数据

编辑 `src/composables/useSeats.ts` 中的座位配置：

```typescript
const seats = ref<Seat[]>([
  {
    id: 'A1',
    table: 'A',
    position: 'left',
    index: 0,
    status: 'available',
  },
  // ... 更多座位
])
```

## 📱 移动端调试

### 在浏览器中模拟移动设备

1. 打开 Chrome DevTools (F12)
2. 点击设备切换按钮 (Ctrl+Shift+M)
3. 选择设备型号（如：iPhone 12 Pro）
4. 刷新页面

### 在真实设备上测试

1. 确保手机和电脑在同一网络
2. 获取电脑 IP 地址
3. 在手机浏览器访问：`http://YOUR_IP:5173/booking`

## 🐛 常见问题

### Q: 座位无法点击？

A: 检查座位状态是否为 `'available'`，只有可用座位才能点击。

### Q: 模态框不显示？

A: 确保 `visible` 状态正确设置为 `true`。

### Q: 样式不正确？

A: 确保已正确导入 TailwindCSS：

```css
/* src/assets/main.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### Q: TypeScript 报错？

A: 运行类型检查：

```bash
npm run type-check
```

## 📚 更多资源

- [完整文档](./BOOKING_SYSTEM_README.md)
- [实现总结](./IMPLEMENTATION_SUMMARY.md)
- [Vue 3 文档](https://vuejs.org/)
- [TailwindCSS 文档](https://tailwindcss.com/)
- [TypeScript 文档](https://www.typescriptlang.org/)

## 🆘 获取帮助

如果遇到问题：

1. 查看浏览器控制台错误信息
2. 检查 Vue DevTools
3. 阅读完整文档
4. 查看源代码注释（都是中文的）

---

**祝你使用愉快！** 🎉
