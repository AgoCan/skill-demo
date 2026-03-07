# 工具函数

## 目录

- [格式化函数](#格式化函数)
- [IO 速率计算函数](#io-速率计算函数)

## 格式化函数

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

## IO 速率计算函数

```typescript
// utils/monitor.ts

import type { DiskIORecord, NetworkIORecord } from '@/types/monitor'

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

/**
 * 将磁盘IO累计值转换为速率
 * @param records 按时间正序排列的历史记录（旧→新）
 * @returns 速率记录数组
 */
export function calculateDiskIOSpeed(records: DiskIORecord[]): DiskIOSpeedRecord[] {
  if (records.length < 2) return []
  
  const result: DiskIOSpeedRecord[] = []
  
  for (let i = 1; i < records.length; i++) {
    const prev = records[i - 1]
    const curr = records[i]
    
    const timeDiff = (new Date(curr.collect_time).getTime() - new Date(prev.collect_time).getTime()) / 1000
    
    if (timeDiff <= 0) continue
    
    result.push({
      collect_time: curr.collect_time,
      read_speed: Math.max(0, (curr.read_bytes - prev.read_bytes) / timeDiff),
      write_speed: Math.max(0, (curr.write_bytes - prev.write_bytes) / timeDiff),
    })
  }
  
  return result
}

/**
 * 将网络IO累计值转换为速率
 * @param records 按时间正序排列的历史记录（旧→新）
 * @returns 速率记录数组
 */
export function calculateNetworkIOSpeed(records: NetworkIORecord[]): NetworkIOSpeedRecord[] {
  if (records.length < 2) return []
  
  const result: NetworkIOSpeedRecord[] = []
  
  for (let i = 1; i < records.length; i++) {
    const prev = records[i - 1]
    const curr = records[i]
    
    const timeDiff = (new Date(curr.collect_time).getTime() - new Date(prev.collect_time).getTime()) / 1000
    
    if (timeDiff <= 0) continue
    
    result.push({
      collect_time: curr.collect_time,
      upload_speed: Math.max(0, (curr.bytes_sent - prev.bytes_sent) / timeDiff),
      download_speed: Math.max(0, (curr.bytes_recv - prev.bytes_recv) / timeDiff),
    })
  }
  
  return result
}

/**
 * 按设备分组并计算速率
 */
export function groupAndCalculateSpeed<T extends { disk_name?: string; interface_name?: string }>(
  records: T[],
  deviceKey: 'disk_name' | 'interface_name',
  calculateFn: (records: T[]) => any[]
): Map<string, any[]> {
  const grouped = new Map<string, T[]>()
  
  records.forEach(record => {
    const device = (record[deviceKey] || 'unknown') as string
    if (!grouped.has(device)) {
      grouped.set(device, [])
    }
    grouped.get(device)!.push(record)
  })
  
  const result = new Map<string, any[]>()
  grouped.forEach((deviceRecords, device) => {
    const sorted = [...deviceRecords].sort((a, b) => 
      new Date(a.collect_time).getTime() - new Date(b.collect_time).getTime()
    )
    result.set(device, calculateFn(sorted))
  })
  
  return result
}
```
