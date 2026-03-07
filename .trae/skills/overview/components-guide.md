# 组件实现指南

本文档提供概览页面模块的组件实现指南和示例代码。

## 目录

- [ServerNav 组件](#servernav-组件)
- [CircleProgress 组件](#circleprogress-组件)
- [MonitorStats 组件](#monitorstats-组件)
- [ProcessManager 组件](#processmanager-组件)
- [NetworkIOChart 组件](#networkiochart-组件)
- [主页面逻辑](#主页面逻辑)

## ServerNav 组件

### 功能要点

- 显示服务器列表导航
- 显示服务器名称和状态
- 点击切换服务器

### 实现示例

```vue
<template>
  <div class="server-nav">
    <div
      v-for="server in servers"
      :key="server.id"
      :class="['server-item', { active: currentServerId === server.id }]"
      @click="$emit('select', server.id)"
    >
      <div class="server-status" :class="server.status"></div>
      <div class="server-name">{{ server.hostname }}</div>
    </div>
  </div>
</template>

<script setup lang="ts">
import type { Server } from '@/types'

const props = defineProps<{
  servers: Server[]
  currentServerId: number | null
}>()

defineEmits<{
  select: [serverId: number]
}>()
</script>

<style scoped>
.server-nav {
  padding: 16px;
}

.server-item {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 12px;
  cursor: pointer;
  border-radius: 4px;
  transition: background 0.2s;
}

.server-item:hover {
  background: #f3f4f6;
}

.server-item.active {
  background: #e0f2fe;
  color: #0284c7;
}

.server-status {
  width: 8px;
  height: 8px;
  border-radius: 50%;
}

.server-status.online {
  background: #10b981;
}

.server-status.offline {
  background: #ef4444;
}
</style>
```

## CircleProgress 组件

### 功能要点

- 圆形进度展示
- 外框没有线条
- 圆圈是主题颜色的线条，线条是细的
- 支持悬停显示详细信息

### 实现示例

```vue
<template>
  <div class="circle-progress" @mouseenter="showDetail = true" @mouseleave="showDetail = false">
    <svg :width="size" :height="size">
      <circle
        :cx="size / 2"
        :cy="size / 2"
        :r="radius"
        fill="none"
        :stroke="backgroundColor"
        :stroke-width="strokeWidth"
      />
      <circle
        :cx="size / 2"
        :cy="size / 2"
        :r="radius"
        fill="none"
        :stroke="color"
        :stroke-width="strokeWidth"
        :stroke-dasharray="circumference"
        :stroke-dashoffset="offset"
        stroke-linecap="round"
        transform="rotate(-90)"
        :transform-origin="`${size / 2} ${size / 2}`"
      />
    </svg>
    <div class="progress-content">
      <div class="progress-value">{{ displayValue }}</div>
      <div class="progress-label">{{ label }}</div>
    </div>
    <div v-if="showDetail" class="detail-panel">
      <slot name="detail"></slot>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

const props = defineProps<{
  value: number
  maxValue?: number
  size?: number
  strokeWidth?: number
  color?: string
  backgroundColor?: string
  label: string
  displayValue: string
}>()

const showDetail = ref(false)

const size = props.size || 120
const strokeWidth = props.strokeWidth || 4
const radius = (size - strokeWidth) / 2
const circumference = 2 * Math.PI * radius

const offset = computed(() => {
  const progress = props.maxValue ? props.value / props.maxValue : props.value / 100
  return circumference * (1 - progress)
})
</script>

<style scoped>
.circle-progress {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.progress-content {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  text-align: center;
}

.progress-value {
  font-size: 16px;
  font-weight: 600;
}

.progress-label {
  font-size: 12px;
  color: #6b7280;
}

.detail-panel {
  position: absolute;
  top: 100%;
  left: 50%;
  transform: translateX(-50%);
  margin-top: 0;
  padding: 12px;
  background: white;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  z-index: 10;
  min-width: 200px;
}
</style>
```

## MonitorStats 组件

### 功能要点

- 展示四个监控指标（CPU、内存、磁盘、负载）
- 使用圆形展示
- 支持悬停显示详细信息

### 实现示例

```vue
<template>
  <div class="monitor-stats">
    <CircleProgress
      :value="stats.cpu.usage * 100"
      :label="$t('overview.cpu')"
      :display-value="formatCPU(stats.cpu.usage, stats.cpu.cores)"
    >
      <template #detail>
        <div class="cpu-detail">
          <div v-for="(usage, index) in stats.cpu.perCoreUsage" :key="index">
            Core {{ index + 1 }}: {{ (usage * 100).toFixed(1) }}%
          </div>
          <button @click="showProcessManager('cpu')">
            查看使用率前五的进程
          </button>
        </div>
      </template>
    </CircleProgress>

    <CircleProgress
      :value="stats.memory.usage"
      :label="$t('overview.memory')"
      :display-value="formatMemory(stats.memory.used, stats.memory.total)"
    >
      <template #detail>
        <div class="memory-detail">
          <div>可用: {{ formatBytes(stats.memory.available) }}</div>
          <button @click="showProcessManager('memory')">
            查看使用率前五的进程
          </button>
        </div>
      </template>
    </CircleProgress>

    <CircleProgress
      :value="stats.disk.usage"
      :label="$t('overview.disk')"
      :display-value="formatDisk(stats.disk.used, stats.disk.total)"
    >
      <template #detail>
        <div class="disk-detail">
          <div v-for="partition in stats.disk.partitions" :key="partition.device">
            {{ partition.mountPoint }}: {{ partition.usage.toFixed(1) }}%
          </div>
        </div>
      </template>
    </CircleProgress>

    <CircleProgress
      :value="stats.loadAverage.load1 * 100 / stats.cpu.cores"
      :label="$t('overview.load')"
      :display-value="formatLoad(stats.loadAverage)"
    >
      <template #detail>
        <div class="load-detail">
          <div>1分钟: {{ stats.loadAverage.load1.toFixed(2) }}</div>
          <div>5分钟: {{ stats.loadAverage.load5.toFixed(2) }}</div>
          <div>15分钟: {{ stats.loadAverage.load15.toFixed(2) }}</div>
        </div>
      </template>
    </CircleProgress>
  </div>
</template>

<script setup lang="ts">
import CircleProgress from './CircleProgress.vue'
import type { MonitorStats } from '@/types'

const props = defineProps<{
  stats: MonitorStats
}>()

const emit = defineEmits<{
  showProcessManager: [type: 'cpu' | 'memory']
}>()

const formatBytes = (bytes: number): string => {
  const gb = bytes / (1024 * 1024 * 1024)
  return `${gb.toFixed(2)} GB`
}

const formatCPU = (usage: number, cores: number): string => {
  const usedCores = usage * cores
  return `(${usedCores.toFixed(2)} / ${cores}) 核`
}

const formatMemory = (used: number, total: number): string => {
  return `${formatBytes(used)} / ${formatBytes(total)}`
}

const formatDisk = (used: number, total: number): string => {
  return `${formatBytes(used)} / ${formatBytes(total)}`
}

const formatLoad = (load: LoadAverage): string => {
  if (load.load1 < 1) {
    return '运行流畅'
  } else if (load.load1 < 2) {
    return '负载适中'
  } else {
    return '负载较高'
  }
}

const showProcessManager = (type: 'cpu' | 'memory') => {
  emit('showProcessManager', type)
}
</script>

<style scoped>
.monitor-stats {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 24px;
  padding: 24px;
}
</style>
```

## ProcessManager 组件

### 功能要点

- 显示进程列表
- 支持杀死进程
- 显示进程详细信息

### 实现示例

```vue
<template>
  <div class="process-manager">
    <div class="process-list">
      <div v-for="process in processes" :key="process.pid" class="process-item">
        <div class="process-name">{{ process.name }}</div>
        <div class="process-info">
          <span>PID: {{ process.pid }}</span>
          <span>CPU: {{ (process.cpuPercent * 100).toFixed(1) }}%</span>
          <span>内存: {{ process.memoryMB.toFixed(0) }} MB</span>
        </div>
        <button class="kill-btn" @click="killProcess(process.pid)">
          杀死进程
        </button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import type { ProcessInfo } from '@/types'

const props = defineProps<{
  processes: ProcessInfo[]
}>()

const emit = defineEmits<{
  killProcess: [pid: number]
}>()

const killProcess = (pid: number) => {
  emit('killProcess', pid)
}
</script>
```

## NetworkIOChart 组件

### 功能要点

- 展示网络流量和 IO 图表
- 支持选择网卡或磁盘
- 10 秒请求一次数据

### 实现示例

```vue
<template>
  <div class="network-io-chart">
    <div class="chart-controls">
      <select v-model="chartType">
        <option value="network">网卡流量</option>
        <option value="disk">磁盘 IO</option>
      </select>
      <select v-model="selectedDevice">
        <option v-for="device in devices" :key="device" :value="device">
          {{ device }}
        </option>
      </select>
    </div>
    <div class="chart-container">
      <!-- 折线图实现 -->
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

const chartType = ref<'network' | 'disk'>('network')
const selectedDevice = ref('')
const devices = ref<string[]>([])

let interval: number | null = null

const loadData = async () => {
  // 加载数据逻辑
}

onMounted(() => {
  loadData()
  interval = setInterval(loadData, 10000)
})

onUnmounted(() => {
  if (interval) {
    clearInterval(interval)
  }
})
</script>
```

## 主页面逻辑

### 主要功能

```typescript
const servers = ref<Server[]>([])
const currentServerId = ref<number | null>(null)
const monitorStats = ref<MonitorStats | null>(null)
const showProcessManager = ref(false)
const processType = ref<'cpu' | 'memory'>('cpu')

const currentServer = computed(() => {
  return servers.value.find(s => s.id === currentServerId.value)
})

const currentProcesses = computed(() => {
  if (!monitorStats.value) return []
  return processType.value === 'cpu' 
    ? monitorStats.value.topCPU 
    : monitorStats.value.topMemory
})

const loadServers = async () => {
  servers.value = await fetchServers()
  if (servers.value.length > 0) {
    currentServerId.value = servers.value[0].id
  }
}

const loadMonitorStats = async () => {
  if (!currentServerId.value) return
  monitorStats.value = await fetchMonitorStats(currentServerId.value)
}

const handleShowProcessManager = (type: 'cpu' | 'memory') => {
  processType.value = type
  showProcessManager.value = true
}

const handleKillProcess = async (pid: number) => {
  // 杀死进程逻辑
}

onMounted(() => {
  loadServers()
  
  // 5秒刷新一次监控数据
  setInterval(loadMonitorStats, 5000)
})
```
