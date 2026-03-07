---
name: monitor
description: 当用户需要开发 Squirrel 监控管理页面、实现服务器监控仪表盘、CPU/内存/磁盘/网络图表展示、历史数据趋势等功能时调用。
---

# Squirrel 监控管理页面开发规范

## 触发条件（When）

当用户请求以下任务时触发此 Skill：
- 开发监控管理页面（`/monitor` 路由）
- 实现 CPU/内存/磁盘/网络监控图表
- 处理历史监控数据展示
- 实现服务器监控仪表盘功能

## 执行步骤（How）

### 1. 理解架构

监控页面提供服务器历史监控数据趋势分析，包括：
- **Overview 页面** (`/`) - 实时监控数据展示
- **Monitor 页面** (`/monitor`) - 历史数据趋势分析

```
Frontend (Vue) → squ-apiserver (代理层) → squ-agent (各主机)
```

### 2. 创建目录结构

```
views/Monitor/
├── index.vue                    # 监控中心主页
├── components/
│   ├── CPUMonitorChart.vue      # CPU历史趋势图表
│   ├── MemoryMonitorChart.vue   # 内存历史趋势图表
│   ├── DiskIOChart.vue          # 磁盘IO图表
│   ├── NetIOChart.vue           # 网络IO图表
│   ├── ServerSelector.vue       # 服务器选择器
│   └── TimeRangeSelector.vue    # 时间范围选择器
├── composables/
│   ├── useMonitorHistory.ts     # 历史数据查询逻辑
│   └── useDeviceList.ts         # 设备列表获取逻辑
└── types/
    └── index.ts                 # 页面类型定义
```

### 3. 实现页面布局

页面包含 5 个表盘：
| 表盘 | 数据来源 | 筛选选项 |
|------|---------|---------|
| CPU 使用率 | BaseMonitor.cpu_usage | 无 |
| 内存使用率 | BaseMonitor.memory_usage | 无 |
| 磁盘使用率 | DiskUsageMonitor | 挂载点选择 |
| 磁盘 IO | DiskIOMonitor | 磁盘设备选择 |
| 网络流量 | NetworkMonitor | 网卡选择 |

### 4. 关键实现要点

**IO 数据处理（重要）**：
- 后端返回的是**累计值**，不是瞬时速率
- 前端必须计算相邻数据点的差值除以时间间隔
- 数据按时间倒序返回，计算前需先反转数组
- 使用 `calculateDiskIOSpeed` 和 `calculateNetworkIOSpeed` 函数

**速率计算公式**：
```
速率 (bytes/s) = (当前值 - 上次值) / 时间差(秒)
```

### 5. 设计规范

- 主色：`#4fc3f7`
- 使用率颜色：正常(`#67c23a`)、警告(`#e6a23c`)、危险(`#f56c6c`)
- 卡片圆角：8px
- 图表库：ECharts
- 遵循 `compact-page-style` skill

## 输出结果（What）

完成后的页面应具备：
- 服务器选择器（右上角）
- 时间范围选择器（1h/6h/24h/7d）
- 5 个监控图表表盘
- 设备筛选功能（磁盘/网卡）
- 完整的国际化支持

## 参考文件

详细实现请参考以下文件：
- [类型定义](types.md) - 数据模型和 TypeScript 类型
- [组件实现](components.md) - 图表组件和主页面代码
- [工具函数](utils.md) - 格式化和速率计算函数
- [API 接口](api.md) - 后端接口示例

## 国际化配置

```typescript
// lang/zh-CN/monitor.ts
export default {
  title: '监控中心',
  selectServer: '选择服务器',
  cpuUsage: 'CPU 使用率',
  memoryUsage: '内存使用率',
  diskIO: '磁盘 IO',
  networkTraffic: '网络流量',
  read: '读取',
  write: '写入',
  upload: '上传',
  download: '下载',
  all: '全部'
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

## 相关文件

- 后端 Agent 处理器: `internal/squ-agent/handler/monitor/`
- 后端 API Server 代理: `internal/squ-apiserver/handler/monitor/`
- 前端 API: `front/src/api/monitor.ts`
- 前端 Overview: `front/src/views/Overview/`
