# 类型定义

## 目录

- [数据模型](#数据模型)
- [TypeScript 类型](#typescript-类型)

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

### DiskUsageMonitor - 磁盘使用量监控

| 字段 | 类型 | 说明 |
|------|------|------|
| id | uint | 主键 |
| device_name | string | 设备名称 |
| mount_point | string | 挂载点路径 |
| fs_type | string | 文件系统类型 |
| total | uint64 | 磁盘总量 (bytes) |
| used | uint64 | 磁盘已用量 (bytes) |
| free | uint64 | 磁盘剩余量 (bytes) |
| usage | float64 | 磁盘使用率 (%) |
| inodes_total | uint64 | inode 总数 |
| inodes_used | uint64 | inode 已用量 |
| inodes_free | uint64 | inode 剩余量 |
| collect_time | time | 采集时间 |

### DiskIOMonitor - 磁盘 IO 监控

> ⚠️ **重要说明**：以下字段存储的是**系统启动以来的累计值**，不是瞬时速率。前端绘制趋势图时需要计算相邻数据点的差值除以时间间隔，得到速率（bytes/s）。

| 字段 | 类型 | 说明 |
|------|------|------|
| id | uint | 主键 |
| disk_name | string | 磁盘设备名 (sda, sdb...) |
| read_count | uint64 | 累计读取次数 |
| write_count | uint64 | 累计写入次数 |
| read_bytes | uint64 | 累计读取字节数（系统启动以来） |
| write_bytes | uint64 | 累计写入字节数（系统启动以来） |
| read_time | uint64 | 累计读取时间 (ms) |
| write_time | uint64 | 累计写入时间 (ms) |
| io_time | uint64 | IO时间 |
| weighted_io_time | uint64 | 加权IO时间 |
| iops_in_progress | uint64 | 进行中的IOPS |
| collect_time | time | 采集时间 |

**速率计算公式**：
```
读取速率 (bytes/s) = (当前 read_bytes - 上次 read_bytes) / (当前 collect_time - 上次 collect_time)
写入速率 (bytes/s) = (当前 write_bytes - 上次 write_bytes) / (当前 collect_time - 上次 collect_time)
```

### NetworkMonitor - 网络监控

> ⚠️ **重要说明**：以下字段存储的是**系统启动以来的累计值**，不是瞬时速率。前端绘制趋势图时需要计算相邻数据点的差值除以时间间隔，得到速率（bytes/s）。

| 字段 | 类型 | 说明 |
|------|------|------|
| id | uint | 主键 |
| interface_name | string | 网卡名 (eth0, eth1...) |
| bytes_sent | uint64 | 累计发送字节数（系统启动以来） |
| bytes_recv | uint64 | 累计接收字节数（系统启动以来） |
| packets_sent | uint64 | 累计发送包数 |
| packets_recv | uint64 | 累计接收包数 |
| err_in | uint64 | 累计接收错误数 |
| err_out | uint64 | 累计发送错误数 |
| drop_in | uint64 | 累计接收丢包数 |
| drop_out | uint64 | 累计发送丢包数 |
| fifo_in | uint64 | FIFO接收 |
| fifo_out | uint64 | FIFO发送 |
| collect_time | time | 采集时间 |

**速率计算公式**：
```
上传速率 (bytes/s) = (当前 bytes_sent - 上次 bytes_sent) / (当前 collect_time - 上次 collect_time)
下载速率 (bytes/s) = (当前 bytes_recv - 上次 bytes_recv) / (当前 collect_time - 上次 collect_time)
```

## TypeScript 类型

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

export interface DiskUsageRecord {
  id: number
  device_name: string
  mount_point: string
  fs_type: string
  total: number
  used: number
  free: number
  usage: number
  inodes_total: number
  inodes_used: number
  inodes_free: number
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

export type TimeRange = '1h' | '6h' | '24h' | '7d' 

export interface ChartDataPoint {
  time: string
  value1: number
  value2?: number
}

export interface DiskIOSpeedRecord {
  collect_time: string
  read_speed: number
  write_speed: number
}

export interface NetworkIOSpeedRecord {
  collect_time: string
  upload_speed: number
  download_speed: number
}

export interface DeviceList {
  disks: string[]
  interfaces: string[]
}
```
