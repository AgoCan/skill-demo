---
name: "scripts"
description: "Squirrel 脚本管理页面开发规范。包含脚本列表、脚本编辑器、执行记录等组件的实现，以及 API 接口和国际化配置。在开发脚本管理功能时调用。"
---

# Squirrel 脚本管理页面开发规范

## 概述

脚本管理页面提供对系统脚本的增删改查和执行功能，包括脚本列表展示、脚本编辑器、脚本执行、执行记录查看等功能。

## 注意
点击添加新内容的时候，会导致页面不可操作问题，它应该和编辑的模式类似

## 目录结构

```
# 脚本管理页面模块
views/Scripts/
├── index.vue                 # 页面入口
├── components/
│   ├── ScriptTable.vue       # 脚本列表表格
│   ├── ScriptEditor.vue      # 脚本编辑器
│   ├── ExecuteDialog.vue     # 执行脚本对话框
│   └── ResultList.vue        # 执行结果列表
├── composables/
│   └── useScript.ts          # 脚本相关的可组合函数
├── types/
│   └── index.ts              # 页面类型定义
└── styles/
    └── scripts.scss          # 脚本相关组件样式

# API 接口模块
api/
└── script.ts                 # 脚本相关 API 接口

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
│   └── scripts.ts            # 脚本管理文本
└── en-US/
    ├── index.ts              # 英文语言包入口
    ├── common.ts             # 通用文本
    ├── layout.ts             # 布局文本
    └── scripts.ts            # 脚本管理文本

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

// 脚本类型
export interface Script {
  id: number
  name: string
  content: string
}

// 创建脚本请求
export interface CreateScriptRequest {
  name: string
  content: string
}

// 更新脚本请求
export interface UpdateScriptRequest {
  id: number
  name: string
  content: string
}

// 执行脚本请求
export interface ExecuteScriptRequest {
  script_id: number
  server_id: number
}

// 脚本执行结果
export interface ScriptResult {
  id: number
  task_id: number
  script_id: number
  server_id: number
  server_ip: string
  agent_port: number
  output: string
  status: string
  error_message: string
  created_at: string
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
// views/Scripts/types/index.ts

import type { Script, CreateScriptRequest, UpdateScriptRequest, ExecuteScriptRequest, ScriptResult } from '@/types'

export type { Script, CreateScriptRequest, UpdateScriptRequest, ExecuteScriptRequest, ScriptResult }

// 表单状态
export interface FormState {
  visible: boolean
  mode: 'create' | 'edit'
  data: CreateScriptRequest | UpdateScriptRequest | null
}

// 执行对话框状态
export interface ExecuteState {
  visible: boolean
  script: Script | null
}

// 结果列表状态
export interface ResultState {
  visible: boolean
  script: Script | null
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

### 脚本相关 API

```typescript
// api/script.ts
import { get, post, del } from '@/utils/request'
import type { Script, CreateScriptRequest, UpdateScriptRequest, ExecuteScriptRequest, ScriptResult } from '@/types'

/**
 * 获取脚本列表
 */
export function fetchScripts(): Promise<Script[]> {
  return get('/scripts')
}

/**
 * 获取脚本详情
 */
export function fetchScriptDetail(scriptId: number): Promise<Script> {
  return get(`/scripts/${scriptId}`)
}

/**
 * 创建脚本
 */
export function createScript(data: CreateScriptRequest): Promise<string> {
  return post('/scripts', data)
}

/**
 * 更新脚本
 */
export function updateScript(scriptId: number, data: UpdateScriptRequest): Promise<string> {
  return post(`/scripts/${scriptId}`, data)
}

/**
 * 删除脚本
 */
export function deleteScript(scriptId: number): Promise<string> {
  return del(`/scripts/${scriptId}`)
}

/**
 * 执行脚本
 */
export function executeScript(data: ExecuteScriptRequest): Promise<string> {
  return post('/scripts/execute', data)
}

/**
 * 获取脚本执行结果
 */
export function fetchScriptResults(scriptId: number): Promise<ScriptResult[]> {
  return get(`/scripts/${scriptId}/results`)
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
import scripts from './scripts'

export default {
  common,
  layout,
  scripts
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
  scripts: '脚本管理',
  configs: '配置管理',
  monitor: '监控',
  logout: '退出登录'
}
```

```typescript
// lang/zh-CN/scripts.ts
export default {
  title: '脚本管理',
  listTitle: '脚本列表',
  addScript: '添加脚本',
  editScript: '编辑脚本',
  deleteScript: '删除脚本',
  executeScript: '执行脚本',
  viewResults: '查看结果',
  scriptName: '脚本名称',
  scriptContent: '脚本内容',
  operation: '操作',
  save: '保存',
  cancel: '取消',
  confirm: '确认',
  confirmDelete: '确认删除',
  deleteWarning: '确定要删除脚本 "{name}" 吗？此操作不可恢复。',
  required: '必填项',
  createSuccess: '脚本创建成功',
  updateSuccess: '脚本更新成功',
  deleteSuccess: '脚本删除成功',
  operationFailed: '操作失败',
  scriptDetail: '脚本详情',
  basicInfo: '基本信息',
  searchPlaceholder: '搜索脚本名称...',
  execute: '执行',
  executeSuccess: '脚本执行任务已提交',
  executeFailed: '脚本执行失败',
  selectServer: '选择服务器',
  executionResults: '执行结果',
  taskId: '任务ID',
  serverIP: '服务器IP',
  status: '状态',
  output: '输出',
  errorMessage: '错误信息',
  createdAt: '创建时间',
  statusRunning: '运行中',
  statusSuccess: '成功',
  statusFailed: '失败',
  shebangRequired: '脚本内容必须以 shebang (#!) 开头',
  contentRequired: '脚本内容不能为空',
  nameRequired: '脚本名称不能为空'
}
```

### 英文语言包

```typescript
// lang/en-US/index.ts
import common from './common'
import layout from './layout'
import scripts from './scripts'

export default {
  common,
  layout,
  scripts
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
  scripts: 'Scripts',
  configs: 'Configs',
  monitor: 'Monitor',
  logout: 'Logout'
}
```

```typescript
// lang/en-US/scripts.ts
export default {
  title: 'Script Management',
  listTitle: 'Script List',
  addScript: 'Add Script',
  editScript: 'Edit Script',
  deleteScript: 'Delete Script',
  executeScript: 'Execute Script',
  viewResults: 'View Results',
  scriptName: 'Script Name',
  scriptContent: 'Script Content',
  operation: 'Operation',
  save: 'Save',
  cancel: 'Cancel',
  confirm: 'Confirm',
  confirmDelete: 'Confirm Delete',
  deleteWarning: 'Are you sure you want to delete script "{name}"? This action cannot be undone.',
  required: 'Required',
  createSuccess: 'Script created successfully',
  updateSuccess: 'Script updated successfully',
  deleteSuccess: 'Script deleted successfully',
  operationFailed: 'Operation failed',
  scriptDetail: 'Script Detail',
  basicInfo: 'Basic Info',
  searchPlaceholder: 'Search script name...',
  execute: 'Execute',
  executeSuccess: 'Script execution task submitted',
  executeFailed: 'Script execution failed',
  selectServer: 'Select Server',
  executionResults: 'Execution Results',
  taskId: 'Task ID',
  serverIP: 'Server IP',
  status: 'Status',
  output: 'Output',
  errorMessage: 'Error Message',
  createdAt: 'Created At',
  statusRunning: 'Running',
  statusSuccess: 'Success',
  statusFailed: 'Failed',
  shebangRequired: 'Script content must start with shebang (#!)',
  contentRequired: 'Script content cannot be empty',
  nameRequired: 'Script name cannot be empty'
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
    path: '/scripts',
    name: 'Scripts',
    component: () => import('@/views/Scripts/index.vue')
  },
  {
    path: '/applications',
    name: 'Applications',
    component: () => import('@/views/Application/index.vue')
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

### ScriptTable 组件

- 使用表格行布局展示脚本列表
- 显示脚本名称
- 操作按钮：编辑、删除、执行、查看结果
- 脚本名称过长时使用省略号显示

```vue
<template>
  <div class="script-table-container">
    <table class="script-table">
      <thead>
        <tr>
          <th>{{ $t('scripts.scriptName') }}</th>
          <th>{{ $t('scripts.operation') }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="script in scripts" :key="script.id" class="script-row">
          <td class="name-cell">
            <div class="name-wrapper">
              <Icon icon="lucide:file-code" class="name-icon" />
              <span class="name-text" :title="script.name">{{ script.name }}</span>
            </div>
          </td>
          <td class="action-cell">
            <button class="action-btn edit-btn" :title="$t('scripts.editScript')" @click="$emit('edit', script)">
              <Icon icon="lucide:edit-2" />
            </button>
            <button class="action-btn execute-btn" :title="$t('scripts.executeScript')" @click="$emit('execute', script)">
              <Icon icon="lucide:play" />
            </button>
            <button class="action-btn result-btn" :title="$t('scripts.viewResults')" @click="$emit('viewResults', script)">
              <Icon icon="lucide:list" />
            </button>
            <button class="action-btn delete-btn" :title="$t('scripts.deleteScript')" @click="$emit('delete', script)">
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
import type { Script } from '@/types'

const props = defineProps<{
  scripts: Script[]
}>()

defineEmits<{
  edit: [script: Script]
  delete: [script: Script]
  execute: [script: Script]
  viewResults: [script: Script]
}>()
</script>
```

### ScriptEditor 组件

- 支持添加和编辑两种模式
- 脚本名称：文本输入框，必填
- 脚本内容：代码编辑器，必填，支持多行文本
- 表单验证：必填项验证、shebang 验证

### ExecuteDialog 组件

- 执行脚本对话框
- 选择服务器下拉框
- 显示脚本名称和内容预览
- 确认/取消按钮

### ResultList 组件

- 显示脚本执行结果列表
- 显示任务ID、服务器IP、状态、输出、创建时间
- 状态标签：运行中（黄色）、成功（绿色）、失败（红色）

## 主页面逻辑

```typescript
// 主要功能
const scripts = ref<Script[]>([])
const showEditor = ref(false)
const showExecuteDialog = ref(false)
const showResultList = ref(false)
const searchKeyword = ref('')
const toastVisible = ref(false)
const toastMessage = ref('')

// 过滤
const filteredScripts = computed(() => {
  if (!searchKeyword.value) {
    return scripts.value
  }
  const keyword = searchKeyword.value.toLowerCase()
  return scripts.value.filter(script => 
    script.name.toLowerCase().includes(keyword)
  )
})

// 加载脚本列表
const loadScripts = async () => {
  scripts.value = await fetchScripts()
}

// 表单提交
const handleFormSubmit = async (data: CreateScriptRequest | UpdateScriptRequest) => {
  if (editingScript.value) {
    await updateScript(editingScript.value.id, data as UpdateScriptRequest)
  } else {
    await createScript(data as CreateScriptRequest)
  }
  await loadScripts()
}

// 执行脚本
const handleExecute = async (script: Script) => {
  selectedScript.value = script
  showExecuteDialog.value = true
}

// 确认执行
const confirmExecute = async (serverId: number) => {
  if (!selectedScript.value) return
  await executeScript({
    script_id: selectedScript.value.id,
    server_id: serverId
  })
  toastMessage.value = t('scripts.executeSuccess')
  toastVisible.value = true
  setTimeout(() => {
    toastVisible.value = false
  }, 2000)
}

// 查看结果
const handleViewResults = async (script: Script) => {
  selectedScript.value = script
  results.value = await fetchScriptResults(script.id)
  showResultList.value = true
}

// 删除确认
const confirmDelete = async () => {
  await deleteScript(deletingScript.value.id)
  await loadScripts()
}
```

## 功能特性

### 1. 模糊搜索 ⭐⭐⭐⭐
- 在页面头部添加搜索框
- 支持按脚本名称进行模糊搜索
- 实时过滤，无需点击搜索按钮
- 搜索框带清除按钮和搜索图标

### 2. 脚本编辑器 ⭐⭐⭐⭐
- 支持语法高亮的代码编辑器
- 支持多行文本编辑
- 自动检测 shebang
- 表单验证：脚本名称和脚本内容都为必填项

### 3. 执行脚本 ⭐⭐⭐⭐
- 选择服务器执行脚本
- 显示脚本名称和内容预览
- 提交执行任务到 Agent
- 显示执行结果

### 4. 执行记录 ⭐⭐⭐⭐
- 查看脚本执行历史记录
- 显示任务ID、服务器IP、状态、输出、创建时间
- 状态标签：运行中（黄色）、成功（绿色）、失败（红色）

### 5. 添加脚本 ⭐⭐⭐
- 创建新的脚本
- 表单验证：脚本名称和脚本内容都为必填项
- 脚本内容必须以 shebang (#!) 开头
- 支持编辑现有脚本

### 6. 编辑脚本 ⭐⭐⭐
- 修改现有脚本
- 表单预填充当前脚本数据
- 保存后自动刷新列表

### 7. 删除脚本 ⭐⭐⭐
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
5. **表单验证**：脚本名称和脚本内容都为必填项，脚本内容必须以 shebang 开头
6. **组件复用**：参考 `component-reusability` skill 设计可复用组件
7. **搜索优化**：使用 computed 实现实时过滤，避免不必要的重新渲染
8. **代码编辑器**：使用专业的代码编辑器组件，支持语法高亮

## 架构参考

- 遵循 Squirrel 运维平台前端布局架构（调用 `layout` skill）
- 使用 Vue3 + vue-i18n 国际化（调用 `vue-i18n-setup` skill）

## 接口案例文件

- .trae\skills\scripts\api.md
