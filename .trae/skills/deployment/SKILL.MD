---
name: "deployment"
description: "Squirrel 部署管理页面开发规范。包含部署列表、表单、详情等组件的实现，以及 API 接口和国际化配置。在开发部署管理功能时调用。"
---

# Squirrel 部署管理页面开发规范

## 概述

部署管理页面提供对应用部署实例的增删改查功能，包括部署列表展示、新建部署、启动/停止应用、卸载部署、查看详情等功能。部署实例是应用（Application）在特定服务器（Server）上的具体运行实例，由唯一的部署 ID 标识。

## 目录结构

```
# 部署管理页面模块
views/Deployment/
├── index.vue                 # 页面入口
├── components/
│   ├── DeploymentTable.vue   # 部署列表表格
│   ├── DeploymentForm.vue    # 新建部署表单
│   ├── DeploymentDetail.vue  # 部署详情
│   └── UndeployConfirm.vue   # 卸载确认弹窗
├── composables/
│   └── useDeployment.ts      # 部署相关的可组合函数
├── types/
│   └── index.ts              # 页面类型定义
└── styles/
    └── deployment.scss       # 部署相关组件样式

# API 接口模块
api/
└── deployment.ts             # 部署相关 API 接口

# 通用组件模块
components/
├── Button.vue                # 按钮组件
├── Loading.vue               # 加载组件
├── Empty.vue                 # 空状态组件
├── PageHeader.vue            # 页面头部组件
├── Modal.vue                 # 模态框组件
├── Toast.vue                # 提示组件
└── index.ts                 # 组件导出

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
│   └── deployment.ts         # 部署管理文本
└── en-US/
    ├── index.ts              # 英文语言包入口
    ├── common.ts             # 通用文本
    ├── layout.ts             # 布局文本
    └── deployment.ts         # 部署管理文本

# 路由配置
router/
└── index.ts                  # 路由配置入口

# 工具函数
utils/
└── request.ts                # HTTP 请求封装
```

## 类型定义

### 全局类型定义

```typescript
// types/index.ts

// 部署状态
export type DeploymentStatus = 'running' | 'stopped' | 'not_deployed' | 'error'

// 部署信息
export interface Deployment {
  id: number
  deploy_id: number
  application: ApplicationInfo
  server: ServerInfo
  status: DeploymentStatus
  deployed_at: string
}

// 应用信息
export interface ApplicationInfo {
  id: number
  name: string
  description: string
  type: string
  version: string
}

// 服务器信息
export interface ServerInfo {
  id: number
  ip_address: string
  agent_port: number
}

// 创建部署请求
export interface CreateDeploymentRequest {
  application_id: number
  server_id: number
}

// API 响应
export interface ApiResponse<T = any> {
  code: number
  message: string
  data: T
}
```

### 页面类型定义

```typescript
// views/Deployment/types/index.ts

import type { Deployment, CreateDeploymentRequest, DeploymentStatus } from '@/types'

export type { Deployment, CreateDeploymentRequest, DeploymentStatus }

// 表单状态
export interface FormState {
  visible: boolean
  data: CreateDeploymentRequest | null
}

// 卸载确认状态
export interface UndeployState {
  visible: boolean
  deployment: Deployment | null
}

// 详情状态
export interface DetailState {
  visible: boolean
  deployment: Deployment | null
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

export function post<T = any>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T> {
  return instance.post(url, data, config)
}

export function del<T = any>(url: string, config?: AxiosRequestConfig): Promise<T> {
  return instance.delete(url, config)
}

export default instance
```

### 部署相关 API

```typescript
// api/deployment.ts
import { get, post, del } from '@/utils/request'
import type { Deployment, CreateDeploymentRequest, ServerInfo } from '@/types'

/**
 * 获取部署列表
 */
export function fetchDeployments(serverId?: number): Promise<Deployment[]> {
  const params = serverId ? { server_id: serverId } : {}
  return get('/deployment', { params })
}

/**
 * 创建部署
 */
export function createDeployment(applicationId: number, data: CreateDeploymentRequest): Promise<string> {
  return post(`/deployment/${applicationId}`, data)
}

/**
 * 启动应用
 */
export function startDeployment(deploymentId: number): Promise<string> {
  return post(`/deployment/${deploymentId}/start`)
}

/**
 * 停止应用
 */
export function stopDeployment(deploymentId: number): Promise<string> {
  return post(`/deployment/${deploymentId}/stop`)
}

/**
 * 卸载部署
 */
export function undeployDeployment(deploymentId: number): Promise<string> {
  return del(`/deployment/${deploymentId}`)
}

/**
 * 获取应用部署的服务器列表
 */
export function fetchDeploymentServers(applicationId: number): Promise<ServerInfo[]> {
  return get(`/deployment/${applicationId}/servers`)
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
import deployment from './deployment'

export default {
  common,
  layout,
  deployment
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
  deployments: '部署管理',
  monitor: '监控',
  logout: '退出登录'
}
```

```typescript
// lang/zh-CN/deployment.ts
export default {
  title: '部署管理',
  listTitle: '部署列表',
  addDeployment: '新建部署',
  undeployDeployment: '卸载部署',
  viewDetail: '查看详情',
  deploymentId: '部署 ID',
  deployId: '部署 ID',
  applicationName: '应用名称',
  applicationType: '应用类型',
  applicationVersion: '应用版本',
  serverAddress: '服务器地址',
  agentPort: 'Agent 端口',
  status: '状态',
  deployedAt: '部署时间',
  operation: '操作',
  confirm: '确认',
  cancel: '取消',
  confirmUndeploy: '确认卸载',
  undeployWarning: '确定要卸载部署 "{name}" 吗？此操作不可恢复。',
  required: '必填项',
  createSuccess: '部署创建成功',
  startSuccess: '应用启动成功',
  stopSuccess: '应用停止成功',
  undeploySuccess: '部署卸载成功',
  operationFailed: '操作失败',
  deploymentDetail: '部署详情',
  basicInfo: '基本信息',
  selectApplication: '选择应用',
  selectServer: '选择服务器',
  start: '启动',
  stop: '停止',
  undeploy: '卸载',
  statusRunning: '运行中',
  statusStopped: '已停止',
  statusNotDeployed: '未部署',
  statusError: '错误',
  deploy: '部署',
  filterByServer: '按服务器筛选',
  allServers: '全部服务器'
}
```

### 英文语言包

```typescript
// lang/en-US/index.ts
import common from './common'
import layout from './layout'
import deployment from './deployment'

export default {
  common,
  layout,
  deployment
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
  deployments: 'Deployments',
  monitor: 'Monitor',
  logout: 'Logout'
}
```

```typescript
// lang/en-US/deployment.ts
export default {
  title: 'Deployment Management',
  listTitle: 'Deployment List',
  addDeployment: 'New Deployment',
  undeployDeployment: 'Undeploy',
  viewDetail: 'View Detail',
  deploymentId: 'Deployment ID',
  deployId: 'Deploy ID',
  applicationName: 'Application Name',
  applicationType: 'Application Type',
  applicationVersion: 'Application Version',
  serverAddress: 'Server Address',
  agentPort: 'Agent Port',
  status: 'Status',
  deployedAt: 'Deployed At',
  operation: 'Operation',
  confirm: 'Confirm',
  cancel: 'Cancel',
  confirmUndeploy: 'Confirm Undeploy',
  undeployWarning: 'Are you sure you want to undeploy "{name}"? This action cannot be undone.',
  required: 'Required',
  createSuccess: 'Deployment created successfully',
  startSuccess: 'Application started successfully',
  stopSuccess: 'Application stopped successfully',
  undeploySuccess: 'Deployment undeployed successfully',
  operationFailed: 'Operation failed',
  deploymentDetail: 'Deployment Detail',
  basicInfo: 'Basic Info',
  selectApplication: 'Select Application',
  selectServer: 'Select Server',
  start: 'Start',
  stop: 'Stop',
  undeploy: 'Undeploy',
  statusRunning: 'Running',
  statusStopped: 'Stopped',
  statusNotDeployed: 'Not Deployed',
  statusError: 'Error',
  deploy: 'Deploy',
  filterByServer: 'Filter by Server',
  allServers: 'All Servers'
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
    path: '/applications',
    name: 'Applications',
    component: () => import('@/views/Application/index.vue')
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
    path: '/deployments',
    name: 'Deployments',
    component: () => import('@/views/Deployment/index.vue')
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

### 通用组件

#### Toast 组件

```vue
<template>
  <Transition name="toast">
    <div v-if="visible" class="toast" :class="`toast--${type}`">
      <Icon :icon="getIcon()" class="toast-icon" />
      <span class="toast-message">{{ message }}</span>
    </div>
  </Transition>
</template>

<script setup lang="ts">
import { Icon } from '@iconify/vue'

interface Props {
  visible?: boolean
  message?: string
  type?: 'success' | 'error' | 'warning' | 'info'
}

const props = withDefaults(defineProps<Props>(), {
  visible: false,
  message: '',
  type: 'success'
})

const getIcon = () => {
  const icons: Record<string, string> = {
    success: 'lucide:check-circle',
    error: 'lucide:x-circle',
    warning: 'lucide:alert-triangle',
    info: 'lucide:info'
  }
  return icons[props.type]
}
</script>
```

#### Button 组件

```vue
<template>
  <button
    :class="[
      'sq-button',
      `sq-button--${type}`,
      `sq-button--${size}`,
      {
        'sq-button--disabled': disabled,
        'sq-button--loading': loading,
        'sq-button--block': block
      }
    ]"
    :disabled="disabled || loading"
    @click="handleClick"
  >
    <span v-if="loading" class="sq-button__loading">
      <Icon icon="lucide:loader-2" class="animate-spin" />
    </span>
    <span v-if="icon && !loading" class="sq-button__icon">
      <Icon :icon="icon" />
    </span>
    <span class="sq-button__content">
      <slot />
    </span>
  </button>
</template>

<script setup lang="ts">
import { Icon } from '@iconify/vue'

interface Props {
  type?: 'primary' | 'secondary' | 'danger' | 'ghost'
  size?: 'small' | 'medium' | 'large'
  disabled?: boolean
  loading?: boolean
  icon?: string
  block?: boolean
}

withDefaults(defineProps<Props>(), {
  type: 'primary',
  size: 'medium',
  disabled: false,
  loading: false,
  block: false
})

const emit = defineEmits<{
  click: [event: MouseEvent]
}>()

const handleClick = (event: MouseEvent) => {
  emit('click', event)
}
</script>
```

#### Loading 组件

```vue
<template>
  <div class="loading-container">
    <Icon icon="lucide:loader-2" class="loading-icon animate-spin" />
    <span v-if="text" class="loading-text">{{ text }}</span>
  </div>
</template>

<script setup lang="ts">
import { Icon } from '@iconify/vue'

interface Props {
  text?: string
}

withDefaults(defineProps<Props>(), {
  text: ''
})
</script>
```

#### Empty 组件

```vue
<template>
  <div class="empty-container">
    <Icon :icon="icon" class="empty-icon" />
    <p class="empty-text">{{ description }}</p>
    <slot name="action" />
  </div>
</template>

<script setup lang="ts">
import { Icon } from '@iconify/vue'

interface Props {
  description?: string
  icon?: string
}

withDefaults(defineProps<Props>(), {
  description: '暂无数据',
  icon: 'lucide:inbox'
})
</script>
```

### DeploymentTable 组件

- 使用表格行布局展示部署列表
- 显示部署 ID、应用信息、服务器信息、状态、部署时间
- 支持按服务器筛选
- 操作按钮：查看详情、启动、停止、卸载
- 状态使用不同颜色标识

```vue
<template>
  <div class="deployment-table-container">
    <table class="deployment-table">
      <thead>
        <tr>
          <th>{{ $t('deployment.deployId') }}</th>
          <th>{{ $t('deployment.applicationName') }}</th>
          <th>{{ $t('deployment.serverAddress') }}</th>
          <th>{{ $t('deployment.status') }}</th>
          <th>{{ $t('deployment.deployedAt') }}</th>
          <th>{{ $t('common.operation') }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="deployment in deployments" :key="deployment.id" class="deployment-row">
          <td class="deploy-id-cell">
            <span class="deploy-id-text">{{ deployment.deploy_id }}</span>
          </td>
          <td class="application-cell">
            <div class="application-wrapper">
              <Icon :icon="getTypeIcon(deployment.application.type)" class="type-icon" />
              <div class="application-info">
                <span class="application-name">{{ deployment.application.name }}</span>
                <span class="application-version">{{ deployment.application.version }}</span>
              </div>
            </div>
          </td>
          <td class="server-cell">
            <div class="server-wrapper">
              <Icon icon="lucide:server" class="server-icon" />
              <div class="server-info">
                <span class="server-ip">{{ deployment.server.ip_address }}</span>
                <span class="agent-port">:{{ deployment.server.agent_port }}</span>
              </div>
            </div>
          </td>
          <td class="status-cell">
            <span class="status-badge" :class="`status-badge--${deployment.status}`">
              {{ getStatusLabel(deployment.status) }}
            </span>
          </td>
          <td class="deployed-at-cell">
            <span class="deployed-at-text">{{ deployment.deployed_at }}</span>
          </td>
          <td class="action-cell">
            <button class="action-btn view-btn" :title="$t('deployment.viewDetail')" @click="$emit('view', deployment)">
              <Icon icon="lucide:eye" />
            </button>
            <button
              v-if="deployment.status === 'stopped'"
              class="action-btn start-btn"
              :title="$t('deployment.start')"
              @click="$emit('start', deployment)"
            >
              <Icon icon="lucide:play" />
            </button>
            <button
              v-if="deployment.status === 'running'"
              class="action-btn stop-btn"
              :title="$t('deployment.stop')"
              @click="$emit('stop', deployment)"
            >
              <Icon icon="lucide:pause" />
            </button>
            <button class="action-btn undeploy-btn" :title="$t('deployment.undeploy')" @click="$emit('undeploy', deployment)">
              <Icon icon="lucide:trash-2" />
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup lang="ts">
import { Icon } from '@iconify/vue'
import type { Deployment } from '@/types'

const props = defineProps<{
  deployments: Deployment[]
}>()

defineEmits<{
  view: [deployment: Deployment]
  start: [deployment: Deployment]
  stop: [deployment: Deployment]
  undeploy: [deployment: Deployment]
}>()

const getTypeIcon = (type: string) => {
  const icons: Record<string, string> = {
    compose: 'lucide:box',
    script: 'lucide:terminal',
    binary: 'lucide:file-code'
  }
  return icons[type] || 'lucide:package'
}

const getStatusLabel = (status: string) => {
  const labels: Record<string, string> = {
    running: '运行中',
    stopped: '已停止',
    not_deployed: '未部署',
    error: '错误'
  }
  return labels[status] || status
}
</script>
```

### DeploymentForm 组件

- 支持新建部署
- 选择应用：下拉选择器，显示应用列表
- 选择服务器：下拉选择器，显示服务器列表
- 表单验证：应用和服务器都为必填项
- 提交后调用部署 API

```vue
<template>
  <Modal v-model:visible="visible" :title="$t('deployment.addDeployment')" width="500px">
    <Form ref="formRef" :model="formData" :rules="rules">
      <FormItem :label="$t('deployment.selectApplication')" prop="application_id" required>
        <Select v-model="formData.application_id" :options="applicationOptions" :placeholder="$t('deployment.selectApplication')" />
      </FormItem>
      <FormItem :label="$t('deployment.selectServer')" prop="server_id" required>
        <Select v-model="formData.server_id" :options="serverOptions" :placeholder="$t('deployment.selectServer')" />
      </FormItem>
    </Form>
    <template #footer>
      <Button @click="handleCancel">{{ $t('common.cancel') }}</Button>
      <Button type="primary" :loading="loading" @click="handleSubmit">{{ $t('common.confirm') }}</Button>
    </template>
  </Modal>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'
import { Icon } from '@iconify/vue'
import type { CreateDeploymentRequest } from '@/types'

interface Props {
  visible: boolean
  applications: Array<{ id: number; name: string }>
  servers: Array<{ id: number; ip_address: string }>
}

const props = defineProps<Props>()

const emit = defineEmits<{
  'update:visible': [value: boolean]
  submit: [data: CreateDeploymentRequest]
}>()

const formRef = ref()
const loading = ref(false)
const formData = ref<CreateDeploymentRequest>({
  application_id: 0,
  server_id: 0
})

const rules = {
  application_id: [{ required: true, message: '请选择应用', trigger: 'change' }],
  server_id: [{ required: true, message: '请选择服务器', trigger: 'change' }]
}

const applicationOptions = computed(() =>
  props.applications.map(app => ({ label: app.name, value: app.id }))
)

const serverOptions = computed(() =>
  props.servers.map(server => ({ label: server.ip_address, value: server.id }))
)

const handleSubmit = async () => {
  await formRef.value?.validate()
  emit('submit', formData.value)
}

const handleCancel = () => {
  emit('update:visible', false)
}

watch(() => props.visible, (val) => {
  if (!val) {
    formData.value = { application_id: 0, server_id: 0 }
  }
})
</script>
```

### DeploymentDetail 组件

- 显示部署完整信息
- 基本信息：部署 ID、应用信息、服务器信息、状态、部署时间
- 操作按钮：启动、停止、卸载

### UndeployConfirm 组件

- 卸载确认弹窗
- 显示警告图标和卸载提示
- 确认/取消按钮

## 主页面逻辑

```typescript
// 主要功能
const deployments = ref<Deployment[]>([])
const showForm = ref(false)
const showUndeployConfirm = ref(false)
const showDetail = ref(false)
const filterServerId = ref<number | undefined>(undefined)
const toastVisible = ref(false)
const toastMessage = ref('')

// 过滤
const filteredDeployments = computed(() => {
  if (filterServerId.value) {
    return deployments.value.filter(d => d.server.id === filterServerId.value)
  }
  return deployments.value
})

// 加载部署列表
const loadDeployments = async () => {
  deployments.value = await fetchDeployments(filterServerId.value)
}

// 表单提交
const handleFormSubmit = async (data: CreateDeploymentRequest) => {
  await createDeployment(data.application_id, data)
  await loadDeployments()
  showToast($t('deployment.createSuccess'), 'success')
}

// 启动应用
const handleStart = async (deployment: Deployment) => {
  await startDeployment(deployment.id)
  await loadDeployments()
  showToast($t('deployment.startSuccess'), 'success')
}

// 停止应用
const handleStop = async (deployment: Deployment) => {
  await stopDeployment(deployment.id)
  await loadDeployments()
  showToast($t('deployment.stopSuccess'), 'success')
}

// 卸载确认
const confirmUndeploy = async () => {
  await undeployDeployment(undeployingDeployment.value.id)
  await loadDeployments()
  showToast($t('deployment.undeploySuccess'), 'success')
}

// 服务器筛选
const handleServerFilter = (serverId: number | undefined) => {
  filterServerId.value = serverId
  loadDeployments()
}
```

## 功能特性

### 1. 部署列表管理 ⭐⭐⭐⭐
- 查看所有部署实例
- 支持按服务器筛选部署列表
- 显示部署详情：
  - 部署 ID 和部署时间
  - 应用信息（名称、版本、类型）
  - 服务器信息（IP 地址、Agent 端口）
  - 当前运行状态

### 2. 新建部署 ⭐⭐⭐
- 选择要部署的应用
- 选择目标服务器
- 系统自动生成唯一的部署 ID
- 调用 Agent API 执行实际部署
- 部署失败时自动回滚

### 3. 应用状态控制 ⭐⭐⭐⭐
- **启动应用**：将已停止的应用重新启动
- **停止应用**：优雅停止正在运行的应用
- 通过 Agent API 执行状态变更操作

### 4. 部署卸载 ⭐⭐⭐
- 卸载不再需要的部署实例
- 调用 Agent 清理容器和资源
- 删除部署记录

### 5. 查看详情 ⭐⭐⭐
- 查看部署完整配置信息
- 查看应用和服务器详细信息
- 查看部署时间和状态

### 6. 状态标识 ⭐⭐⭐⭐
- 使用不同颜色标识不同状态
- running：绿色
- stopped：灰色
- error：红色
- not_deployed：黄色

## 设计规范

- 使用项目统一的配色方案（主色 #4fc3f7）
- 表格行悬停效果
- 操作按钮使用图标按钮
- 弹窗使用圆角卡片设计
- 表单分组展示（应用选择、服务器选择）
- 遵循 Squirrel 紧凑风格页面设计规范（调用 `compact-page-style` skill）

## 最佳实践

1. **列表布局**：使用表格行布局，不是方块卡片
2. **状态管理**：使用响应式数据管理弹窗显示状态
3. **错误处理**：API 调用添加 try-catch 处理
4. **国际化**：所有文本使用 $t() 函数包裹
5. **表单验证**：应用和服务器都为必填项
6. **组件复用**：参考 `component-reusability` skill 设计可复用组件
7. **筛选优化**：使用 computed 实现实时过滤
8. **状态标识**：使用不同颜色和图标区分部署状态
9. **操作权限**：根据部署状态显示不同的操作按钮
10. **回滚机制**：部署失败时自动回滚，清理已部署的应用

## 架构参考

- 遵循 Squirrel 运维平台前端布局架构（调用 `layout` skill）
- 使用 Vue3 + vue-i18n 国际化（调用 `vue-i18n-setup` skill）

## 与其他模块的关系

```
Application（应用实例）
    │
    │ [部署到服务器]
    ▼
Deployment（部署实例）
    │
    ├──> Server（目标服务器）
    │
    └──> Agent（部署执行者）
```

## 部署状态说明

| 状态 | 说明 | 可执行操作 |
|------|------|-----------|
| `running` | 应用正在正常运行 | 停止、卸载 |
| `stopped` | 应用已停止 | 启动、卸载 |
| `not_deployed` | 应用已创建但未部署 | 等待部署完成 |
| `error` | 应用运行出错 | 停止、卸载、重新部署 |

## 接口案例文件

- .trae\skills\deployment\api.md
