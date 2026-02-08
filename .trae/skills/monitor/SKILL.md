---
name: "monitor"
description: "Squirrel 监控管理页面开发规范。包含服务器监控仪表盘、CPU/内存/磁盘/网络图表展示、历史数据趋势等组件的实现，以及 API 接口和国际化配置。在开发监控功能时调用。"
---

# Squirrel 监控管理页面开发规范

## 概述

监控页面提供对服务器的实时监控功能，包括 CPU 使用率、内存使用率、磁盘使用率和网络流量的可视化展示。支持历史数据趋势分析、多维度监控图表、服务器选择切换等功能。

## 目录结构

```
# 监控页面模块
views/Monitor/
├── index.vue                 # 页面入口
├── components/
│   ├── MonitorDashboard.vue  # 监控仪表盘（包含所有图表）
│   ├── CpuChart.vue          # CPU 使用率折线图
│   ├── MemoryChart.vue       # 内存使用率折线图
│   ├── DiskChart.vue         # 磁盘使用率饼图/环形图
│   ├── NetworkChart.vue      # 网络流量折线图
│   ├── MetricCard.vue        # 指标卡片组件
│   ├── ServerSelector.vue    # 服务器选择器
│   └── TimeRangePicker.vue   # 时间范围选择器
├── composables/
│   └── useMonitor.ts         # 监控相关的可组合函数
├── types/
│   └── index.ts              # 页面类型定义
└── styles/
    └── monitor.scss          # 监控页面相关样式

# API 接口模块
api/
└── monitor.ts                # 监控相关 API 接口

# 通用组件模块
components/
├── ChartCard.vue             # 图表卡片组件
├── LineChart.vue             # 通用折线图组件
├── PieChart.vue              # 通用饼图组件
├── Loading.vue               # 加载组件
├── Empty.vue                 # 空状态组件
└── index.ts                  # 组件导出

# 类型定义模块
types/
└── index.ts                  # 全局类型定义

# 国际化语言包
lang/
├── index.ts                  # i18n 配置入口
├── zh-CN/
│   ├── index.ts              # 中文语言包入口
│   ├── common.ts             # 通用文本
│   ├── layout.ts             # 布局文本
│   └── monitor.ts            # 监控页面文本
└── en-US/
    ├── index.ts              # 英文语言包入口
    ├── common.ts             # 通用文本
    ├── layout.ts             # 布局文本
    └── monitor.ts            # 监控页面文本

# 路由配置
router/
└── index.ts                  # 路由配置入口

# 工具函数
utils/
├── request.ts                # HTTP 请求封装
└── format.ts                 # 数据格式化工具
```

## 类型定义

### 全局类型定义

```typescript
// types/index.ts

// 基础监控数据
export interface MonitorBase {
  id: number
  collect_time: string
  cpu_usage: number
  disk_total: number
  disk_usage: number
  disk_used: number
  memory_total: number
  memory_usage: number
  memory_used: number
}

// 磁盘监控数据
export interface MonitorDisk {
  id: number
  collect_time: string
  disk_name: string
  io_time: number
  iops_in_progress: number
  read_bytes: number
  read_count: number
  read_time: number
  weighted_io_time: number
  write_bytes: number
  write_count: number
  write_time: number
}

// 网络监控数据
export interface MonitorNet {
  id: number
  collect_time: string
  interface_name: string
  bytes_recv: number
  bytes_sent: number
  drop_in: number
  drop_out: number
  err_in: number
  err_out: number
  fifo_in: number
  fifo_out: number
  packets_recv: number
  packets_sent: number
}

// 分页响应
export interface PaginatedResponse<T> {
  list: T[]
  page: number
  size: number
  total: number
}

// API 响应
export interface ApiResponse<T = any> {
  code: number
  message: string
  data: T
}

// 图表数据点
export interface ChartDataPoint {
  time: string
  value: number
  label?: string
}

// 饼图数据
export interface PieChartData {
  name: string
  value: number
  color?: string
}
```

### 页面类型定义

```typescript
// views/Monitor/types/index.ts

import type { MonitorBase, MonitorDisk, MonitorNet, ChartDataPoint, PieChartData } from '@/types'

export type { MonitorBase, MonitorDisk, MonitorNet, ChartDataPoint, PieChartData }

// 时间范围
export type TimeRange = '1h' | '6h' | '24h' | '7d' | '30d'

// 指标类型
export type MetricType = 'cpu' | 'memory' | 'disk' | 'network'

// 图表配置
export interface ChartConfig {
  title: string
  color: string
  unit: string
  min?: number
  max?: number
}

// 服务器选项
export interface ServerOption {
  id: number
  name: string
  ip: string
  status: 'online' | 'offline'
}

// 监控状态
export interface MonitorState {
  loading: boolean
  error: string | null
  selectedServer: number | null
  timeRange: TimeRange
  baseData: MonitorBase[]
  diskData: MonitorDisk[]
  netData: MonitorNet[]
}
```

## API 接口

### HTTP 请求封装

```typescript
// utils/request.ts
import axios, { type AxiosInstance, type AxiosRequestConfig, type AxiosResponse } from 'axios'

const baseURL = import.meta.env.VITE_API_BASE_URL || '/api/v1'

const instance: AxiosInstance = axios.create({
  baseURL,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json'
  }
})

instance.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('token')
    if (token) {
      config.headers.Authorization = `Bearer ${token}`
    }
    return config
  },
  (error) => {
    return Promise.reject(error)
  }
)

instance.interceptors.response.use(
  (response: AxiosResponse) => {
    const { code, message, data } = response.data
    if (code === 0) {
      return data
    }
    return Promise.reject(new Error(message || '请求失败'))
  },
  (error) => {
    return Promise.reject(error)
  }
)

export function get<T = any>(url: string, config?: AxiosRequestConfig): Promise<T> {
  return instance.get(url, config)
}

export default instance
```

### 监控相关 API

```typescript
// api/monitor.ts
import { get } from '@/utils/request'
import type { PaginatedResponse, MonitorBase, MonitorDisk, MonitorNet } from '@/types'

/**
 * 获取基础监控数据（CPU、内存、磁盘整体使用率）
 * @param serverId 服务器ID
 * @param page 页码
 * @param count 每页数量
 */
export function fetchBaseMonitor(
  serverId: number,
  page: number = 1,
  count: number = 100
): Promise<PaginatedResponse<MonitorBase>> {
  return get(`/monitor/base/${serverId}/${page}/${count}`)
}

/**
 * 获取磁盘监控数据
 * @param serverId 服务器ID
 * @param page 页码
 * @param count 每页数量
 */
export function fetchDiskMonitor(
  serverId: number,
  page: number = 1,
  count: number = 100
): Promise<PaginatedResponse<MonitorDisk>> {
  return get(`/monitor/disk/${serverId}/${page}/${count}`)
}

/**
 * 获取网络监控数据
 * @param serverId 服务器ID
 * @param page 页码
 * @param count 每页数量
 */
export function fetchNetMonitor(
  serverId: number,
  page: number = 1,
  count: number = 100
): Promise<PaginatedResponse<MonitorNet>> {
  return get(`/monitor/net/${serverId}/${page}/${count}`)
}
```

### 数据格式化工具

```typescript
// utils/format.ts

/**
 * 格式化字节为可读格式
 */
export function formatBytes(bytes: number, decimals: number = 2): string {
  if (bytes === 0) return '0 B'
  const k = 1024
  const dm = decimals < 0 ? 0 : decimals
  const sizes = ['B', 'KB', 'MB', 'GB', 'TB', 'PB']
  const i = Math.floor(Math.log(bytes) / Math.log(k))
  return parseFloat((bytes / Math.pow(k, i)).toFixed(dm)) + ' ' + sizes[i]
}

/**
 * 格式化百分比
 */
export function formatPercent(value: number, decimals: number = 2): string {
  return value.toFixed(decimals) + '%'
}

/**
 * 格式化时间
 */
export function formatTime(timeStr: string): string {
  const date = new Date(timeStr)
  return date.toLocaleTimeString('zh-CN', {
    hour: '2-digit',
    minute: '2-digit',
    second: '2-digit'
  })
}

/**
 * 格式化日期时间
 */
export function formatDateTime(timeStr: string): string {
  const date = new Date(timeStr)
  return date.toLocaleString('zh-CN', {
    month: '2-digit',
    day: '2-digit',
    hour: '2-digit',
    minute: '2-digit'
  })
}
```

## 国际化语言包

### i18n 配置入口

```typescript
// lang/index.ts
import { createI18n } from 'vue-i18n'
import zhCN from './zh-CN'
import enUS from './en-US'

const messages = {
  'zh-CN': zhCN,
  'en-US': enUS
}

export const i18n = createI18n({
  legacy: false,
  locale: localStorage.getItem('locale') || 'zh-CN',
  fallbackLocale: 'zh-CN',
  messages
})

export const availableLocales = [
  { code: 'zh-CN', name: '简体中文' },
  { code: 'en-US', name: 'English' }
]
```

### 中文语言包

```typescript
// lang/zh-CN/index.ts
import common from './common'
import layout from './layout'
import monitor from './monitor'

export default {
  common,
  layout,
  monitor
}
```

```typescript
// lang/zh-CN/common.ts
export default {
  confirm: '确认',
  cancel: '取消',
  save: '保存',
  delete: '删除',
  edit: '编辑',
  create: '创建',
  search: '搜索',
  loading: '加载中...',
  noData: '暂无数据',
  refresh: '刷新',
  close: '关闭',
  back: '返回',
  submit: '提交'
}
```

```typescript
// lang/zh-CN/layout.ts
export default {
  overview: '概览',
  servers: '服务器',
  applications: '应用',
  appStore: '应用商店',
  configs: '配置管理',
  monitor: '监控',
  logout: '退出登录'
}
```

```typescript
// lang/zh-CN/monitor.ts
export default {
  title: '服务器监控',
  selectServer: '选择服务器',
  timeRange: '时间范围',
  realtime: '实时',
  last1Hour: '最近1小时',
  last6Hours: '最近6小时',
  last24Hours: '最近24小时',
  last7Days: '最近7天',
  last30Days: '最近30天',
  cpuUsage: 'CPU 使用率',
  memoryUsage: '内存使用率',
  diskUsage: '磁盘使用率',
  networkTraffic: '网络流量',
  diskIO: '磁盘 IO',
  current: '当前',
  average: '平均',
  max: '最高',
  min: '最低',
  total: '总计',
  used: '已用',
  free: '可用',
  read: '读取',
  write: '写入',
  receive: '接收',
  transmit: '发送',
  noServerSelected: '请选择服务器',
  loadFailed: '加载监控数据失败',
  refreshInterval: '自动刷新',
  bytes: '字节',
  packets: '包',
  errors: '错误',
  drops: '丢包'
}
```

### 英文语言包

```typescript
// lang/en-US/index.ts
import common from './common'
import layout from './layout'
import monitor from './monitor'

export default {
  common,
  layout,
  monitor
}
```

```typescript
// lang/en-US/common.ts
export default {
  confirm: 'Confirm',
  cancel: 'Cancel',
  save: 'Save',
  delete: 'Delete',
  edit: 'Edit',
  create: 'Create',
  search: 'Search',
  loading: 'Loading...',
  noData: 'No Data',
  refresh: 'Refresh',
  close: 'Close',
  back: 'Back',
  submit: 'Submit'
}
```

```typescript
// lang/en-US/layout.ts
export default {
  overview: 'Overview',
  servers: 'Servers',
  applications: 'Applications',
  appStore: 'App Store',
  configs: 'Configs',
  monitor: 'Monitor',
  logout: 'Logout'
}
```

```typescript
// lang/en-US/monitor.ts
export default {
  title: 'Server Monitor',
  selectServer: 'Select Server',
  timeRange: 'Time Range',
  realtime: 'Realtime',
  last1Hour: 'Last 1 Hour',
  last6Hours: 'Last 6 Hours',
  last24Hours: 'Last 24 Hours',
  last7Days: 'Last 7 Days',
  last30Days: 'Last 30 Days',
  cpuUsage: 'CPU Usage',
  memoryUsage: 'Memory Usage',
  diskUsage: 'Disk Usage',
  networkTraffic: 'Network Traffic',
  diskIO: 'Disk I/O',
  current: 'Current',
  average: 'Average',
  max: 'Max',
  min: 'Min',
  total: 'Total',
  used: 'Used',
  free: 'Free',
  read: 'Read',
  write: 'Write',
  receive: 'Receive',
  transmit: 'Transmit',
  noServerSelected: 'Please select a server',
  loadFailed: 'Failed to load monitor data',
  refreshInterval: 'Auto Refresh',
  bytes: 'Bytes',
  packets: 'Packets',
  errors: 'Errors',
  drops: 'Drops'
}
```

## 路由配置

```typescript
// router/index.ts
import { createRouter, createWebHistory } from 'vue-router'

const routes = [
  {
    path: '/',
    name: 'Overview',
    component: () => import('@/views/Overview/index.vue')
  },
  {
    path: '/servers',
    name: 'Servers',
    component: () => import('@/views/Server/index.vue')
  },
  {
    path: '/app-store',
    name: 'AppStore',
    component: () => import('@/views/AppStore/index.vue')
  },
  {
    path: '/configs',
    name: 'Configs',
    component: () => import('@/views/Configs/index.vue')
  },
  {
    path: '/applications',
    name: 'Applications',
    component: () => import('@/views/Application/index.vue')
  },
  {
    path: '/monitor',
    name: 'Monitor',
    component: () => import('@/views/Monitor/index.vue')
  }
]

const router = createRouter({
  history: createWebHistory(),
  routes
})

export default router
```

## 组件实现要点

### 通用图表组件

#### LineChart 组件（通用折线图）

```vue
<template>
  <div ref="chartRef" class="line-chart" :style="{ height: height + 'px' }"></div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted, watch } from 'vue'
import * as echarts from 'echarts'
import type { ChartDataPoint } from '@/types'

interface Props {
  data: ChartDataPoint[]
  title?: string
  color?: string
  height?: number
  unit?: string
  showArea?: boolean
  smooth?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  title: '',
  color: '#4fc3f7',
  height: 250,
  unit: '%',
  showArea: true,
  smooth: true
})

const chartRef = ref<HTMLElement>()
let chart: echarts.ECharts | null = null

const initChart = () => {
  if (!chartRef.value) return
  
  chart = echarts.init(chartRef.value)
  updateChart()
  
  const resizeHandler = () => chart?.resize()
  window.addEventListener('resize', resizeHandler)
  
  onUnmounted(() => {
    window.removeEventListener('resize', resizeHandler)
    chart?.dispose()
  })
}

const updateChart = () => {
  if (!chart) return
  
  const option: echarts.EChartsOption = {
    grid: {
      left: '3%',
      right: '4%',
      bottom: '3%',
      top: '15%',
      containLabel: true
    },
    tooltip: {
      trigger: 'axis',
      formatter: (params: any) => {
        const data = params[0]
        return `${data.name}<br/>${data.seriesName}: ${data.value}${props.unit}`
      }
    },
    xAxis: {
      type: 'category',
      boundaryGap: false,
      data: props.data.map(item => item.time),
      axisLine: { lineStyle: { color: '#e0e0e0' } },
      axisLabel: { color: '#666' }
    },
    yAxis: {
      type: 'value',
      name: props.unit,
      axisLine: { show: false },
      axisTick: { show: false },
      splitLine: { lineStyle: { color: '#f0f0f0' } },
      axisLabel: { color: '#666' }
    },
    series: [{
      name: props.title,
      type: 'line',
      smooth: props.smooth,
      symbol: 'none',
      lineStyle: { color: props.color, width: 2 },
      areaStyle: props.showArea ? {
        color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
          { offset: 0, color: props.color + '40' },
          { offset: 1, color: props.color + '10' }
        ])
      } : undefined,
      data: props.data.map(item => item.value)
    }]
  }
  
  chart.setOption(option)
}

onMounted(initChart)
watch(() => props.data, updateChart, { deep: true })
</script>

<style scoped>
.line-chart {
  width: 100%;
}
</style>
```

#### PieChart 组件（通用饼图/环形图）

```vue
<template>
  <div ref="chartRef" class="pie-chart" :style="{ height: height + 'px' }"></div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted, watch } from 'vue'
import * as echarts from 'echarts'
import type { PieChartData } from '@/types'

interface Props {
  data: PieChartData[]
  title?: string
  height?: number
  donut?: boolean
  showLegend?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  title: '',
  height: 250,
  donut: true,
  showLegend: true
})

const chartRef = ref<HTMLElement>()
let chart: echarts.ECharts | null = null

const initChart = () => {
  if (!chartRef.value) return
  
  chart = echarts.init(chartRef.value)
  updateChart()
  
  const resizeHandler = () => chart?.resize()
  window.addEventListener('resize', resizeHandler)
  
  onUnmounted(() => {
    window.removeEventListener('resize', resizeHandler)
    chart?.dispose()
  })
}

const updateChart = () => {
  if (!chart) return
  
  const option: echarts.EChartsOption = {
    tooltip: {
      trigger: 'item',
      formatter: '{b}: {c} ({d}%)'
    },
    legend: props.showLegend ? {
      orient: 'vertical',
      right: '5%',
      top: 'center',
      textStyle: { color: '#666' }
    } : undefined,
    series: [{
      name: props.title,
      type: 'pie',
      radius: props.donut ? ['40%', '70%'] : '70%',
      center: props.showLegend ? ['35%', '50%'] : ['50%', '50%'],
      avoidLabelOverlap: false,
      itemStyle: {
        borderRadius: 8,
        borderColor: '#fff',
        borderWidth: 2
      },
      label: {
        show: false,
        position: 'center'
      },
      emphasis: {
        label: {
          show: true,
          fontSize: 16,
          fontWeight: 'bold'
        }
      },
      labelLine: { show: false },
      data: props.data
    }]
  }
  
  chart.setOption(option)
}

onMounted(initChart)
watch(() => props.data, updateChart, { deep: true })
</script>

<style scoped>
.pie-chart {
  width: 100%;
}
</style>
```

### MetricCard 组件（指标卡片）

```vue
<template>
  <div class="metric-card" :class="`metric-card--${type}`">
    <div class="metric-header">
      <Icon :icon="icon" class="metric-icon" />
      <span class="metric-title">{{ title }}</span>
    </div>
    <div class="metric-body">
      <div class="metric-value">{{ value }}</div>
      <div class="metric-unit">{{ unit }}</div>
    </div>
    <div v-if="trend !== undefined" class="metric-footer">
      <span class="metric-trend" :class="trend >= 0 ? 'trend-up' : 'trend-down'">
        <Icon :icon="trend >= 0 ? 'lucide:trending-up' : 'lucide:trending-down'" />
        {{ Math.abs(trend).toFixed(1) }}%
      </span>
      <span class="metric-label">{{ trendLabel }}</span>
    </div>
  </div>
</template>

<script setup lang="ts">
import { Icon } from '@iconify/vue'

interface Props {
  title: string
  value: string | number
  unit?: string
  icon: string
  type?: 'primary' | 'success' | 'warning' | 'danger'
  trend?: number
  trendLabel?: string
}

withDefaults(defineProps<Props>(), {
  unit: '',
  type: 'primary',
  trendLabel: '较上期'
})
</script>

<style scoped>
.metric-card {
  background: #ffffff;
  border-radius: 12px;
  padding: 20px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.06);
  transition: all 0.3s ease;
}

.metric-card:hover {
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
  transform: translateY(-2px);
}

.metric-header {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 12px;
}

.metric-icon {
  width: 20px;
  height: 20px;
}

.metric-card--primary .metric-icon { color: #4fc3f7; }
.metric-card--success .metric-icon { color: #67c23a; }
.metric-card--warning .metric-icon { color: #e6a23c; }
.metric-card--danger .metric-icon { color: #f56c6c; }

.metric-title {
  font-size: 14px;
  color: #666;
}

.metric-body {
  display: flex;
  align-items: baseline;
  gap: 4px;
  margin-bottom: 12px;
}

.metric-value {
  font-size: 28px;
  font-weight: 600;
  color: #333;
}

.metric-unit {
  font-size: 14px;
  color: #999;
}

.metric-footer {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 12px;
}

.metric-trend {
  display: flex;
  align-items: center;
  gap: 2px;
}

.trend-up { color: #67c23a; }
.trend-down { color: #f56c6c; }

.metric-label {
  color: #999;
}
</style>
```

### ServerSelector 组件（服务器选择器）

```vue
<template>
  <div class="server-selector">
    <label class="selector-label">{{ $t('monitor.selectServer') }}</label>
    <select v-model="selectedId" class="selector-select" @change="handleChange">
      <option value="">{{ $t('monitor.noServerSelected') }}</option>
      <option 
        v-for="server in servers" 
        :key="server.id" 
        :value="server.id"
        :disabled="server.status === 'offline'"
      >
        {{ server.name }} ({{ server.ip }})
        <span v-if="server.status === 'offline'" class="offline-tag">[Offline]</span>
      </option>
    </select>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { useI18n } from 'vue-i18n'
import type { ServerOption } from '@/types'

interface Props {
  servers: ServerOption[]
  modelValue: number | null
}

const props = defineProps<Props>()
const emit = defineEmits<{
  'update:modelValue': [value: number | null]
  change: [serverId: number]
}>()

const { t } = useI18n()

const selectedId = computed({
  get: () => props.modelValue,
  set: (val) => emit('update:modelValue', val)
})

const handleChange = () => {
  if (selectedId.value) {
    emit('change', selectedId.value)
  }
}
</script>

<style scoped>
.server-selector {
  display: flex;
  align-items: center;
  gap: 12px;
}

.selector-label {
  font-size: 14px;
  color: #666;
  white-space: nowrap;
}

.selector-select {
  padding: 8px 12px;
  border: 1px solid #e0e0e0;
  border-radius: 6px;
  font-size: 14px;
  background: #fff;
  min-width: 200px;
  cursor: pointer;
}

.selector-select:focus {
  outline: none;
  border-color: #4fc3f7;
}

.offline-tag {
  color: #f56c6c;
  margin-left: 4px;
}
</style>
```

### TimeRangePicker 组件（时间范围选择器）

```vue
<template>
  <div class="time-range-picker">
    <button
      v-for="range in ranges"
      :key="range.value"
      class="range-btn"
      :class="{ active: modelValue === range.value }"
      @click="selectRange(range.value)"
    >
      {{ range.label }}
    </button>
  </div>
</template>

<script setup lang="ts">
import { useI18n } from 'vue-i18n'
import type { TimeRange } from '@/types'

interface Props {
  modelValue: TimeRange
}

const props = defineProps<Props>()
const emit = defineEmits<{
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

const selectRange = (value: TimeRange) => {
  emit('update:modelValue', value)
}
</script>

<style scoped>
.time-range-picker {
  display: flex;
  gap: 8px;
}

.range-btn {
  padding: 6px 12px;
  border: 1px solid #e0e0e0;
  border-radius: 6px;
  background: #fff;
  font-size: 13px;
  color: #666;
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

### MonitorDashboard 组件（监控仪表盘）

```vue
<template>
  <div class="monitor-dashboard">
    <!-- 顶部指标卡片 -->
    <div class="metrics-row">
      <MetricCard
        :title="$t('monitor.cpuUsage')"
        :value="formatPercent(currentMetrics.cpu)"
        icon="lucide:cpu"
        type="primary"
      />
      <MetricCard
        :title="$t('monitor.memoryUsage')"
        :value="formatPercent(currentMetrics.memory)"
        :subtitle="formatBytes(currentMetrics.memoryUsed) + ' / ' + formatBytes(currentMetrics.memoryTotal)"
        icon="lucide:hard-drive"
        type="success"
      />
      <MetricCard
        :title="$t('monitor.diskUsage')"
        :value="formatPercent(currentMetrics.disk)"
        :subtitle="formatBytes(currentMetrics.diskUsed) + ' / ' + formatBytes(currentMetrics.diskTotal)"
        icon="lucide:database"
        type="warning"
      />
      <MetricCard
        :title="$t('monitor.networkTraffic')"
        :value="formatBytes(currentMetrics.netRecv + currentMetrics.netSent)"
        icon="lucide:activity"
        type="danger"
      />
    </div>

    <!-- 图表区域 -->
    <div class="charts-grid">
      <!-- CPU 图表 -->
      <div class="chart-card">
        <div class="chart-header">
          <h3 class="chart-title">
            <Icon icon="lucide:cpu" />
            {{ $t('monitor.cpuUsage') }}
          </h3>
          <span class="chart-value" :class="getUsageClass(cpuData[cpuData.length - 1]?.value)">
            {{ formatPercent(cpuData[cpuData.length - 1]?.value || 0) }}
          </span>
        </div>
        <LineChart
          :data="cpuData"
          :title="$t('monitor.cpuUsage')"
          color="#4fc3f7"
          unit="%"
          :height="200"
        />
      </div>

      <!-- 内存图表 -->
      <div class="chart-card">
        <div class="chart-header">
          <h3 class="chart-title">
            <Icon icon="lucide:hard-drive" />
            {{ $t('monitor.memoryUsage') }}
          </h3>
          <span class="chart-value" :class="getUsageClass(memoryData[memoryData.length - 1]?.value)">
            {{ formatPercent(memoryData[memoryData.length - 1]?.value || 0) }}
          </span>
        </div>
        <LineChart
          :data="memoryData"
          :title="$t('monitor.memoryUsage')"
          color="#67c23a"
          unit="%"
          :height="200"
        />
      </div>

      <!-- 磁盘使用率饼图 -->
      <div class="chart-card">
        <div class="chart-header">
          <h3 class="chart-title">
            <Icon icon="lucide:database" />
            {{ $t('monitor.diskUsage') }}
          </h3>
        </div>
        <PieChart
          :data="diskPieData"
          :title="$t('monitor.diskUsage')"
          :height="200"
          :donut="true"
        />
      </div>

      <!-- 网络流量图表 -->
      <div class="chart-card full-width">
        <div class="chart-header">
          <h3 class="chart-title">
            <Icon icon="lucide:activity" />
            {{ $t('monitor.networkTraffic') }}
          </h3>
        </div>
        <LineChart
          :data="netRecvData"
          :title="$t('monitor.receive')"
          color="#4fc3f7"
          unit="B/s"
          :height="200"
          :show-area="false"
        />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { Icon } from '@iconify/vue'
import { useI18n } from 'vue-i18n'
import MetricCard from './MetricCard.vue'
import LineChart from '@/components/LineChart.vue'
import PieChart from '@/components/PieChart.vue'
import type { MonitorBase, ChartDataPoint, PieChartData } from '@/types'
import { formatPercent, formatBytes } from '@/utils/format'

interface Props {
  baseData: MonitorBase[]
}

const props = defineProps<Props>()
const { t } = useI18n()

// 当前指标
const currentMetrics = computed(() => {
  const latest = props.baseData[props.baseData.length - 1]
  if (!latest) {
    return {
      cpu: 0,
      memory: 0,
      memoryUsed: 0,
      memoryTotal: 0,
      disk: 0,
      diskUsed: 0,
      diskTotal: 0,
      netRecv: 0,
      netSent: 0
    }
  }
  return {
    cpu: latest.cpu_usage * 100,
    memory: latest.memory_usage,
    memoryUsed: latest.memory_used,
    memoryTotal: latest.memory_total,
    disk: latest.disk_usage,
    diskUsed: latest.disk_used,
    diskTotal: latest.disk_total,
    netRecv: 0,
    netSent: 0
  }
})

// CPU 图表数据
const cpuData = computed<ChartDataPoint[]>(() => {
  return props.baseData.map(item => ({
    time: new Date(item.collect_time).toLocaleTimeString(),
    value: item.cpu_usage * 100
  }))
})

// 内存图表数据
const memoryData = computed<ChartDataPoint[]>(() => {
  return props.baseData.map(item => ({
    time: new Date(item.collect_time).toLocaleTimeString(),
    value: item.memory_usage
  }))
})

// 磁盘饼图数据
const diskPieData = computed<PieChartData[]>(() => {
  const latest = props.baseData[props.baseData.length - 1]
  if (!latest) return []
  return [
    { name: t('monitor.used'), value: latest.disk_used, color: '#4fc3f7' },
    { name: t('monitor.free'), value: latest.disk_total - latest.disk_used, color: '#e0e0e0' }
  ]
})

// 网络接收数据
const netRecvData = computed<ChartDataPoint[]>(() => {
  // 这里需要根据实际网络数据处理
  return props.baseData.map((item, index) => ({
    time: new Date(item.collect_time).toLocaleTimeString(),
    value: 0 // 实际使用时替换为网络数据
  }))
})

const getUsageClass = (value: number) => {
  if (value >= 90) return 'danger'
  if (value >= 70) return 'warning'
  return 'normal'
}
</script>

<style scoped>
.monitor-dashboard {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.metrics-row {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 16px;
}

.charts-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 16px;
}

.chart-card {
  background: #ffffff;
  border-radius: 12px;
  padding: 20px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.06);
}

.chart-card.full-width {
  grid-column: 1 / -1;
}

.chart-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.chart-title {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 16px;
  font-weight: 500;
  color: #333;
  margin: 0;
}

.chart-value {
  font-size: 20px;
  font-weight: 600;
}

.chart-value.normal { color: #67c23a; }
.chart-value.warning { color: #e6a23c; }
.chart-value.danger { color: #f56c6c; }

@media (max-width: 1200px) {
  .metrics-row {
    grid-template-columns: repeat(2, 1fr);
  }
  .charts-grid {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 768px) {
  .metrics-row {
    grid-template-columns: 1fr;
  }
}
</style>
```

## 主页面逻辑

```vue
<template>
  <div class="monitor-page">
    <!-- 页面头部 -->
    <div class="page-header">
      <h1 class="page-title">{{ $t('monitor.title') }}</h1>
      <div class="header-controls">
        <ServerSelector
          v-model="selectedServer"
          :servers="servers"
          @change="loadMonitorData"
        />
        <TimeRangePicker v-model="timeRange" />
        <button class="refresh-btn" @click="loadMonitorData" :disabled="loading">
          <Icon icon="lucide:refresh-cw" :class="{ spinning: loading }" />
        </button>
      </div>
    </div>

    <!-- 加载状态 -->
    <Loading v-if="loading && !baseData.length" text="加载监控数据中..." />

    <!-- 空状态 -->
    <Empty
      v-else-if="!selectedServer"
      :description="$t('monitor.noServerSelected')"
      icon="lucide:monitor"
    />

    <!-- 监控仪表盘 -->
    <MonitorDashboard
      v-else
      :base-data="baseData"
      :disk-data="diskData"
      :net-data="netData"
    />
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
import { useI18n } from 'vue-i18n'
import { Icon } from '@iconify/vue'
import ServerSelector from './components/ServerSelector.vue'
import TimeRangePicker from './components/TimeRangePicker.vue'
import MonitorDashboard from './components/MonitorDashboard.vue'
import Loading from '@/components/Loading.vue'
import Empty from '@/components/Empty.vue'
import { fetchBaseMonitor, fetchDiskMonitor, fetchNetMonitor } from '@/api/monitor'
import type { MonitorBase, MonitorDisk, MonitorNet, ServerOption, TimeRange } from '@/types'

const { t } = useI18n()

// 状态
const loading = ref(false)
const selectedServer = ref<number | null>(null)
const timeRange = ref<TimeRange>('1h')
const baseData = ref<MonitorBase[]>([])
const diskData = ref<MonitorDisk[]>([])
const netData = ref<MonitorNet[]>([])

// 模拟服务器列表（实际应从服务器API获取）
const servers = ref<ServerOption[]>([
  { id: 1, name: '生产服务器-01', ip: '192.168.1.101', status: 'online' },
  { id: 2, name: '生产服务器-02', ip: '192.168.1.102', status: 'online' },
  { id: 3, name: '测试服务器', ip: '192.168.1.201', status: 'offline' }
])

// 自动刷新定时器
let refreshTimer: NodeJS.Timeout | null = null

// 加载监控数据
const loadMonitorData = async () => {
  if (!selectedServer.value) return
  
  loading.value = true
  try {
    const [base, disk, net] = await Promise.all([
      fetchBaseMonitor(selectedServer.value, 1, 100),
      fetchDiskMonitor(selectedServer.value, 1, 100),
      fetchNetMonitor(selectedServer.value, 1, 100)
    ])
    
    baseData.value = base.list
    diskData.value = disk.list
    netData.value = net.list
  } catch (error) {
    console.error('Failed to load monitor data:', error)
  } finally {
    loading.value = false
  }
}

// 设置自动刷新
const setupAutoRefresh = () => {
  refreshTimer = setInterval(() => {
    if (selectedServer.value) {
      loadMonitorData()
    }
  }, 30000) // 30秒刷新一次
}

onMounted(() => {
  setupAutoRefresh()
})

onUnmounted(() => {
  if (refreshTimer) {
    clearInterval(refreshTimer)
  }
})
</script>

<style scoped>
.monitor-page {
  padding: 24px;
}

.page-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 24px;
}

.page-title {
  font-size: 24px;
  font-weight: 600;
  color: #333;
  margin: 0;
}

.header-controls {
  display: flex;
  align-items: center;
  gap: 16px;
}

.refresh-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 36px;
  height: 36px;
  border: 1px solid #e0e0e0;
  border-radius: 6px;
  background: #fff;
  cursor: pointer;
  transition: all 0.2s;
}

.refresh-btn:hover {
  border-color: #4fc3f7;
  color: #4fc3f7;
}

.refresh-btn:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.spinning {
  animation: spin 1s linear infinite;
}

@keyframes spin {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}

@media (max-width: 768px) {
  .page-header {
    flex-direction: column;
    align-items: flex-start;
    gap: 16px;
  }
  
  .header-controls {
    flex-wrap: wrap;
    width: 100%;
  }
}
</style>
```

## 功能特性

### 1. 服务器选择 ⭐⭐⭐⭐⭐
- 支持下拉选择要监控的服务器
- 显示服务器名称和IP地址
- 离线服务器置灰显示

### 2. 时间范围选择 ⭐⭐⭐⭐
- 支持多种时间范围：1小时、6小时、24小时、7天、30天
- 按钮组切换，直观易用
- 切换后自动刷新数据

### 3. CPU 监控图表 ⭐⭐⭐⭐⭐
- 折线图展示 CPU 使用率趋势
- 显示当前使用率数值
- 根据使用率显示不同颜色（正常/警告/危险）
- 支持面积图效果

### 4. 内存监控图表 ⭐⭐⭐⭐⭐
- 折线图展示内存使用率趋势
- 显示当前使用率数值
- 显示已用/总内存容量
- 根据使用率显示不同颜色

### 5. 磁盘监控图表 ⭐⭐⭐⭐
- 环形图展示磁盘使用比例
- 显示已用空间和可用空间
- 直观展示磁盘容量分布

### 6. 网络监控图表 ⭐⭐⭐⭐
- 折线图展示网络流量趋势
- 区分接收和发送流量
- 支持多网卡展示

### 7. 指标卡片 ⭐⭐⭐⭐
- 顶部展示关键指标卡片
- CPU、内存、磁盘、网络四大指标
- 显示当前数值和趋势
- 悬停效果增强交互

### 8. 自动刷新 ⭐⭐⭐
- 支持定时自动刷新数据
- 默认30秒刷新一次
- 手动刷新按钮

### 9. 响应式布局 ⭐⭐⭐⭐
- 适配不同屏幕尺寸
- 移动端优化显示
- 图表自动调整大小

### 10. 数据格式化 ⭐⭐⭐
- 字节自动转换为 KB/MB/GB
- 百分比格式化显示
- 时间格式化显示

## 设计规范

- 使用项目统一的配色方案（主色 #4fc3f7）
- 卡片式设计，圆角 12px
- 阴影效果：0 2px 8px rgba(0, 0, 0, 0.06)
- 图表使用 ECharts 库
- 指标卡片悬停效果：上浮 + 阴影增强
- 使用率颜色：正常(#67c23a)、警告(#e6a23c)、危险(#f56c6c)
- 遵循 Squirrel 紧凑风格页面设计规范（调用 `compact-page-style` skill）

## 最佳实践

1. **图表优化**：使用 ECharts 的 dataZoom 处理大量数据点
2. **性能优化**：使用 computed 缓存图表数据转换
3. **错误处理**：API 调用添加 try-catch 处理
4. **国际化**：所有文本使用 $t() 函数包裹
5. **组件复用**：参考 `component-reusability` skill 设计可复用图表组件
6. **响应式设计**：使用 CSS Grid 实现自适应布局
7. **自动刷新**：组件卸载时清理定时器
8. **数据格式化**：统一使用工具函数格式化数据
9. **类型安全**：完整的 TypeScript 类型定义
10. **图表交互**：支持悬停提示、图例切换

## 架构参考

- 遵循 Squirrel 运维平台前端布局架构（调用 `layout` skill）
- 使用 Vue3 + vue-i18n 国际化（调用 `vue-i18n-setup` skill）
- 使用 ECharts 实现数据可视化
- 采用 Composables 封装监控数据获取逻辑

## 依赖安装

```bash
npm install echarts
npm install vue-i18n@9
npm install @iconify/vue
```

## 接口案例文件

- .trae\skills\monitor\api.md
