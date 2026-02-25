---
name: "monitor"
description: "Squirrel 监控管理页面开发规范。包含服务器监控仪表盘、CPU/内存/磁盘/网络图表展示、历史数据趋势等组件的实现，以及 API 接口和国际化配置。在开发监控功能时调用。"
---

# Squirrel 监控管理页面开发规范

## 概述

监控页面提供对服务器的历史监控数据趋势分析功能，包括 CPU 使用率、内存使用率、磁盘 IO 和网络流量的可视化展示。支持多服务器切换、时间范围选择、设备筛选等功能。

**页面定位：**
- Overview 页面 (`/`) - 实时监控数据展示
- Monitor 页面 (`/monitor`) - 历史数据趋势分析

## 架构设计

```
┌─────────────────────────────────────────────────────────────────────┐
│                           Frontend (Vue)                            │
│  ├── Overview 页面 - 实时数据                                        │
│  └── Monitor 页面 - 历史数据                                         │
└─────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────┐
│                        squ-apiserver (代理层)                        │
│  - 根据 serverId 路由请求到对应的 agent                              │
│  - 统一认证和权限控制                                                │
└─────────────────────────────────────────────────────────────────────┘
                                    │
              ┌─────────────────────┼─────────────────────┐
              ▼                     ▼                     ▼
┌──────────────────────┐ ┌──────────────────────┐ ┌──────────────────────┐
│   squ-agent (主机1)   │ │   squ-agent (主机2)   │ │   squ-agent (主机N)   │
├──────────────────────┤ ├──────────────────────┤ ├──────────────────────┤
│ • 实时数据采集        │ │ • 实时数据采集        │ │ • 实时数据采集        │
│ • 定时数据存储        │ │ • 定时数据存储        │ │ • 定时数据存储        │
│ • 本地数据库(SQLite)  │ │ • 本地数据库(SQLite)  │ │ • 本地数据库(SQLite)  │
└──────────────────────┘ └──────────────────────┘ └──────────────────────┘
```

## 目录结构

```
views/Monitor/
├── index.vue                    # 监控中心主页
├── components/
│   ├── CPUMonitorChart.vue      # CPU历史趋势图表
│   ├── MemoryMonitorChart.vue   # 内存历史趋势图表
│   ├── DiskIOChart.vue          # 磁盘IO图表(含筛选)
│   ├── NetIOChart.vue           # 网络IO图表(含筛选)
│   ├── ServerSelector.vue       # 服务器选择器
│   └── TimeRangeSelector.vue    # 时间范围选择器
├── composables/
│   ├── useMonitorHistory.ts     # 历史数据查询逻辑
│   └── useDeviceList.ts         # 设备列表获取逻辑
└── types/
    └── index.ts                 # 页面类型定义

api/
└── monitor.ts                   # 监控相关API

types/
└── monitor.ts                   # 监控类型定义
```

## 数据模型

### BaseMonitor - 基础监控数据

| 字段 | 类型 | 说明 |
|------|------|------|
| id | uint | 主键 |
| cpu_usage | float64 | CPU 使用率 (0-1) |
| memory_usage | float64 | 内存使用率 (%) |
| memory_total | uint64 | 内存总量 (bytes) |
| memory_used | uint64 | 已用内存 (bytes) |
| disk_usage | float64 | 磁盘使用率 (%) |
| disk_total | uint64 | 磁盘总量 |
| disk_used | uint64 | 磁盘已用 |
| collect_time | time | 采集时间 |

### DiskIOMonitor - 磁盘 IO 监控

| 字段 | 类型 | 说明 |
|------|------|------|
| id | uint | 主键 |
| disk_name | string | 磁盘设备名 (sda, sdb...) |
| read_count | uint64 | 读取次数 |
| write_count | uint64 | 写入次数 |
| read_bytes | uint64 | 读取字节数 |
| write_bytes | uint64 | 写入字节数 |
| read_time | uint64 | 读取时间 (ms) |
| write_time | uint64 | 写入时间 (ms) |
| io_time | uint64 | IO时间 |
| weighted_io_time | uint64 | 加权IO时间 |
| iops_in_progress | uint64 | 进行中的IOPS |
| collect_time | time | 采集时间 |

### NetworkMonitor - 网络监控

| 字段 | 类型 | 说明 |
|------|------|------|
| id | uint | 主键 |
| interface_name | string | 网卡名 (eth0, eth1...) |
| bytes_sent | uint64 | 发送字节数 |
| bytes_recv | uint64 | 接收字节数 |
| packets_sent | uint64 | 发送包数 |
| packets_recv | uint64 | 接收包数 |
| err_in | uint64 | 接收错误数 |
| err_out | uint64 | 发送错误数 |
| drop_in | uint64 | 接收丢包数 |
| drop_out | uint64 | 发送丢包数 |
| fifo_in | uint64 | FIFO接收 |
| fifo_out | uint64 | FIFO发送 |
| collect_time | time | 采集时间 |

## 类型定义

```typescript
// types/monitor.ts

export interface BaseMonitorRecord {
  id: number
  cpu_usage: number
  memory_usage: number
  memory_total: number
  memory_used: number
  disk_usage: number
  disk_total: number
  disk_used: number
  collect_time: string
}

export interface DiskIORecord {
  id: number
  disk_name: string
  read_count: number
  write_count: number
  read_bytes: number
  write_bytes: number
  read_time: number
  write_time: number
  io_time: number
  weighted_io_time: number
  iops_in_progress: number
  collect_time: string
}

export interface NetworkIORecord {
  id: number
  interface_name: string
  bytes_sent: number
  bytes_recv: number
  packets_sent: number
  packets_recv: number
  err_in: number
  err_out: number
  drop_in: number
  drop_out: number
  fifo_in: number
  fifo_out: number
  collect_time: string
}

export interface PageData<T> {
  list: T[]
  total: number
  page: number
  size: number
}

export type TimeRange = '1h' | '6h' | '24h' | '7d' | '30d'

export interface ChartDataPoint {
  time: string
  value1: number
  value2?: number
}
```

## API 接口

```typescript
// api/monitor.ts
import { get } from '@/utils/request'
import type { PageData, BaseMonitorRecord, DiskIORecord, NetworkIORecord } from '@/types/monitor'

export function fetchMonitorStats(serverId: number): Promise<MonitorData> {
  return get(`/monitor/stats/${serverId}`)
}

export function fetchBaseMonitorHistory(
  serverId: number,
  page: number,
  count: number
): Promise<PageData<BaseMonitorRecord>> {
  return get(`/monitor/base/${serverId}/${page}/${count}`)
}

export function fetchDiskIOHistory(
  serverId: number,
  page: number,
  count: number
): Promise<PageData<DiskIORecord>> {
  return get(`/monitor/disk/${serverId}/${page}/${count}`)
}

export function fetchNetIOHistory(
  serverId: number,
  page: number,
  count: number
): Promise<PageData<NetworkIORecord>> {
  return get(`/monitor/net/${serverId}/${page}/${count}`)
}

export function fetchAllDiskIO(serverId: number): Promise<any> {
  return get(`/monitor/stats/io/${serverId}/all`)
}

export function fetchAllNetIO(serverId: number): Promise<any> {
  return get(`/monitor/stats/net/${serverId}/all`)
}

export function fetchDiskIO(serverId: number, device: string): Promise<any> {
  return get(`/monitor/stats/io/${serverId}/${device}`)
}

export function fetchNetIO(serverId: number, iface: string): Promise<any> {
  return get(`/monitor/stats/net/${serverId}/${iface}`)
}
```

## 页面布局

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  📊 监控中心                                        [服务器选择 ▼]          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────────────┐  ┌─────────────────────────┐                  │
│  │      📈 表盘1: CPU       │  │     📈 表盘2: 内存       │                  │
│  │  ─────────────────────  │  │  ─────────────────────  │                  │
│  │  │ ▁▂▃▄▅▆▇█▇▆▅▄▃▂▁   │  │  │ ▁▂▃▄▅▆▇█▇▆▅▄▃▂▁   │                  │
│  │  │   历史趋势图表        │  │  │   历史趋势图表        │                  │
│  │  ─────────────────────  │  │  ─────────────────────  │                  │
│  │  数据: cpu_usage        │  │  数据: memory_usage     │                  │
│  └─────────────────────────┘  └─────────────────────────┘                  │
│                                                                             │
│  ┌─────────────────────────┐  ┌─────────────────────────┐                  │
│  │   💾 表盘3: 磁盘IO       │  │   🌐 表盘4: 网络IO       │                  │
│  │        [sda ▼]          │  │        [eth0 ▼]         │                  │
│  │  ─────────────────────  │  │  ─────────────────────  │                  │
│  │  │ ▁▂▃▄▅▆▇█▇▆▅▄▃▂▁   │  │  │ ▁▂▃▄▅▆▇█▇▆▅▄▃▂▁   │                  │
│  │  │   历史趋势图表        │  │  │   历史趋势图表        │                  │
│  │  ─────────────────────  │  │  ─────────────────────  │                  │
│  │  默认: 所有磁盘总量      │  │  默认: 所有网卡总量      │                  │
│  │  可选: sda, sdb, all    │  │  可选: eth0, eth1, all  │                  │
│  └─────────────────────────┘  └─────────────────────────┘                  │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────────┐│
│  │  ⏱️ 时间范围      [1小时] [6小时] [24小时] [7天] [自定义]               ││
│  └─────────────────────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────────────────────┘
```

## 表盘设计说明

| 表盘 | 数据来源 | 默认展示 | 筛选选项 | 说明 |
|------|---------|---------|---------|------|
| **表盘1** | BaseMonitor.cpu_usage | CPU使用率趋势 | 无 | 单一指标，无需筛选 |
| **表盘2** | BaseMonitor.memory_usage | 内存使用率趋势 | 无 | 单一指标，无需筛选 |
| **表盘3** | DiskIOMonitor | 所有磁盘IO总量 | sda, sdb, ... , all | 支持选择单个磁盘或总量 |
| **表盘4** | NetworkMonitor | 所有网卡流量总量 | eth0, eth1, ... , all | 支持选择单个网卡或总量 |

## 交互设计

### 1. 服务器选择器
- 位置：页面右上角
- 功能：切换查看不同服务器的监控数据
- 触发：选择后刷新所有表盘数据

### 2. 时间范围选择器
- 位置：页面底部
- 选项：1小时、6小时、24小时、7天、自定义
- 功能：统一控制所有表盘的时间范围

### 3. 磁盘/网卡选择器
- 位置：各自表盘右上角
- 功能：筛选特定设备的数据
- 默认值：`all` (显示总量)

## 组件实现

### CPUMonitorChart 组件

```vue
<template>
  <div class="chart-card">
    <div class="chart-header">
      <h3 class="chart-title">
        <Icon icon="lucide:cpu" />
        {{ $t('monitor.cpuUsage') }}
      </h3>
      <span class="chart-value" :class="getUsageClass(latestValue)">
        {{ formatPercent(latestValue) }}
      </span>
    </div>
    <div class="chart-container" ref="chartContainer"></div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch, onMounted, onUnmounted, nextTick } from 'vue'
import { useI18n } from 'vue-i18n'
import { Icon } from '@iconify/vue'
import * as echarts from 'echarts'
import type { BaseMonitorRecord } from '@/types/monitor'
import { formatPercent } from '@/utils/format'

const props = defineProps<{
  data: BaseMonitorRecord[]
}>()

const chartContainer = ref<HTMLDivElement>()
let chartInstance: echarts.ECharts | null = null

const latestValue = computed(() => {
  if (props.data.length === 0) return 0
  return props.data[props.data.length - 1].cpu_usage * 100
})

const getUsageClass = (value: number) => {
  if (value >= 90) return 'danger'
  if (value >= 70) return 'warning'
  return 'normal'
}

const getChartOption = () => {
  const times = props.data.map(d => 
    new Date(d.collect_time).toLocaleTimeString('zh-CN', { hour: '2-digit', minute: '2-digit' })
  )
  const values = props.data.map(d => d.cpu_usage * 100)

  return {
    tooltip: {
      trigger: 'axis',
      formatter: (params: any) => {
        return `<strong>${params[0].axisValue}</strong><br/>
                CPU: ${params[0].value.toFixed(2)}%`
      }
    },
    grid: {
      left: '2%',
      right: '2%',
      bottom: '15%',
      top: '10%',
      containLabel: true
    },
    xAxis: {
      type: 'category',
      data: times,
      axisLine: { lineStyle: { color: '#e2e8f0' } },
      axisLabel: { color: '#64748b', fontSize: 11, rotate: 30 },
      axisTick: { show: false }
    },
    yAxis: {
      type: 'value',
      min: 0,
      max: 100,
      axisLine: { show: false },
      axisTick: { show: false },
      splitLine: { lineStyle: { color: '#e2e8f0', type: 'dashed' } },
      axisLabel: { 
        color: '#64748b', 
        fontSize: 11,
        formatter: '{value}%'
      }
    },
    series: [{
      type: 'line',
      smooth: true,
      symbol: 'none',
      lineStyle: { width: 2, color: '#4fc3f7' },
      areaStyle: {
        color: {
          type: 'linear',
          x: 0, y: 0, x2: 0, y2: 1,
          colorStops: [
            { offset: 0, color: 'rgba(79, 195, 247, 0.3)' },
            { offset: 1, color: 'rgba(79, 195, 247, 0)' }
          ]
        }
      },
      data: values
    }]
  }
}

const initChart = () => {
  if (!chartContainer.value) return
  chartInstance = echarts.init(chartContainer.value)
  updateChart()
}

const updateChart = () => {
  if (!chartInstance) return
  chartInstance.setOption(getChartOption())
}

const handleResize = () => chartInstance?.resize()

onMounted(() => {
  window.addEventListener('resize', handleResize)
  nextTick(() => initChart())
})

onUnmounted(() => {
  window.removeEventListener('resize', handleResize)
  chartInstance?.dispose()
})

watch(() => props.data, () => nextTick(() => updateChart()), { deep: true })
</script>

<style scoped>
.chart-card {
  background: #ffffff;
  border-radius: 8px;
  padding: 16px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.chart-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 12px;
}

.chart-title {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 14px;
  font-weight: 600;
  color: #1e3a5f;
  margin: 0;
}

.chart-value {
  font-size: 18px;
  font-weight: 600;
}

.chart-value.normal { color: #67c23a; }
.chart-value.warning { color: #e6a23c; }
.chart-value.danger { color: #f56c6c; }

.chart-container {
  height: 200px;
  width: 100%;
}
</style>
```

### MemoryMonitorChart 组件

```vue
<template>
  <div class="chart-card">
    <div class="chart-header">
      <h3 class="chart-title">
        <Icon icon="lucide:hard-drive" />
        {{ $t('monitor.memoryUsage') }}
      </h3>
      <span class="chart-value" :class="getUsageClass(latestValue)">
        {{ formatPercent(latestValue) }}
      </span>
    </div>
    <div class="chart-info">
      {{ formatBytes(latestUsed) }} / {{ formatBytes(latestTotal) }}
    </div>
    <div class="chart-container" ref="chartContainer"></div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch, onMounted, onUnmounted, nextTick } from 'vue'
import { Icon } from '@iconify/vue'
import * as echarts from 'echarts'
import type { BaseMonitorRecord } from '@/types/monitor'
import { formatPercent, formatBytes } from '@/utils/format'

const props = defineProps<{
  data: BaseMonitorRecord[]
}>()

const chartContainer = ref<HTMLDivElement>()
let chartInstance: echarts.ECharts | null = null

const latestValue = computed(() => {
  if (props.data.length === 0) return 0
  return props.data[props.data.length - 1].memory_usage
})

const latestUsed = computed(() => {
  if (props.data.length === 0) return 0
  return props.data[props.data.length - 1].memory_used
})

const latestTotal = computed(() => {
  if (props.data.length === 0) return 0
  return props.data[props.data.length - 1].memory_total
})

const getUsageClass = (value: number) => {
  if (value >= 90) return 'danger'
  if (value >= 70) return 'warning'
  return 'normal'
}

const getChartOption = () => {
  const times = props.data.map(d => 
    new Date(d.collect_time).toLocaleTimeString('zh-CN', { hour: '2-digit', minute: '2-digit' })
  )
  const values = props.data.map(d => d.memory_usage)

  return {
    tooltip: {
      trigger: 'axis',
      formatter: (params: any) => {
        return `<strong>${params[0].axisValue}</strong><br/>
                ${$t('monitor.memoryUsage')}: ${params[0].value.toFixed(2)}%`
      }
    },
    grid: {
      left: '2%',
      right: '2%',
      bottom: '15%',
      top: '10%',
      containLabel: true
    },
    xAxis: {
      type: 'category',
      data: times,
      axisLine: { lineStyle: { color: '#e2e8f0' } },
      axisLabel: { color: '#64748b', fontSize: 11, rotate: 30 },
      axisTick: { show: false }
    },
    yAxis: {
      type: 'value',
      min: 0,
      max: 100,
      axisLine: { show: false },
      axisTick: { show: false },
      splitLine: { lineStyle: { color: '#e2e8f0', type: 'dashed' } },
      axisLabel: { 
        color: '#64748b', 
        fontSize: 11,
        formatter: '{value}%'
      }
    },
    series: [{
      type: 'line',
      smooth: true,
      symbol: 'none',
      lineStyle: { width: 2, color: '#67c23a' },
      areaStyle: {
        color: {
          type: 'linear',
          x: 0, y: 0, x2: 0, y2: 1,
          colorStops: [
            { offset: 0, color: 'rgba(103, 194, 58, 0.3)' },
            { offset: 1, color: 'rgba(103, 194, 58, 0)' }
          ]
        }
      },
      data: values
    }]
  }
}

const initChart = () => {
  if (!chartContainer.value) return
  chartInstance = echarts.init(chartContainer.value)
  updateChart()
}

const updateChart = () => {
  if (!chartInstance) return
  chartInstance.setOption(getChartOption())
}

const handleResize = () => chartInstance?.resize()

onMounted(() => {
  window.addEventListener('resize', handleResize)
  nextTick(() => initChart())
})

onUnmounted(() => {
  window.removeEventListener('resize', handleResize)
  chartInstance?.dispose()
})

watch(() => props.data, () => nextTick(() => updateChart()), { deep: true })
</script>

<style scoped>
.chart-card {
  background: #ffffff;
  border-radius: 8px;
  padding: 16px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.chart-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 8px;
}

.chart-title {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 14px;
  font-weight: 600;
  color: #1e3a5f;
  margin: 0;
}

.chart-value {
  font-size: 18px;
  font-weight: 600;
}

.chart-value.normal { color: #67c23a; }
.chart-value.warning { color: #e6a23c; }
.chart-value.danger { color: #f56c6c; }

.chart-info {
  font-size: 12px;
  color: #64748b;
  margin-bottom: 12px;
}

.chart-container {
  height: 200px;
  width: 100%;
}
</style>
```

### DiskIOChart 组件

```vue
<template>
  <div class="chart-card">
    <div class="chart-header">
      <h3 class="chart-title">
        <Icon icon="lucide:database" />
        {{ $t('monitor.diskIO') }}
      </h3>
      <select v-model="selectedDevice" class="device-select">
        <option value="all">{{ $t('monitor.all') }}</option>
        <option v-for="device in devices" :key="device" :value="device">
          {{ device }}
        </option>
      </select>
    </div>
    <div class="chart-stats">
      <div class="stat-item">
        <span class="stat-label">{{ $t('monitor.read') }}</span>
        <span class="stat-value">{{ formatBytes(latestReadBytes) }}</span>
      </div>
      <div class="stat-item">
        <span class="stat-label">{{ $t('monitor.write') }}</span>
        <span class="stat-value">{{ formatBytes(latestWriteBytes) }}</span>
      </div>
    </div>
    <div class="chart-container" ref="chartContainer"></div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch, onMounted, onUnmounted, nextTick } from 'vue'
import { useI18n } from 'vue-i18n'
import { Icon } from '@iconify/vue'
import * as echarts from 'echarts'
import type { DiskIORecord } from '@/types/monitor'
import { formatBytes } from '@/utils/format'

const props = defineProps<{
  data: DiskIORecord[]
  devices: string[]
}>()

const { t } = useI18n()
const selectedDevice = ref('all')
const chartContainer = ref<HTMLDivElement>()
let chartInstance: echarts.ECharts | null = null

const filteredData = computed(() => {
  if (selectedDevice.value === 'all') {
    const grouped: { [time: string]: DiskIORecord } = {}
    props.data.forEach(record => {
      const time = record.collect_time
      if (!grouped[time]) {
        grouped[time] = { ...record, disk_name: 'all' }
      } else {
        grouped[time].read_bytes += record.read_bytes
        grouped[time].write_bytes += record.write_bytes
        grouped[time].read_count += record.read_count
        grouped[time].write_count += record.write_count
      }
    })
    return Object.values(grouped).sort((a, b) => 
      new Date(a.collect_time).getTime() - new Date(b.collect_time).getTime()
    )
  }
  return props.data.filter(d => d.disk_name === selectedDevice.value)
})

const latestReadBytes = computed(() => {
  if (filteredData.value.length === 0) return 0
  return filteredData.value[filteredData.value.length - 1].read_bytes
})

const latestWriteBytes = computed(() => {
  if (filteredData.value.length === 0) return 0
  return filteredData.value[filteredData.value.length - 1].write_bytes
})

const getChartOption = () => {
  const times = filteredData.value.map(d => 
    new Date(d.collect_time).toLocaleTimeString('zh-CN', { hour: '2-digit', minute: '2-digit' })
  )
  const readValues = filteredData.value.map(d => d.read_bytes)
  const writeValues = filteredData.value.map(d => d.write_bytes)

  return {
    tooltip: {
      trigger: 'axis',
      formatter: (params: any) => {
        let result = `<strong>${params[0].axisValue}</strong><br/>`
        params.forEach((param: any) => {
          result += `${param.marker} ${param.seriesName}: ${formatBytes(param.value)}<br/>`
        })
        return result
      }
    },
    legend: {
      data: [t('monitor.read'), t('monitor.write')],
      right: '2%',
      top: 0,
      textStyle: { color: '#64748b', fontSize: 12 }
    },
    grid: {
      left: '2%',
      right: '2%',
      bottom: '15%',
      top: '15%',
      containLabel: true
    },
    xAxis: {
      type: 'category',
      data: times,
      axisLine: { lineStyle: { color: '#e2e8f0' } },
      axisLabel: { color: '#64748b', fontSize: 11, rotate: 30 },
      axisTick: { show: false }
    },
    yAxis: {
      type: 'value',
      axisLine: { show: false },
      axisTick: { show: false },
      splitLine: { lineStyle: { color: '#e2e8f0', type: 'dashed' } },
      axisLabel: { 
        color: '#64748b', 
        fontSize: 11,
        formatter: (value: number) => formatBytes(value)
      }
    },
    series: [
      {
        name: t('monitor.read'),
        type: 'line',
        smooth: true,
        symbol: 'none',
        lineStyle: { width: 2, color: '#4fc3f7' },
        data: readValues
      },
      {
        name: t('monitor.write'),
        type: 'line',
        smooth: true,
        symbol: 'none',
        lineStyle: { width: 2, color: '#94a3b8' },
        data: writeValues
      }
    ]
  }
}

const initChart = () => {
  if (!chartContainer.value) return
  chartInstance = echarts.init(chartContainer.value)
  updateChart()
}

const updateChart = () => {
  if (!chartInstance) return
  chartInstance.setOption(getChartOption())
}

const handleResize = () => chartInstance?.resize()

onMounted(() => {
  window.addEventListener('resize', handleResize)
  nextTick(() => initChart())
})

onUnmounted(() => {
  window.removeEventListener('resize', handleResize)
  chartInstance?.dispose()
})

watch([() => props.data, selectedDevice], () => nextTick(() => updateChart()), { deep: true })
</script>

<style scoped>
.chart-card {
  background: #ffffff;
  border-radius: 8px;
  padding: 16px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.chart-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 12px;
}

.chart-title {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 14px;
  font-weight: 600;
  color: #1e3a5f;
  margin: 0;
}

.device-select {
  padding: 4px 8px;
  font-size: 12px;
  border: 1px solid #e2e8f0;
  border-radius: 4px;
  background: #f8fafc;
  color: #1e3a5f;
  cursor: pointer;
}

.chart-stats {
  display: flex;
  gap: 16px;
  margin-bottom: 12px;
}

.stat-item {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 4px 8px;
  background: #f8fafc;
  border-radius: 4px;
  font-size: 12px;
}

.stat-label {
  color: #64748b;
}

.stat-value {
  font-weight: 500;
  color: #1e3a5f;
}

.chart-container {
  height: 200px;
  width: 100%;
}
</style>
```

### NetIOChart 组件

```vue
<template>
  <div class="chart-card">
    <div class="chart-header">
      <h3 class="chart-title">
        <Icon icon="lucide:activity" />
        {{ $t('monitor.networkTraffic') }}
      </h3>
      <select v-model="selectedInterface" class="device-select">
        <option value="all">{{ $t('monitor.all') }}</option>
        <option v-for="iface in interfaces" :key="iface" :value="iface">
          {{ iface }}
        </option>
      </select>
    </div>
    <div class="chart-stats">
      <div class="stat-item">
        <span class="stat-label">{{ $t('monitor.upload') }}</span>
        <span class="stat-value">{{ formatBytes(latestBytesSent) }}</span>
      </div>
      <div class="stat-item">
        <span class="stat-label">{{ $t('monitor.download') }}</span>
        <span class="stat-value">{{ formatBytes(latestBytesRecv) }}</span>
      </div>
    </div>
    <div class="chart-container" ref="chartContainer"></div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch, onMounted, onUnmounted, nextTick } from 'vue'
import { useI18n } from 'vue-i18n'
import { Icon } from '@iconify/vue'
import * as echarts from 'echarts'
import type { NetworkIORecord } from '@/types/monitor'
import { formatBytes } from '@/utils/format'

const props = defineProps<{
  data: NetworkIORecord[]
  interfaces: string[]
}>()

const { t } = useI18n()
const selectedInterface = ref('all')
const chartContainer = ref<HTMLDivElement>()
let chartInstance: echarts.ECharts | null = null

const filteredData = computed(() => {
  if (selectedInterface.value === 'all') {
    const grouped: { [time: string]: NetworkIORecord } = {}
    props.data.forEach(record => {
      const time = record.collect_time
      if (!grouped[time]) {
        grouped[time] = { ...record, interface_name: 'all' }
      } else {
        grouped[time].bytes_sent += record.bytes_sent
        grouped[time].bytes_recv += record.bytes_recv
        grouped[time].packets_sent += record.packets_sent
        grouped[time].packets_recv += record.packets_recv
      }
    })
    return Object.values(grouped).sort((a, b) => 
      new Date(a.collect_time).getTime() - new Date(b.collect_time).getTime()
    )
  }
  return props.data.filter(d => d.interface_name === selectedInterface.value)
})

const latestBytesSent = computed(() => {
  if (filteredData.value.length === 0) return 0
  return filteredData.value[filteredData.value.length - 1].bytes_sent
})

const latestBytesRecv = computed(() => {
  if (filteredData.value.length === 0) return 0
  return filteredData.value[filteredData.value.length - 1].bytes_recv
})

const getChartOption = () => {
  const times = filteredData.value.map(d => 
    new Date(d.collect_time).toLocaleTimeString('zh-CN', { hour: '2-digit', minute: '2-digit' })
  )
  const sentValues = filteredData.value.map(d => d.bytes_sent)
  const recvValues = filteredData.value.map(d => d.bytes_recv)

  return {
    tooltip: {
      trigger: 'axis',
      formatter: (params: any) => {
        let result = `<strong>${params[0].axisValue}</strong><br/>`
        params.forEach((param: any) => {
          result += `${param.marker} ${param.seriesName}: ${formatBytes(param.value)}<br/>`
        })
        return result
      }
    },
    legend: {
      data: [t('monitor.upload'), t('monitor.download')],
      right: '2%',
      top: 0,
      textStyle: { color: '#64748b', fontSize: 12 }
    },
    grid: {
      left: '2%',
      right: '2%',
      bottom: '15%',
      top: '15%',
      containLabel: true
    },
    xAxis: {
      type: 'category',
      data: times,
      axisLine: { lineStyle: { color: '#e2e8f0' } },
      axisLabel: { color: '#64748b', fontSize: 11, rotate: 30 },
      axisTick: { show: false }
    },
    yAxis: {
      type: 'value',
      axisLine: { show: false },
      axisTick: { show: false },
      splitLine: { lineStyle: { color: '#e2e8f0', type: 'dashed' } },
      axisLabel: { 
        color: '#64748b', 
        fontSize: 11,
        formatter: (value: number) => formatBytes(value)
      }
    },
    series: [
      {
        name: t('monitor.upload'),
        type: 'line',
        smooth: true,
        symbol: 'none',
        lineStyle: { width: 2, color: '#4fc3f7' },
        data: sentValues
      },
      {
        name: t('monitor.download'),
        type: 'line',
        smooth: true,
        symbol: 'none',
        lineStyle: { width: 2, color: '#94a3b8' },
        data: recvValues
      }
    ]
  }
}

const initChart = () => {
  if (!chartContainer.value) return
  chartInstance = echarts.init(chartContainer.value)
  updateChart()
}

const updateChart = () => {
  if (!chartInstance) return
  chartInstance.setOption(getChartOption())
}

const handleResize = () => chartInstance?.resize()

onMounted(() => {
  window.addEventListener('resize', handleResize)
  nextTick(() => initChart())
})

onUnmounted(() => {
  window.removeEventListener('resize', handleResize)
  chartInstance?.dispose()
})

watch([() => props.data, selectedInterface], () => nextTick(() => updateChart()), { deep: true })
</script>

<style scoped>
.chart-card {
  background: #ffffff;
  border-radius: 8px;
  padding: 16px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.chart-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 12px;
}

.chart-title {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 14px;
  font-weight: 600;
  color: #1e3a5f;
  margin: 0;
}

.device-select {
  padding: 4px 8px;
  font-size: 12px;
  border: 1px solid #e2e8f0;
  border-radius: 4px;
  background: #f8fafc;
  color: #1e3a5f;
  cursor: pointer;
}

.chart-stats {
  display: flex;
  gap: 16px;
  margin-bottom: 12px;
}

.stat-item {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 4px 8px;
  background: #f8fafc;
  border-radius: 4px;
  font-size: 12px;
}

.stat-label {
  color: #64748b;
}

.stat-value {
  font-weight: 500;
  color: #1e3a5f;
}

.chart-container {
  height: 200px;
  width: 100%;
}
</style>
```

### TimeRangeSelector 组件

```vue
<template>
  <div class="time-range-selector">
    <button
      v-for="range in ranges"
      :key="range.value"
      class="range-btn"
      :class="{ active: modelValue === range.value }"
      @click="$emit('update:modelValue', range.value)"
    >
      {{ range.label }}
    </button>
  </div>
</template>

<script setup lang="ts">
import { useI18n } from 'vue-i18n'
import type { TimeRange } from '@/types/monitor'

defineProps<{
  modelValue: TimeRange
}>()

defineEmits<{
  'update:modelValue': [value: TimeRange]
}>()

const { t } = useI18n()

const ranges = [
  { value: '1h' as TimeRange, label: t('monitor.last1Hour') },
  { value: '6h' as TimeRange, label: t('monitor.last6Hours') },
  { value: '24h' as TimeRange, label: t('monitor.last24Hours') },
  { value: '7d' as TimeRange, label: t('monitor.last7Days') },
  { value: '30d' as TimeRange, label: t('monitor.last30Days') }
]
</script>

<style scoped>
.time-range-selector {
  display: flex;
  gap: 8px;
  padding: 16px;
  background: #ffffff;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.range-btn {
  padding: 8px 16px;
  border: 1px solid #e2e8f0;
  border-radius: 6px;
  background: #fff;
  font-size: 13px;
  color: #64748b;
  cursor: pointer;
  transition: all 0.2s;
}

.range-btn:hover {
  border-color: #4fc3f7;
  color: #4fc3f7;
}

.range-btn.active {
  background: #4fc3f7;
  border-color: #4fc3f7;
  color: #fff;
}
</style>
```

## 主页面实现

```vue
<template>
  <div class="monitor-page">
    <div class="page-header">
      <h1 class="page-title">{{ $t('monitor.title') }}</h1>
      <ServerSelector
        v-model="selectedServer"
        :servers="servers"
        @change="loadMonitorData"
      />
    </div>

    <Loading v-if="loading && !baseData.length" :text="$t('common.loading')" />

    <template v-else-if="selectedServer">
      <div class="charts-grid">
        <CPUMonitorChart :data="baseData" />
        <MemoryMonitorChart :data="baseData" />
        <DiskIOChart :data="diskData" :devices="diskDevices" />
        <NetIOChart :data="netData" :interfaces="netInterfaces" />
      </div>
      <TimeRangeSelector v-model="timeRange" />
    </template>

    <Empty
      v-else
      :description="$t('monitor.noServerSelected')"
      icon="lucide:monitor"
    />
  </div>
</template>

<script setup lang="ts">
import { ref, watch, onMounted } from 'vue'
import { useI18n } from 'vue-i18n'
import Loading from '@/components/Loading/index.vue'
import Empty from '@/components/Empty/index.vue'
import ServerSelector from './components/ServerSelector.vue'
import CPUMonitorChart from './components/CPUMonitorChart.vue'
import MemoryMonitorChart from './components/MemoryMonitorChart.vue'
import DiskIOChart from './components/DiskIOChart.vue'
import NetIOChart from './components/NetIOChart.vue'
import TimeRangeSelector from './components/TimeRangeSelector.vue'
import { 
  fetchBaseMonitorHistory, 
  fetchDiskIOHistory, 
  fetchNetIOHistory 
} from '@/api/monitor'
import { fetchServers } from '@/api/server'
import type { 
  BaseMonitorRecord, 
  DiskIORecord, 
  NetworkIORecord, 
  TimeRange,
  Server 
} from '@/types'

const { t } = useI18n()

const loading = ref(false)
const selectedServer = ref<number | null>(null)
const timeRange = ref<TimeRange>('1h')
const servers = ref<Server[]>([])
const baseData = ref<BaseMonitorRecord[]>([])
const diskData = ref<DiskIORecord[]>([])
const netData = ref<NetworkIORecord[]>([])
const diskDevices = ref<string[]>([])
const netInterfaces = ref<string[]>([])

const loadServers = async () => {
  servers.value = await fetchServers()
  if (servers.value.length > 0) {
    selectedServer.value = servers.value[0].id
  }
}

const loadMonitorData = async () => {
  if (!selectedServer.value) return
  
  loading.value = true
  try {
    const [base, disk, net] = await Promise.all([
      fetchBaseMonitorHistory(selectedServer.value, 1, 100),
      fetchDiskIOHistory(selectedServer.value, 1, 100),
      fetchNetIOHistory(selectedServer.value, 1, 100)
    ])
    
    baseData.value = base.list
    diskData.value = disk.list
    netData.value = net.list
    
    diskDevices.value = [...new Set(disk.list.map(d => d.disk_name))]
    netInterfaces.value = [...new Set(net.list.map(d => d.interface_name))]
  } catch (error) {
    console.error('Failed to load monitor data:', error)
  } finally {
    loading.value = false
  }
}

watch(selectedServer, (newVal) => {
  if (newVal) {
    loadMonitorData()
  }
})

watch(timeRange, () => {
  loadMonitorData()
})

onMounted(() => {
  loadServers()
})
</script>

<style scoped>
.monitor-page {
  padding: 20px;
  display: flex;
  flex-direction: column;
  gap: 16px;
  height: 100%;
  overflow-y: auto;
}

.page-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.page-title {
  font-size: 20px;
  font-weight: 600;
  color: #1e3a5f;
  margin: 0;
}

.charts-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 16px;
  flex: 1;
}

@media (max-width: 1200px) {
  .charts-grid {
    grid-template-columns: 1fr;
  }
}
</style>
```

## 国际化配置

```typescript
// lang/zh-CN/monitor.ts
export default {
  title: '监控中心',
  selectServer: '选择服务器',
  timeRange: '时间范围',
  last1Hour: '1小时',
  last6Hours: '6小时',
  last24Hours: '24小时',
  last7Days: '7天',
  last30Days: '30天',
  cpuUsage: 'CPU 使用率',
  memoryUsage: '内存使用率',
  diskUsage: '磁盘使用率',
  diskIO: '磁盘 IO',
  networkTraffic: '网络流量',
  read: '读取',
  write: '写入',
  upload: '上传',
  download: '下载',
  all: '全部',
  noServerSelected: '请选择服务器',
  loading: '加载中...'
}
```

```typescript
// lang/en-US/monitor.ts
export default {
  title: 'Monitor Center',
  selectServer: 'Select Server',
  timeRange: 'Time Range',
  last1Hour: '1 Hour',
  last6Hours: '6 Hours',
  last24Hours: '24 Hours',
  last7Days: '7 Days',
  last30Days: '30 Days',
  cpuUsage: 'CPU Usage',
  memoryUsage: 'Memory Usage',
  diskUsage: 'Disk Usage',
  diskIO: 'Disk I/O',
  networkTraffic: 'Network Traffic',
  read: 'Read',
  write: 'Write',
  upload: 'Upload',
  download: 'Download',
  all: 'All',
  noServerSelected: 'Please select a server',
  loading: 'Loading...'
}
```

## 路由配置

```typescript
{
  path: '/monitor',
  name: 'Monitor',
  component: () => import('@/views/Monitor/index.vue')
}
```

## 工具函数

```typescript
// utils/format.ts

export function formatBytes(bytes: number, decimals: number = 2): string {
  if (bytes === 0) return '0 B'
  const k = 1024
  const dm = decimals < 0 ? 0 : decimals
  const sizes = ['B', 'KB', 'MB', 'GB', 'TB', 'PB']
  const i = Math.floor(Math.log(bytes) / Math.log(k))
  return parseFloat((bytes / Math.pow(k, i)).toFixed(dm)) + ' ' + sizes[i]
}

export function formatPercent(value: number, decimals: number = 2): string {
  return value.toFixed(decimals) + '%'
}
```

## 设计规范

- 使用项目统一的配色方案（主色 #4fc3f7）
- 卡片式设计，圆角 8px
- 阴影效果：0 2px 8px rgba(0, 0, 0, 0.04)
- 图表使用 ECharts 库
- 使用率颜色：正常(#67c23a)、警告(#e6a23c)、危险(#f56c6c)
- 遵循 Squirrel 紧凑风格页面设计规范（调用 `compact-page-style` skill）

## 最佳实践

1. **图表优化**：使用 ECharts 的 dataZoom 处理大量数据点
2. **性能优化**：使用 computed 缓存图表数据转换
3. **错误处理**：API 调用添加 try-catch 处理
4. **国际化**：所有文本使用 $t() 函数包裹
5. **组件复用**：参考 `component-reusability` skill 设计可复用图表组件
6. **响应式设计**：使用 CSS Grid 实现自适应布局
7. **类型安全**：完整的 TypeScript 类型定义
8. **图表交互**：支持悬停提示、图例切换

## 相关文件

- 后端 Agent 处理器: `internal/squ-agent/handler/monitor/`
- 后端 API Server 代理: `internal/squ-apiserver/handler/monitor/`
- 后端数据模型: `internal/squ-agent/model/monitor.go`
- 后端定时任务: `internal/squ-agent/cron/monitor.go`
- 前端 API: `front/src/api/monitor.ts`
- 前端 Overview: `front/src/views/Overview/`

## 接口案例文件

- .trae\skills\monitor\api.md
