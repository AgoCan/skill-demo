---
name: "app-store"
description: "Squirrel 应用商店页面开发规范。包含应用模板列表、表单、搜索、分类、详情等组件的实现，以及 API 接口和国际化配置。在开发应用商店功能时调用。"
---

# Squirrel 应用商店页面开发规范

## 概述

应用商店页面提供对应用模板的管理功能，包括应用模板列表展示、添加/编辑模板、搜索过滤、分类筛选、查看详情、下载部署等功能。支持多种模板类型：Docker Compose、K8s Manifest、Helm Chart。

## 目录结构

```
# 应用商店页面模块
views/AppStore/
├── index.vue                 # 页面入口
├── components/
│   ├── AppTable.vue          # 应用列表表格
│   ├── AppForm.vue           # 添加/编辑表单
│   ├── AppDetail.vue         # 应用详情弹窗
│   ├── DeleteConfirm.vue     # 删除确认弹窗
│   └── CategoryFilter.vue    # 分类筛选组件
├── composables/
│   └── useAppStore.ts        # 应用商店相关的可组合函数
├── types/
│   └── index.ts              # 页面类型定义
└── styles/
    └── app-store.scss        # 应用商店相关组件样式

# API 接口模块
api/
└── app-store.ts              # 应用商店相关 API 接口

# 通用组件模块
components/
├── Button.vue                # 按钮组件
├── Loading.vue               # 加载组件
├── Empty.vue                 # 空状态组件
├── PageHeader.vue            # 页面头部组件
├── Modal.vue                 # 模态框组件
├── Toast.vue                 # 提示组件
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
│   └── app-store.ts          # 应用商店文本
└── en-US/
    ├── index.ts              # 英文语言包入口
    ├── common.ts             # 通用文本
    ├── layout.ts             # 布局文本
    └── app-store.ts          # 应用商店文本

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

// 应用模板类型
export interface AppStore {
  id: number
  name: string
  description: string
  type: 'compose' | 'k8s_manifest' | 'helm_chart'
  category: 'web' | 'database' | 'middleware' | 'devops'
  icon?: string
  version: string
  content: string
  tags: string
  author: string
  repo_url?: string
  homepage_url?: string
  is_official: boolean
  downloads: number
  status: 'active' | 'deprecated'
}

// 创建应用请求
export interface CreateAppRequest {
  name: string
  description: string
  type: 'compose' | 'k8s_manifest' | 'helm_chart'
  category: 'web' | 'database' | 'middleware' | 'devops'
  icon?: string
  version: string
  content: string
  tags: string
  author: string
  repo_url?: string
  homepage_url?: string
  is_official: boolean
}

// 更新应用请求
export interface UpdateAppRequest {
  id: number
  name: string
  description: string
  type: 'compose' | 'k8s_manifest' | 'helm_chart'
  category: 'web' | 'database' | 'middleware' | 'devops'
  icon?: string
  version: string
  content: string
  tags: string
  author: string
  repo_url?: string
  homepage_url?: string
  is_official: boolean
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
// views/AppStore/types/index.ts

import type { AppStore, CreateAppRequest, UpdateAppRequest } from '@/types'

export type { AppStore, CreateAppRequest, UpdateAppRequest }

// 表单状态
export interface FormState {
  visible: boolean
  mode: 'create' | 'edit'
  data: CreateAppRequest | UpdateAppRequest | null
}

// 详情状态
export interface DetailState {
  visible: boolean
  app: AppStore | null
}

// 删除确认状态
export interface DeleteState {
  visible: boolean
  app: AppStore | null
}

// 筛选状态
export interface FilterState {
  category?: string
  type?: string
  keyword?: string
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

### 应用商店相关 API

```typescript
// api/app-store.ts
import { get, post, del } from '@/utils/request'
import type { AppStore, CreateAppRequest, UpdateAppRequest } from '@/types'

/**
 * 获取应用列表
 */
export function fetchApps(): Promise<AppStore[]> {
  return get('/app-store')
}

/**
 * 获取应用详情
 */
export function fetchAppDetail(appId: number): Promise<AppStore> {
  return get(`/app-store/${appId}`)
}

/**
 * 创建应用
 */
export function createApp(data: CreateAppRequest): Promise<string> {
  return post('/app-store', data)
}

/**
 * 更新应用
 */
export function updateApp(appId: number, data: UpdateAppRequest): Promise<string> {
  return post(`/app-store/${appId}`, data)
}

/**
 * 删除应用
 */
export function deleteApp(appId: number): Promise<string> {
  return del(`/app-store/${appId}`)
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
import appStore from './app-store'

export default {
  common,
  layout,
  appStore
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
// lang/zh-CN/app-store.ts
export default {
  title: '应用商店',
  listTitle: '应用列表',
  addApp: '添加应用',
  editApp: '编辑应用',
  deleteApp: '删除应用',
  viewDetail: '查看详情',
  appName: '应用名称',
  description: '描述',
  type: '类型',
  category: '分类',
  icon: '图标',
  version: '版本',
  tags: '标签',
  author: '作者',
  repoUrl: '仓库地址',
  homepageUrl: '主页地址',
  isOfficial: '官方',
  downloads: '下载次数',
  status: '状态',
  content: '模板内容',
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
  appDetail: '应用详情',
  basicInfo: '基本信息',
  templateContent: '模板内容',
  searchPlaceholder: '搜索应用名称、标签...',
  copy: '复制',
  copySuccess: '已复制到剪贴板',
  deploy: '部署',
  download: '下载',
  allCategory: '全部分类',
  allType: '全部类型',
  typeCompose: 'Docker Compose',
  typeK8s: 'K8s Manifest',
  typeHelm: 'Helm Chart',
  categoryWeb: 'Web应用',
  categoryDatabase: '数据库',
  categoryMiddleware: '中间件',
  categoryDevops: 'DevOps',
  statusActive: '活跃',
  statusDeprecated: '已弃用'
}
```

### 英文语言包

```typescript
// lang/en-US/index.ts
import common from './common'
import layout from './layout'
import appStore from './app-store'

export default {
  common,
  layout,
  appStore
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
// lang/en-US/app-store.ts
export default {
  title: 'App Store',
  listTitle: 'App List',
  addApp: 'Add App',
  editApp: 'Edit App',
  deleteApp: 'Delete App',
  viewDetail: 'View Detail',
  appName: 'App Name',
  description: 'Description',
  type: 'Type',
  category: 'Category',
  icon: 'Icon',
  version: 'Version',
  tags: 'Tags',
  author: 'Author',
  repoUrl: 'Repository URL',
  homepageUrl: 'Homepage URL',
  isOfficial: 'Official',
  downloads: 'Downloads',
  status: 'Status',
  content: 'Template Content',
  operation: 'Operation',
  save: 'Save',
  cancel: 'Cancel',
  confirm: 'Confirm',
  confirmDelete: 'Confirm Delete',
  deleteWarning: 'Are you sure you want to delete app "{name}"? This action cannot be undone.',
  required: 'Required',
  createSuccess: 'App created successfully',
  updateSuccess: 'App updated successfully',
  deleteSuccess: 'App deleted successfully',
  operationFailed: 'Operation failed',
  appDetail: 'App Detail',
  basicInfo: 'Basic Info',
  templateContent: 'Template Content',
  searchPlaceholder: 'Search app name, tags...',
  copy: 'Copy',
  copySuccess: 'Copied to clipboard',
  deploy: 'Deploy',
  download: 'Download',
  allCategory: 'All Categories',
  allType: 'All Types',
  typeCompose: 'Docker Compose',
  typeK8s: 'K8s Manifest',
  typeHelm: 'Helm Chart',
  categoryWeb: 'Web Apps',
  categoryDatabase: 'Database',
  categoryMiddleware: 'Middleware',
  categoryDevops: 'DevOps',
  statusActive: 'Active',
  statusDeprecated: 'Deprecated'
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

### AppTable 组件

- 使用表格行布局展示应用列表
- 显示应用图标、名称、描述、类型、分类、版本、下载次数、状态
- 支持按分类和类型筛选
- 操作按钮：查看详情、编辑、删除
- 官方应用显示官方徽章

```vue
<template>
  <div class="app-table-container">
    <table class="app-table">
      <thead>
        <tr>
          <th>{{ $t('appStore.appName') }}</th>
          <th>{{ $t('appStore.type') }}</th>
          <th>{{ $t('appStore.category') }}</th>
          <th>{{ $t('appStore.version') }}</th>
          <th>{{ $t('appStore.downloads') }}</th>
          <th>{{ $t('appStore.status') }}</th>
          <th>{{ $t('appStore.operation') }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="app in apps" :key="app.id" class="app-row">
          <td class="name-cell">
            <div class="name-wrapper">
              <img v-if="app.icon" :src="app.icon" class="app-icon" />
              <Icon v-else icon="lucide:package" class="app-icon-default" />
              <div class="name-info">
                <div class="name-text">
                  {{ app.name }}
                  <span v-if="app.is_official" class="official-badge">
                    {{ $t('appStore.isOfficial') }}
                  </span>
                </div>
                <div class="description-text">{{ app.description }}</div>
              </div>
            </div>
          </td>
          <td class="type-cell">
            <span class="type-tag" :class="`type-${app.type}`">
              {{ getTypeLabel(app.type) }}
            </span>
          </td>
          <td class="category-cell">
            <span class="category-tag">
              {{ getCategoryLabel(app.category) }}
            </span>
          </td>
          <td class="version-cell">
            <span class="version-text">{{ app.version }}</span>
          </td>
          <td class="downloads-cell">
            <Icon icon="lucide:download" class="downloads-icon" />
            <span>{{ app.downloads }}</span>
          </td>
          <td class="status-cell">
            <span class="status-badge" :class="`status-${app.status}`">
              {{ getStatusLabel(app.status) }}
            </span>
          </td>
          <td class="action-cell">
            <button class="action-btn detail-btn" :title="$t('appStore.viewDetail')" @click="$emit('detail', app)">
              <Icon icon="lucide:eye" />
            </button>
            <button class="action-btn edit-btn" :title="$t('appStore.editApp')" @click="$emit('edit', app)">
              <Icon icon="lucide:edit-2" />
            </button>
            <button class="action-btn delete-btn" :title="$t('appStore.deleteApp')" @click="$emit('delete', app)">
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
import { useI18n } from 'vue-i18n'
import type { AppStore } from '@/types'

const props = defineProps<{
  apps: AppStore[]
}>()

defineEmits<{
  detail: [app: AppStore]
  edit: [app: AppStore]
  delete: [app: AppStore]
}>()

const { t } = useI18n()

const getTypeLabel = (type: string) => {
  const labels: Record<string, string> = {
    compose: t('appStore.typeCompose'),
    k8s_manifest: t('appStore.typeK8s'),
    helm_chart: t('appStore.typeHelm')
  }
  return labels[type] || type
}

const getCategoryLabel = (category: string) => {
  const labels: Record<string, string> = {
    web: t('appStore.categoryWeb'),
    database: t('appStore.categoryDatabase'),
    middleware: t('appStore.categoryMiddleware'),
    devops: t('appStore.categoryDevops')
  }
  return labels[category] || category
}

const getStatusLabel = (status: string) => {
  const labels: Record<string, string> = {
    active: t('appStore.statusActive'),
    deprecated: t('appStore.statusDeprecated')
  }
  return labels[status] || status
}
</script>
```

### AppForm 组件

- 支持添加和编辑两种模式
- 应用名称：文本输入框，必填
- 描述：文本域，必填
- 类型：下拉选择，必填（compose/k8s_manifest/helm_chart）
- 分类：下拉选择，必填（web/database/middleware/devops）
- 图标：URL 输入框，可选
- 版本：文本输入框，必填
- 标签：文本输入框，逗号分隔
- 作者：文本输入框，必填
- 仓库地址：URL 输入框，可选
- 主页地址：URL 输入框，可选
- 是否官方：复选框
- 模板内容：代码编辑器，必填

### AppDetail 组件

- 显示应用完整信息
- 基本信息分组：名称、描述、类型、分类、版本、标签、作者
- 链接信息：仓库地址、主页地址
- 统计信息：下载次数、官方标识
- 模板内容：代码展示区域，支持复制

### DeleteConfirm 组件

- 删除确认弹窗
- 显示警告图标和删除提示
- 确认/取消按钮

### CategoryFilter 组件

- 分类筛选下拉菜单
- 支持全部分类选项
- 类型筛选下拉菜单
- 支持全部类型选项

## 主页面逻辑

```typescript
// 主要功能
const apps = ref<AppStore[]>([])
const showForm = ref(false)
const showDetail = ref(false)
const showDeleteConfirm = ref(false)
const searchKeyword = ref('')
const selectedCategory = ref<string>('')
const selectedType = ref<string>('')
const toastVisible = ref(false)
const toastMessage = ref('')

// 过滤和搜索
const filteredApps = computed(() => {
  let result = apps.value

  if (searchKeyword.value) {
    const keyword = searchKeyword.value.toLowerCase()
    result = result.filter(app => 
      app.name.toLowerCase().includes(keyword) ||
      app.description.toLowerCase().includes(keyword) ||
      app.tags.toLowerCase().includes(keyword)
    )
  }

  if (selectedCategory.value) {
    result = result.filter(app => app.category === selectedCategory.value)
  }

  if (selectedType.value) {
    result = result.filter(app => app.type === selectedType.value)
  }

  return result
})

// 加载应用列表
const loadApps = async () => {
  apps.value = await fetchApps()
}

// 表单提交
const handleFormSubmit = async (data: CreateAppRequest | UpdateAppRequest) => {
  if (editingApp.value) {
    await updateApp(editingApp.value.id, data as UpdateAppRequest)
  } else {
    await createApp(data as CreateAppRequest)
  }
  await loadApps()
}

// 删除确认
const confirmDelete = async () => {
  await deleteApp(deletingApp.value.id)
  await loadApps()
}

// 分类筛选
const handleCategoryChange = (category: string) => {
  selectedCategory.value = category
}

// 类型筛选
const handleTypeChange = (type: string) => {
  selectedType.value = type
}
```

## 功能特性

### 1. 模糊搜索 ⭐⭐⭐⭐
- 在页面头部添加搜索框
- 支持按应用名称、描述、标签进行模糊搜索
- 实时过滤，无需点击搜索按钮
- 搜索框带清除按钮和搜索图标

### 2. 分类筛选 ⭐⭐⭐⭐
- 按分类筛选应用（Web应用、数据库、中间件、DevOps）
- 支持全部分类选项
- 下拉菜单选择，带图标显示

### 3. 类型筛选 ⭐⭐⭐⭐
- 按类型筛选应用（Docker Compose、K8s Manifest、Helm Chart）
- 支持全部类型选项
- 下拉菜单选择

### 4. 官方标识 ⭐⭐⭐⭐
- 官方应用显示官方徽章
- 徽章使用醒目的颜色和样式

### 5. 查看详情 ⭐⭐⭐⭐
- 点击详情按钮查看应用完整信息
- 弹窗展示基本信息、链接信息、统计信息
- 模板内容代码展示，支持复制

### 6. 添加应用 ⭐⭐⭐
- 创建新的应用模板
- 表单验证：名称、描述、类型、分类、版本、作者、内容都为必填项
- 支持编辑现有应用

### 7. 编辑应用 ⭐⭐⭐
- 修改现有应用模板
- 表单预填充当前应用数据
- 保存后自动刷新列表

### 8. 删除应用 ⭐⭐⭐
- 带确认对话框的删除功能
- 显示警告图标和删除提示
- 删除后自动刷新列表

### 9. 下载统计 ⭐⭐⭐
- 显示应用下载次数
- 带下载图标

### 10. 状态显示 ⭐⭐⭐
- 显示应用状态（活跃/已弃用）
- 使用不同颜色区分状态

## 设计规范

- 使用项目统一的配色方案（主色 #4fc3f7）
- 表格行悬停效果
- 应用图标使用圆形或圆角矩形
- 操作按钮使用图标按钮
- 弹窗使用圆角卡片设计
- 表单分组展示（基本信息、链接信息、模板内容）
- 官方徽章使用醒目的颜色（如金色）
- 类型标签使用不同颜色区分
- 状态徽章使用绿色（活跃）和灰色（已弃用）
- 遵循 Squirrel 紧凑风格页面设计规范（调用 `compact-page-style` skill）

## 最佳实践

1. **列表布局**：使用表格行布局，不是方块卡片
2. **状态管理**：使用响应式数据管理弹窗显示状态
3. **错误处理**：API 调用添加 try-catch 处理
4. **国际化**：所有文本使用 $t() 函数包裹
5. **表单验证**：名称、描述、类型、分类、版本、作者、内容都为必填项
6. **组件复用**：参考 `component-reusability` skill 设计可复用组件
7. **搜索优化**：使用 computed 实现实时过滤，避免不必要的重新渲染
8. **筛选优化**：支持多条件筛选，提供直观的筛选器
9. **图标展示**：应用图标优先显示，无图标时使用默认图标
10. **标签展示**：标签使用逗号分隔，可考虑使用标签组件展示

## 架构参考

- 遵循 Squirrel 运维平台前端布局架构（调用 `layout` skill）
- 使用 Vue3 + vue-i18n 国际化（调用 `vue-i18n-setup` skill）

## 接口案例文件

- .trae\skills\app-store\api.md
