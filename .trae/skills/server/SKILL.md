---
name: "server-management"
description: "Squirrel 服务器管理页面开发规范。包含服务器列表、表单、详情等组件的实现，以及 API 接口和国际化配置。在开发服务器管理功能时调用。"
---

# Squirrel 服务器管理页面开发规范

## 概述

服务器管理页面提供对服务器的增删改查功能，包括服务器列表展示、添加/编辑服务器、查看详情、SSH 终端连接等功能。

## 目录结构

```
views/Server/
├── index.vue                 # 页面入口
├── components/
│   ├── ServerTable.vue       # 服务器列表表格
│   ├── ServerForm.vue        # 添加/编辑表单
│   ├── ServerDetail.vue      # 服务器详情弹窗
│   └── DeleteConfirm.vue     # 删除确认弹窗
```

## 类型定义

```typescript
// types/index.ts

// IP地址类型
export interface IpAddress {
  ipv4: string[]
  ipv6: string[]
  name: string
}

// 服务器详细信息
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

// 服务器类型
export interface Server {
  id: number
  hostname: string
  ip_address: string
  ssh_username: string
  ssh_port: number
  auth_type: 'password' | 'key'
  status: 'online' | 'offline' | 'unknown' | 'active'
  server_info?: ServerInfo | null
  server_alias?: string
}

// 创建服务器请求
export interface CreateServerRequest {
  ip_address: string
  ssh_username: string
  ssh_port: number
  ssh_password?: string
  ssh_private_key?: string
  auth_type: 'password' | 'key'
  status: 'active' | 'inactive'
  server_alias?: string
}

// 更新服务器请求
export interface UpdateServerRequest {
  ip_address: string
  ssh_username: string
  ssh_port: number
  auth_type: 'password' | 'key'
  status: 'active' | 'inactive'
  server_alias?: string
}
```

## API 接口

```typescript
// api/server.ts
import { get, post, del } from '@/utils/request'
import type { Server, CreateServerRequest, UpdateServerRequest } from '@/types'

/**
 * 获取服务器列表
 */
export function fetchServers(): Promise<Server[]> {
  return get('/server')
}

/**
 * 获取服务器详情
 */
export function fetchServerDetail(serverId: number): Promise<Server> {
  return get(`/server/${serverId}`)
}

/**
 * 创建服务器
 */
export function createServer(data: CreateServerRequest): Promise<string> {
  return post('/server', data)
}

/**
 * 更新服务器
 */
export function updateServer(serverId: number, data: UpdateServerRequest): Promise<string> {
  return post(`/server/${serverId}`, data)
}

/**
 * 删除服务器
 */
export function deleteServer(serverId: number): Promise<string> {
  return del(`/server/${serverId}`)
}
```

## 国际化语言包

```typescript
// lang/zh-CN/server.ts
export default {
  title: '服务器管理',
  listTitle: '服务器列表',
  addServer: '添加服务器',
  editServer: '编辑服务器',
  deleteServer: '删除服务器',
  connectTerminal: '连接终端',
  viewDetail: '查看详情',
  hostname: '主机名',
  ipAddress: 'IP地址',
  sshPort: 'SSH端口',
  username: '用户名',
  authType: '认证方式',
  status: '状态',
  operation: '操作',
  alias: '别名',
  password: '密码',
  key: '密钥',
  online: '在线',
  offline: '离线',
  unknown: '未知',
  active: '活跃',
  inactive: '停用',
  basicInfo: '基本信息',
  authInfo: '认证信息',
  optional: '可选',
  save: '保存',
  cancel: '取消',
  confirm: '确认',
  confirmDelete: '确认删除',
  deleteWarning: '确定要删除服务器 "{name}" 吗？此操作不可恢复。',
  required: '必填项',
  invalidIp: '请输入有效的IP地址',
  invalidPort: '端口范围 1-65535',
  createSuccess: '服务器创建成功',
  updateSuccess: '服务器更新成功',
  deleteSuccess: '服务器删除成功',
  operationFailed: '操作失败',
  terminal: '终端',
  connecting: '连接中...',
  connectionFailed: '连接失败',
  reconnect: '重新连接',
  close: '关闭',
  serverDetail: '服务器详情',
  systemInfo: '系统信息',
  os: '操作系统',
  kernel: '内核版本',
  architecture: '架构',
  platform: '平台',
  platformVersion: '平台版本',
  uptime: '运行时间',
  ipAddresses: 'IP地址列表'
}
```

## 路由配置

```typescript
// router/index.ts
{
  path: '/servers',
  name: 'Servers',
  component: () => import('@/views/Server/index.vue')
}
```

## 组件实现要点

### ServerTable 组件

- 使用表格行布局展示服务器列表
- 显示主机名、IP、端口、用户名、认证方式、状态
- 操作按钮：终端连接、查看详情、编辑、删除
- 状态徽章使用不同颜色区分在线/离线/未知

### ServerForm 组件

- 支持添加和编辑两种模式
- 基本信息：IP地址、SSH端口、用户名、别名
- 认证信息：密码/密钥切换，编辑时可选填
- 状态选择：活跃/停用
- 表单验证：IP格式、端口范围、必填项

### ServerDetail 组件

- 展示服务器基本信息和系统信息
- 系统信息包括：OS、内核、架构、平台、运行时间
- IP地址列表展示IPv4和IPv6

### DeleteConfirm 组件

- 删除确认弹窗
- 显示警告图标和删除提示
- 确认/取消按钮

## 主页面逻辑

```typescript
// 主要功能
const servers = ref<Server[]>([])
const showForm = ref(false)
const showDeleteConfirm = ref(false)
const showDetail = ref(false)
const showTerminal = ref(false)

// 加载服务器列表
const loadServers = async () => {
  servers.value = await fetchServers()
}

// 表单提交
const handleFormSubmit = async (data: CreateServerRequest | UpdateServerRequest) => {
  if (editingServer.value) {
    await updateServer(editingServer.value.id, data as UpdateServerRequest)
  } else {
    await createServer(data as CreateServerRequest)
  }
  await loadServers()
}

// 删除确认
const confirmDelete = async () => {
  await deleteServer(deletingServer.value.id)
  await loadServers()
}
```

## 设计规范

- 使用项目统一的配色方案（主色 #4fc3f7）
- 表格行悬停效果
- 状态徽章使用圆点和背景色
- 操作按钮使用图标按钮
- 弹窗使用圆角卡片设计
- 表单分组展示（基本信息、认证信息）

## 最佳实践

1. **列表布局**：使用表格行布局，不是方块卡片
2. **认证可选**：编辑时密码和密钥可以不填写
3. **状态管理**：使用响应式数据管理弹窗显示状态
4. **错误处理**：API 调用添加 try-catch 处理
5. **国际化**：所有文本使用 $t() 函数包裹
