# 组件实现

## 目录

- [CPUMonitorChart 组件](#cpumonitorchart-组件)
- [MemoryMonitorChart 组件](#memorymonitorchart-组件)
- [DiskIOChart 组件](#diskiochart-组件)
- [NetIOChart 组件](#netiochart-组件)
- [TimeRangeSelector 组件](#timerangeselector-组件)
- [主页面实现](#主页面实现)

## CPUMonitorChart 组件

**职责**：展示 CPU 使用率历史趋势

**模板结构**：
```vue
<template>
  <div class="chart-card">
    <div class="chart-header">
      <h3>标题 + 图标</h3>
      <span class="chart-value">当前值（带颜色状态）</span>
    </div>
    <div class="chart-container" ref="chartContainer"></div>
  </div>
</template>
```

**关键逻辑**：
```typescript
// Props
defineProps<{ data: BaseMonitorRecord[] }>()

// 计算最新值
const latestValue = computed(() => data[data.length - 1].cpu_usage * 100)

// 状态颜色判断
const getUsageClass = (value) => 
  value >= 90 ? 'danger' : value >= 70 ? 'warning' : 'normal'

// ECharts 配置要点
const chartOption = {
  xAxis: { type: 'category', data: 时间数组 },
  yAxis: { min: 0, max: 100 },
  series: [{
    type: 'line',
    smooth: true,
    areaStyle: { 渐变填充 }
  }]
}
```

## MemoryMonitorChart 组件

**职责**：展示内存使用率历史趋势

**与 CPU 组件差异**：
- 增加 `chart-info` 显示 `已用/总量`
- Y 轴数据为 `memory_usage`（百分比）
- 颜色使用绿色系

**模板结构**：
```vue
<template>
  <div class="chart-card">
    <div class="chart-header">...</div>
    <div class="chart-info">{{ formatBytes(used) }} / {{ formatBytes(total) }}</div>
    <div class="chart-container"></div>
  </div>
</template>
```

## DiskIOChart 组件

**职责**：展示磁盘 IO 读写趋势，支持设备筛选

**模板结构**：
```vue
<template>
  <div class="chart-card">
    <div class="chart-header">
      <h3>标题</h3>
      <select v-model="selectedDevice">
        <option value="all">全部</option>
        <option v-for="device in devices">{{ device }}</option>
      </select>
    </div>
    <div class="chart-stats">
      <div>读取: {{ formatBytes(latestReadBytes) }}</div>
      <div>写入: {{ formatBytes(latestWriteBytes) }}</div>
    </div>
    <div class="chart-container"></div>
  </div>
</template>
```

**关键逻辑**：
```typescript
// 设备筛选
const filteredData = computed(() => {
  if (selectedDevice === 'all') {
    // 按时间分组聚合所有设备数据
    return groupByTimeAndSum(data)
  }
  return data.filter(d => d.disk_name === selectedDevice)
})

// ECharts 配置 - 双线图
const series = [
  { name: '读取', data: readValues },
  { name: '写入', data: writeValues }
]
```

## NetIOChart 组件

**职责**：展示网络流量趋势，支持网卡筛选

**与 DiskIOChart 结构相同**，差异：
- 筛选字段：`interface_name`
- 数据字段：`bytes_sent`（上传）、`bytes_recv`（下载）
- 图例：上传/下载

## TimeRangeSelector 组件

**职责**：时间范围切换

**模板结构**：
```vue
<template>
  <div class="time-range-selector">
    <button v-for="range in ranges"
      :class="{ active: modelValue === range.value }"
      @click="$emit('update:modelValue', range.value)">
      {{ range.label }}
    </button>
  </div>
</template>
```

**选项**：`1h` | `6h` | `24h` | `7d` | `30d`

## 主页面实现

**模板结构**：
```vue
<template>
  <div class="monitor-page">
    <div class="page-header">
      <h1>标题</h1>
      <ServerSelector v-model="selectedServer" />
    </div>

    <Loading v-if="loading" />
    
    <template v-else-if="selectedServer">
      <div class="charts-grid">
        <CPUMonitorChart :data="baseData" />
        <MemoryMonitorChart :data="baseData" />
        <DiskIOChart :data="diskData" :devices="diskDevices" />
        <NetIOChart :data="netData" :interfaces="netInterfaces" />
      </div>
      <TimeRangeSelector v-model="timeRange" />
    </template>

    <Empty v-else />
  </div>
</template>
```

**关键逻辑**：
```typescript
// 数据加载
const loadMonitorData = async () => {
  const [base, disk, net] = await Promise.all([
    fetchBaseMonitorHistory(serverId, page, count),
    fetchDiskIOHistory(serverId, page, count),
    fetchNetIOHistory(serverId, page, count)
  ])
  
  baseData = base.list
  diskData = disk.list
  netData = net.list
  
  // 提取设备列表
  diskDevices = [...new Set(disk.list.map(d => d.disk_name))]
  netInterfaces = [...new Set(net.list.map(d => d.interface_name))]
}

// 响应式更新
watch([selectedServer, timeRange], loadMonitorData)
```

**布局样式**：
```css
.charts-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 16px;
}

@media (max-width: 1200px) {
  .charts-grid { grid-template-columns: 1fr; }
}
```
