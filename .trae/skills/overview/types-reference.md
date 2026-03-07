# 类型定义参考

本文档提供概览页面模块的所有 TypeScript 类型定义。

## 目录

- [监控数据类型](#监控数据类型)
- [进程信息类型](#进程信息类型)
- [服务器信息类型](#服务器信息类型)
- [API 函数类型](#api-函数类型)

## 监控数据类型

### MonitorStats

```typescript
export interface MonitorStats {
  cpu: CPUInfo
  disk: DiskInfo
  hostname: string
  loadAverage: LoadAverage
  memory: MemoryInfo
  timestamp: string
  topCPU: ProcessInfo[]
  topMemory: ProcessInfo[]
}
```

### CPUInfo

```typescript
export interface CPUInfo {
  cores: number
  frequency: number
  model: string
  perCoreUsage: number[]
  usage: number
}
```

### DiskInfo

```typescript
export interface DiskInfo {
  available: number
  partitions: DiskPartition[]
  total: number
  usage: number
  used: number
}
```

### DiskPartition

```typescript
export interface DiskPartition {
  available: number
  device: string
  fsType: string
  mountPoint: string
  total: number
  usage: number
  used: number
}
```

### MemoryInfo

```typescript
export interface MemoryInfo {
  available: number
  swapTotal: number
  swapUsed: number
  total: number
  usage: number
  used: number
}
```

### LoadAverage

```typescript
export interface LoadAverage {
  load1: number
  load15: number
  load5: number
}
```

## 进程信息类型

### ProcessInfo

```typescript
export interface ProcessInfo {
  cpuPercent: number
  createTime: number
  memoryMB: number
  memoryPercent: number
  name: string
  pid: number
  status: string
}
```

## 服务器信息类型

### ServerInfo

```typescript
export interface ServerInfo {
  architecture: string
  hostname: string
  ipAddresses: IpAddress[]
  kernelVersion: string
  os: string
  platform: string
  platformVersion: string
  uptime: number
  uptimeStr: string
}
```

### IpAddress

```typescript
export interface IpAddress {
  ipv4: string[]
  ipv6: string[]
  name: string
}
```

## 网络 IO 类型

### NetworkIO

```typescript
export interface NetworkIO {
  bytesRecv: number
  bytesSent: number
  dropin: number
  dropout: number
  errin: number
  errout: number
  name: string
  packetsRecv: number
  packetsSent: number
}
```

### NetworkIOAll

```typescript
export interface NetworkIOAll {
  data: NetworkIO
  ifnames: string[]
}
```

## 磁盘 IO 类型

### DiskIO

```typescript
export interface DiskIO {
  device: string
  readBytes: number
  readCount: number
  readTime: number
  writeBytes: number
  writeCount: number
  writeTime: number
}
```

### DiskIOAll

```typescript
export interface DiskIOAll {
  data: DiskIO
  devices: string[]
}
```

## 历史数据类型

### BaseMonitorData

```typescript
export interface BaseMonitorData {
  collect_time: string
  cpu_usage: number
  disk_total: number
  disk_usage: number
  disk_used: number
  id: number
  memory_total: number
  memory_usage: number
  memory_used: number
}
```

### DiskMonitorData

```typescript
export interface DiskMonitorData {
  collect_time: string
  disk_name: string
  id: number
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
```

### NetMonitorData

```typescript
export interface NetMonitorData {
  bytes_recv: number
  bytes_sent: number
  collect_time: string
  drop_in: number
  drop_out: number
  err_in: number
  err_out: number
  fifo_in: number
  fifo_out: number
  id: number
  interface_name: string
  packets_recv: number
  packets_sent: number
}
```

## API 函数类型

### API 函数签名

```typescript
export function fetchMonitorStats(serverId: number): Promise<MonitorStats>

export function fetchDiskIO(serverId: number, diskName: string): Promise<DiskIO>

export function fetchDiskIOAll(serverId: number): Promise<DiskIOAll>

export function fetchNetworkIO(serverId: number, interfaceName: string): Promise<NetworkIO>

export function fetchNetworkIOAll(serverId: number): Promise<NetworkIOAll>

export function fetchBaseMonitorData(serverId: number, page: number, count: number): Promise<{ list: BaseMonitorData[], total: number }>

export function fetchDiskMonitorData(serverId: number, page: number, count: number): Promise<{ list: DiskMonitorData[], total: number }>

export function fetchNetMonitorData(serverId: number, page: number, count: number): Promise<{ list: NetMonitorData[], total: number }>
```

## 使用示例

### 获取监控数据

```typescript
const monitorStats = ref<MonitorStats | null>(null)

const loadMonitorStats = async (serverId: number) => {
  try {
    monitorStats.value = await fetchMonitorStats(serverId)
  } catch (error) {
    console.error('Failed to load monitor stats:', error)
  }
}
```

### 获取进程信息

```typescript
const topCPU = computed(() => {
  return monitorStats.value?.topCPU || []
})

const topMemory = computed(() => {
  return monitorStats.value?.topMemory || []
})
```

### 格式化数据

```typescript
const formatBytes = (bytes: number): string => {
  const gb = bytes / (1024 * 1024 * 1024)
  return `${gb.toFixed(2)} GB`
}

const formatCPU = (usage: number, cores: number): string => {
  const usedCores = usage * cores
  return `(${usedCores.toFixed(2)} / ${cores}) 核`
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
```
