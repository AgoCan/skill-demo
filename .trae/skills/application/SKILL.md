---
name: "application"
description: "Squirrel 应用管理页面开发规范。包含应用列表、表单、详情等组件的实现，以及 API 接口和国际化配置。在开发应用管理功能时调用。"
---

# Squirrel 应用管理页面开发规范

## 概述

应用管理页面提供对应用实例的增删改查功能，包括应用列表展示、添加/编辑应用、搜索过滤、查看详情等功能。应用实例是从应用商店导入的具体应用配置，代表一个可以部署到服务器上的应用。

## 目录结构

```
# 应用管理页面模块
views/Application/
├── index.vue                 # 页面入口
├── components/
│   ├── ApplicationTable.vue  # 应用列表表格
│   ├── ApplicationForm.vue   # 添加/编辑表单
│   ├── ApplicationDetail.vue # 应用详情
│   └── DeleteConfirm.vue     # 删除确认弹窗
├── composables/
│   └── useApplication.ts     # 应用相关的可组合函数
├── types/
│   └── index.ts              # 页面类型定义
└── styles/
    └── application.scss      # 应用相关组件样式

# API 接口模块
api/
└── application.ts            # 应用相关 API 接口

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
│   └── application.ts       # 应用管理文本
└── en-US/
    ├── index.ts              # 英文语言包入口
    ├── common.ts             # 通用文本
    ├── layout.ts             # 布局文本
    └── application.ts       # 应用管理文本

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

// 应用类型
export interface Application {
  id: number
  name: string
  description: string
  type: string
  content: string
  version: string
}

// 创建应用请求
export interface CreateApplicationRequest {
  name: string
  description: string
  type: string
  content: string
  version: string
}

// 更新应用请求
export interface UpdateApplicationRequest {
  id: number
  name: string
  description: string
  type: string
  content: string
  version: string
}

// 分页响应
export interface PageResponse<T> {
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
```

### 页面类型定义

```typescript
// views/Application/types/index.ts

import type { Application, CreateApplicationRequest, UpdateApplicationRequest } from '@/types'

export type { Application, CreateApplicationRequest, UpdateApplicationRequest }

// 表单状态
export interface FormState {
  visible: boolean
  mode: 'create' | 'edit'
  data: CreateApplicationRequest | UpdateApplicationRequest | null
}

// 删除确认状态
export interface DeleteState {
  visible: boolean
  application: Application | null
}

// 详情状态
export interface DetailState {
  visible: boolean
  application: Application | null
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

export function put<T = any>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T> {
  return instance.put(url, data, config)
}

export function del<T = any>(url: string, config?: AxiosRequestConfig): Promise<T> {
  return instance.delete(url, config)
}

export default instance
```

### 应用相关 API

```typescript
// api/application.ts
import { get, post, del } from '@/utils/request'
import type { Application, CreateApplicationRequest, UpdateApplicationRequest } from '@/types'

/**
 * 获取应用列表
 */
export function fetchApplications(): Promise<Application[]> {
  return get('/application')
}

/**
 * 获取应用详情
 */
export function fetchApplicationDetail(applicationId: number): Promise<Application> {
  return get(`/application/${applicationId}`)
}

/**
 * 创建应用
 */
export function createApplication(data: CreateApplicationRequest): Promise<string> {
  return post('/application', data)
}

/**
 * 更新应用
 */
export function updateApplication(applicationId: number, data: UpdateApplicationRequest): Promise<string> {
  return post(`/application/${applicationId}`, data)
}

/**
 * 删除应用
 */
export function deleteApplication(applicationId: number): Promise<string> {
  return del(`/application/${applicationId}`)
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
import application from './application'

export default {
  common,
  layout,
  application
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
// lang/zh-CN/application.ts
export default {
  title: '应用管理',
  listTitle: '应用列表',
  addApplication: '添加应用',
  editApplication: '编辑应用',
  deleteApplication: '删除应用',
  viewDetail: '查看详情',
  applicationName: '应用名称',
  applicationType: '应用类型',
  applicationVersion: '应用版本',
  applicationDescription: '应用描述',
  applicationContent: '应用内容',
  operation: '操作',
  save: '保存',
  cancel: '取消',
  confirm: '确认',
  confirmDelete: '确认删除',
  deleteWarning: '确定要删除应用 "{name}" 吗？此操作不可恢复。',
  required: '必填项',
  createSuccess: '应用创建成功',
  updateSuccess: '应用更新成功',
  deleteSuccess: '应用删除成功',
  operationFailed: '操作失败',
  applicationDetail: '应用详情',
  basicInfo: '基本信息',
  searchPlaceholder: '搜索应用名称或描述...',
  typeCompose: 'Compose',
  typeScript: '脚本',
  typeBinary: '二进制',
  contentPreview: '内容预览',
  copy: '复制',
  copySuccess: '已复制到剪贴板',
  sortAsc: '升序',
  sortDesc: '降序',
  importFromStore: '从应用商店导入',
  deploy: '部署'
}
```

### 英文语言包

```typescript
// lang/en-US/index.ts
import common from './common'
import layout from './layout'
import application from './application'

export default {
  common,
  layout,
  application
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
// lang/en-US/application.ts
export default {
  title: 'Application Management',
  listTitle: 'Application List',
  addApplication: 'Add Application',
  editApplication: 'Edit Application',
  deleteApplication: 'Delete Application',
  viewDetail: 'View Detail',
  applicationName: 'Application Name',
  applicationType: 'Application Type',
  applicationVersion: 'Application Version',
  applicationDescription: 'Application Description',
  applicationContent: 'Application Content',
  operation: 'Operation',
  save: 'Save',
  cancel: 'Cancel',
  confirm: 'Confirm',
  confirmDelete: 'Confirm Delete',
  deleteWarning: 'Are you sure you want to delete application "{name}"? This action cannot be undone.',
  required: 'Required',
  createSuccess: 'Application created successfully',
  updateSuccess: 'Application updated successfully',
  deleteSuccess: 'Application deleted successfully',
  operationFailed: 'Operation failed',
  applicationDetail: 'Application Detail',
  basicInfo: 'Basic Info',
  searchPlaceholder: 'Search application name or description...',
  typeCompose: 'Compose',
  typeScript: 'Script',
  typeBinary: 'Binary',
  contentPreview: 'Content Preview',
  copy: 'Copy',
  copySuccess: 'Copied to clipboard',
  sortAsc: 'Ascending',
  sortDesc: 'Descending',
  importFromStore: 'Import from App Store',
  deploy: 'Deploy'
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

### ApplicationTable 组件

- 使用表格行布局展示应用列表
- 显示应用名称、类型、版本、描述
- 支持排序功能（点击表头切换升序/降序）
- 操作按钮：查看详情、编辑、删除、部署
- 描述过长时使用省略号显示

```vue
<template>
  <div class="application-table-container">
    <table class="application-table">
      <thead>
        <tr>
          <th class="sortable" @click="$emit('sort', 'name')">
            {{ $t('application.applicationName') }}
            <Icon :icon="getSortIcon('name')" class="sort-icon" />
          </th>
          <th class="sortable" @click="$emit('sort', 'type')">
            {{ $t('application.applicationType') }}
            <Icon :icon="getSortIcon('type')" class="sort-icon" />
          </th>
          <th class="sortable" @click="$emit('sort', 'version')">
            {{ $t('application.applicationVersion') }}
            <Icon :icon="getSortIcon('version')" class="sort-icon" />
          </th>
          <th>{{ $t('application.applicationDescription') }}</th>
          <th>{{ $t('common.operation') }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="app in applications" :key="app.id" class="application-row">
          <td class="name-cell">
            <div class="name-wrapper">
              <Icon :icon="getTypeIcon(app.type)" class="type-icon" />
              <span class="name-text">{{ app.name }}</span>
            </div>
          </td>
          <td class="type-cell">
            <span class="type-badge" :class="`type-badge--${app.type}`">
              {{ getTypeLabel(app.type) }}
            </span>
          </td>
          <td class="version-cell">
            <span class="version-text">{{ app.version }}</span>
          </td>
          <td class="description-cell">
            <div class="description-wrapper">
              <span class="description-text" :title="app.description">{{ app.description }}</span>
            </div>
          </td>
          <td class="action-cell">
            <button class="action-btn view-btn" :title="$t('application.viewDetail')" @click="$emit('view', app)">
              <Icon icon="lucide:eye" />
            </button>
            <button class="action-btn edit-btn" :title="$t('application.editApplication')" @click="$emit('edit', app)">
              <Icon icon="lucide:edit-2" />
            </button>
            <button class="action-btn delete-btn" :title="$t('application.deleteApplication')" @click="$emit('delete', app)">
              <Icon icon="lucide:trash-2" />
            </button>
            <button class="action-btn deploy-btn" :title="$t('application.deploy')" @click="$emit('deploy', app)">
              <Icon icon="lucide:rocket" />
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup lang="ts">
import { Icon } from '@iconify/vue'
import type { Application } from '@/types'

const props = defineProps<{
  applications: Application[]
  sortBy?: string | null
  sortOrder?: 'asc' | 'desc'
}>()

defineEmits<{
  edit: [application: Application]
  delete: [application: Application]
  view: [application: Application]
  deploy: [application: Application]
  sort: [field: string]
}>()

const getSortIcon = (field: string) => {
  if (field !== props.sortBy) return 'lucide:chevrons-up-down'
  return props.sortOrder === 'asc' ? 'lucide:chevron-up' : 'lucide:chevron-down'
}

const getTypeIcon = (type: string) => {
  const icons: Record<string, string> = {
    compose: 'lucide:box',
    script: 'lucide:terminal',
    binary: 'lucide:file-code'
  }
  return icons[type] || 'lucide:package'
}

const getTypeLabel = (type: string) => {
  const labels: Record<string, string> = {
    compose: 'Compose',
    script: '脚本',
    binary: '二进制'
  }
  return labels[type] || type
}
</script>
```

### ApplicationForm 组件

- 支持添加和编辑两种模式
- 应用名称：文本输入框，必填
- 应用类型：下拉选择（compose、script、binary）
- 应用版本：文本输入框，必填
- 应用描述：文本域，可选
- 应用内容：代码编辑器，必填，支持语法高亮
- 表单验证：必填项验证

### ApplicationDetail 组件

- 显示应用完整信息
- 基本信息：名称、类型、版本、描述
- 内容预览：代码编辑器，只读模式
- 操作按钮：编辑、删除、部署

### DeleteConfirm 组件

- 删除确认弹窗
- 显示警告图标和删除提示
- 确认/取消按钮

## 主页面逻辑

```typescript
// 主要功能
const applications = ref<Application[]>([])
const showForm = ref(false)
const showDeleteConfirm = ref(false)
const showDetail = ref(false)
const searchKeyword = ref('')
const sortBy = ref<string | null>(null)
const sortOrder = ref<'asc' | 'desc'>('asc')
const toastVisible = ref(false)
const toastMessage = ref('')

// 过滤和排序
const filteredApplications = computed(() => {
  let result = applications.value

  if (searchKeyword.value) {
    const keyword = searchKeyword.value.toLowerCase()
    result = result.filter(app => 
      app.name.toLowerCase().includes(keyword) ||
      app.description.toLowerCase().includes(keyword)
    )
  }

  if (sortBy.value) {
    result = [...result].sort((a, b) => {
      const aValue = a[sortBy.value as keyof Application]
      const bValue = b[sortBy.value as keyof Application]
      const comparison = String(aValue).localeCompare(String(bValue))
      return sortOrder.value === 'asc' ? comparison : -comparison
    })
  }

  return result
})

// 加载应用列表
const loadApplications = async () => {
  applications.value = await fetchApplications()
}

// 表单提交
const handleFormSubmit = async (data: CreateApplicationRequest | UpdateApplicationRequest) => {
  if (editingApplication.value) {
    await updateApplication(editingApplication.value.id, data as UpdateApplicationRequest)
  } else {
    await createApplication(data as CreateApplicationRequest)
  }
  await loadApplications()
}

// 排序处理
const handleSort = (field: string) => {
  if (sortBy.value === field) {
    sortOrder.value = sortOrder.value === 'asc' ? 'desc' : 'asc'
  } else {
    sortBy.value = field
    sortOrder.value = 'asc'
  }
}

// 删除确认
const confirmDelete = async () => {
  await deleteApplication(deletingApplication.value.id)
  await loadApplications()
}

// 部署应用
const handleDeploy = (application: Application) => {
  // 跳转到部署页面或打开部署对话框
  router.push({ name: 'Deployment', params: { applicationId: application.id } })
}
```

## 功能特性

### 1. 模糊搜索 ⭐⭐⭐⭐
- 在页面头部添加搜索框
- 支持按应用名称和描述进行模糊搜索
- 实时过滤，无需点击搜索按钮
- 搜索框带清除按钮和搜索图标

### 2. 排序功能 ⭐⭐⭐⭐
- 支持按应用名称升序/降序排列
- 支持按应用类型排序
- 支持按应用版本排序
- 点击表头进行排序切换
- 排序图标动态显示

### 3. 添加应用 ⭐⭐⭐
- 创建新的应用实例
- 表单验证：应用名称、类型、版本、内容都为必填项
- 支持编辑现有应用
- 支持从应用商店导入应用

### 4. 编辑应用 ⭐⭐⭐
- 修改现有应用实例
- 表单预填充当前应用数据
- 保存后自动刷新列表

### 5. 删除应用 ⭐⭐⭐
- 带确认对话框的删除功能
- 显示警告图标和删除提示
- 删除后自动刷新列表
- 注意：已部署的应用需要先卸载部署

### 6. 查看详情 ⭐⭐⭐⭐
- 查看应用完整配置信息
- 预览应用内容（如 Docker Compose 文件）
- 查看应用类型和版本信息
- 支持代码语法高亮

### 7. 部署应用 ⭐⭐⭐⭐
- 从应用列表直接部署应用
- 跳转到部署页面或打开部署对话框
- 选择目标服务器进行部署

## 设计规范

- 使用项目统一的配色方案（主色 #4fc3f7）
- 表格行悬停效果
- 操作按钮使用图标按钮
- 弹窗使用圆角卡片设计
- 表单分组展示（基本信息、应用内容）
- 遵循 Squirrel 紧凑风格页面设计规范（调用 `compact-page-style` skill）

## 最佳实践

1. **列表布局**：使用表格行布局，不是方块卡片
2. **状态管理**：使用响应式数据管理弹窗显示状态
3. **错误处理**：API 调用添加 try-catch 处理
4. **国际化**：所有文本使用 $t() 函数包裹
5. **表单验证**：应用名称、类型、版本、内容都为必填项
6. **组件复用**：参考 `component-reusability` skill 设计可复用组件
7. **搜索优化**：使用 computed 实现实时过滤，避免不必要的重新渲染
8. **排序优化**：支持多字段排序，提供直观的排序图标
9. **应用类型**：使用不同图标和徽标区分应用类型
10. **内容编辑**：使用代码编辑器组件编辑应用内容，支持语法高亮

## 架构参考

- 遵循 Squirrel 运维平台前端布局架构（调用 `layout` skill）
- 使用 Vue3 + vue-i18n 国际化（调用 `vue-i18n-setup` skill）

## 与其他模块的关系

```
App Store（应用模板）
    │
    │ [导入/fork]
    ▼
Application（应用实例）
    │
    │ [部署到服务器]
    ▼
Deployment（部署实例）
    │
    └──> Server（目标服务器）
```

## 接口案例文件

- .trae\skills\application\api.md
