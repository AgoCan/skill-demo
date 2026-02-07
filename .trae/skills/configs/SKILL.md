---
name: "configs"
description: "Squirrel 配置管理页面开发规范。包含配置项列表、表单、搜索、排序、复制等组件的实现，以及 API 接口和国际化配置。在开发配置管理功能时调用。"
---

# Squirrel 配置管理页面开发规范

## 概述

配置管理页面提供对系统配置项的增删改查功能，包括配置列表展示、添加/编辑配置、搜索过滤、排序、快速复制等功能。

## 目录结构

```
# 配置管理页面模块
views/Configs/
├── index.vue                 # 页面入口
├── components/
│   ├── ConfigTable.vue       # 配置列表表格
│   ├── ConfigForm.vue        # 添加/编辑表单
│   └── DeleteConfirm.vue     # 删除确认弹窗
├── composables/
│   └── useConfig.ts          # 配置相关的可组合函数
├── types/
│   └── index.ts              # 页面类型定义
└── styles/
    └── configs.scss          # 配置相关组件样式

# API 接口模块
api/
└── config.ts                 # 配置相关 API 接口

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
│   └── configs.ts            # 配置管理文本
└── en-US/
    ├── index.ts              # 英文语言包入口
    ├── common.ts             # 通用文本
    ├── layout.ts             # 布局文本
    └── configs.ts            # 配置管理文本

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

// 配置项类型
export interface Config {
  id: number
  key: string
  value: string
}

// 创建配置请求
export interface CreateConfigRequest {
  key: string
  value: string
}

// 更新配置请求
export interface UpdateConfigRequest {
  id: number
  key: string
  value: string
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
// views/Configs/types/index.ts

import type { Config, CreateConfigRequest, UpdateConfigRequest } from '@/types'

export type { Config, CreateConfigRequest, UpdateConfigRequest }

// 表单状态
export interface FormState {
  visible: boolean
  mode: 'create' | 'edit'
  data: CreateConfigRequest | UpdateConfigRequest | null
}

// 删除确认状态
export interface DeleteState {
  visible: boolean
  config: Config | null
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

### 配置相关 API

```typescript
// api/config.ts
import { get, post, del } from '@/utils/request'
import type { Config, CreateConfigRequest, UpdateConfigRequest } from '@/types'

/**
 * 获取配置列表
 */
export function fetchConfigs(): Promise<Config[]> {
  return get('/config')
}

/**
 * 获取配置详情
 */
export function fetchConfigDetail(configId: number): Promise<Config> {
  return get(`/config/${configId}`)
}

/**
 * 创建配置
 */
export function createConfig(data: CreateConfigRequest): Promise<string> {
  return post('/config', data)
}

/**
 * 更新配置
 */
export function updateConfig(configId: number, data: UpdateConfigRequest): Promise<string> {
  return post(`/config/${configId}`, data)
}

/**
 * 删除配置
 */
export function deleteConfig(configId: number): Promise<string> {
  return del(`/config/${configId}`)
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
import configs from './configs'

export default {
  common,
  layout,
  configs
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
  configs: '配置管理',
  monitor: '监控',
  logout: '退出登录'
}
```

```typescript
// lang/zh-CN/configs.ts
export default {
  title: '配置管理',
  listTitle: '配置列表',
  addConfig: '添加配置',
  editConfig: '编辑配置',
  deleteConfig: '删除配置',
  viewDetail: '查看详情',
  configKey: '配置键',
  configValue: '配置值',
  operation: '操作',
  save: '保存',
  cancel: '取消',
  confirm: '确认',
  confirmDelete: '确认删除',
  deleteWarning: '确定要删除配置项 "{key}" 吗？此操作不可恢复。',
  required: '必填项',
  createSuccess: '配置创建成功',
  updateSuccess: '配置更新成功',
  deleteSuccess: '配置删除成功',
  operationFailed: '操作失败',
  configDetail: '配置详情',
  basicInfo: '基本信息',
  searchPlaceholder: '搜索配置键或值...',
  copy: '复制',
  copySuccess: '已复制到剪贴板',
  sortAsc: '升序',
  sortDesc: '降序'
}
```

### 英文语言包

```typescript
// lang/en-US/index.ts
import common from './common'
import layout from './layout'
import configs from './configs'

export default {
  common,
  layout,
  configs
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
  configs: 'Configs',
  monitor: 'Monitor',
  logout: 'Logout'
}
```

```typescript
// lang/en-US/configs.ts
export default {
  title: 'Config Management',
  listTitle: 'Config List',
  addConfig: 'Add Config',
  editConfig: 'Edit Config',
  deleteConfig: 'Delete Config',
  viewDetail: 'View Detail',
  configKey: 'Config Key',
  configValue: 'Config Value',
  operation: 'Operation',
  save: 'Save',
  cancel: 'Cancel',
  confirm: 'Confirm',
  confirmDelete: 'Confirm Delete',
  deleteWarning: 'Are you sure you want to delete config "{key}"? This action cannot be undone.',
  required: 'Required',
  createSuccess: 'Config created successfully',
  updateSuccess: 'Config updated successfully',
  deleteSuccess: 'Config deleted successfully',
  operationFailed: 'Operation failed',
  configDetail: 'Config Detail',
  basicInfo: 'Basic Info',
  searchPlaceholder: 'Search config key or value...',
  copy: 'Copy',
  copySuccess: 'Copied to clipboard',
  sortAsc: 'Ascending',
  sortDesc: 'Descending'
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

### ConfigTable 组件

- 使用表格行布局展示配置列表
- 显示配置键、配置值
- 支持排序功能（点击表头切换升序/降序）
- 操作按钮：复制、编辑、删除
- 配置值过长时使用省略号显示

```vue
<template>
  <div class="config-table-container">
    <table class="config-table">
      <thead>
        <tr>
          <th class="sortable" @click="$emit('sort', 'key')">
            {{ $t('configs.configKey') }}
            <Icon :icon="getSortIcon('key')" class="sort-icon" />
          </th>
          <th class="sortable" @click="$emit('sort', 'value')">
            {{ $t('configs.configValue') }}
            <Icon :icon="getSortIcon('value')" class="sort-icon" />
          </th>
          <th>{{ $t('configs.operation') }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="config in configs" :key="config.id" class="config-row">
          <td class="key-cell">
            <div class="key-wrapper">
              <Icon icon="lucide:key" class="key-icon" />
              <span class="key-text">{{ config.key }}</span>
            </div>
          </td>
          <td class="value-cell">
            <div class="value-wrapper">
              <span class="value-text" :title="config.value">{{ config.value }}</span>
            </div>
          </td>
          <td class="action-cell">
            <button class="action-btn copy-btn" :title="$t('configs.copy')" @click="$emit('copy', config.value)">
              <Icon icon="lucide:copy" />
            </button>
            <button class="action-btn edit-btn" :title="$t('configs.editConfig')" @click="$emit('edit', config)">
              <Icon icon="lucide:edit-2" />
            </button>
            <button class="action-btn delete-btn" :title="$t('configs.deleteConfig')" @click="$emit('delete', config)">
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
import type { Config } from '@/types'

const props = defineProps<{
  configs: Config[]
  sortBy?: string | null
  sortOrder?: 'asc' | 'desc'
}>()

defineEmits<{
  edit: [config: Config]
  delete: [config: Config]
  copy: [value: string]
  sort: [field: string]
}>()

const getSortIcon = (field: string) => {
  if (field !== props.sortBy) return 'lucide:chevrons-up-down'
  return props.sortOrder === 'asc' ? 'lucide:chevron-up' : 'lucide:chevron-down'
}
</script>
```

### ConfigForm 组件

- 支持添加和编辑两种模式
- 配置键：文本输入框，必填
- 配置值：文本输入框，必填，支持多行文本
- 表单验证：必填项验证

### DeleteConfirm 组件

- 删除确认弹窗
- 显示警告图标和删除提示
- 确认/取消按钮

## 主页面逻辑

```typescript
// 主要功能
const configs = ref<Config[]>([])
const showForm = ref(false)
const showDeleteConfirm = ref(false)
const searchKeyword = ref('')
const sortBy = ref<string | null>(null)
const sortOrder = ref<'asc' | 'desc'>('asc')
const toastVisible = ref(false)
const toastMessage = ref('')

// 过滤和排序
const filteredConfigs = computed(() => {
  let result = configs.value

  if (searchKeyword.value) {
    const keyword = searchKeyword.value.toLowerCase()
    result = result.filter(config => 
      config.key.toLowerCase().includes(keyword) ||
      config.value.toLowerCase().includes(keyword)
    )
  }

  if (sortBy.value) {
    result = [...result].sort((a, b) => {
      const aValue = a[sortBy.value as keyof Config]
      const bValue = b[sortBy.value as keyof Config]
      const comparison = String(aValue).localeCompare(String(bValue))
      return sortOrder.value === 'asc' ? comparison : -comparison
    })
  }

  return result
})

// 加载配置列表
const loadConfigs = async () => {
  configs.value = await fetchConfigs()
}

// 表单提交
const handleFormSubmit = async (data: CreateConfigRequest | UpdateConfigRequest) => {
  if (editingConfig.value) {
    await updateConfig(editingConfig.value.id, data as UpdateConfigRequest)
  } else {
    await createConfig(data as CreateConfigRequest)
  }
  await loadConfigs()
}

// 复制配置值
const handleCopy = async (value: string) => {
  try {
    await navigator.clipboard.writeText(value)
    toastMessage.value = t('configs.copySuccess')
    toastVisible.value = true
    setTimeout(() => {
      toastVisible.value = false
    }, 2000)
  } catch (err) {
    console.error('Failed to copy:', err)
  }
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
  await deleteConfig(deletingConfig.value.id)
  await loadConfigs()
}
```

## 功能特性

### 1. 模糊搜索 ⭐⭐⭐⭐
- 在页面头部添加搜索框
- 支持按配置键和配置值进行模糊搜索
- 实时过滤，无需点击搜索按钮
- 搜索框带清除按钮和搜索图标

### 2. 快速复制 ⭐⭐⭐⭐
- 在操作列添加复制按钮
- 点击后自动复制配置值到剪贴板
- 显示 Toast 提示"已复制到剪贴板"
- 2 秒后自动消失

### 3. 排序功能 ⭐⭐⭐⭐
- 支持按配置键升序/降序排列
- 支持按配置值排序
- 点击表头进行排序切换
- 排序图标动态显示

### 4. 添加配置 ⭐⭐⭐
- 创建新的配置项
- 表单验证：配置键和配置值都为必填项
- 支持编辑现有配置

### 5. 编辑配置 ⭐⭐⭐
- 修改现有配置项
- 表单预填充当前配置数据
- 保存后自动刷新列表

### 6. 删除配置 ⭐⭐⭐
- 带确认对话框的删除功能
- 显示警告图标和删除提示
- 删除后自动刷新列表

## 设计规范

- 使用项目统一的配色方案（主色 #4fc3f7）
- 表格行悬停效果
- 操作按钮使用图标按钮
- 弹窗使用圆角卡片设计
- 表单分组展示（基本信息）
- 遵循 Squirrel 紧凑风格页面设计规范（调用 `compact-page-style` skill）

## 最佳实践

1. **列表布局**：使用表格行布局，不是方块卡片
2. **状态管理**：使用响应式数据管理弹窗显示状态
3. **错误处理**：API 调用添加 try-catch 处理
4. **国际化**：所有文本使用 $t() 函数包裹
5. **表单验证**：配置键和配置值都为必填项
6. **组件复用**：参考 `component-reusability` skill 设计可复用组件
7. **搜索优化**：使用 computed 实现实时过滤，避免不必要的重新渲染
8. **排序优化**：支持多字段排序，提供直观的排序图标

## 架构参考

- 遵循 Squirrel 运维平台前端布局架构（调用 `layout` skill）
- 使用 Vue3 + vue-i18n 国际化（调用 `vue-i18n-setup` skill）

## 接口案例文件

- .trae\skills\configs\api.md
