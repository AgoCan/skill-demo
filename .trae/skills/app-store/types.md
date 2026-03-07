# 类型定义参考

## 目录

- [全局类型定义](#全局类型定义)
- [页面类型定义](#页面类型定义)
- [HTTP 请求封装](#http-请求封装)

## 全局类型定义

```typescript
// types/index.ts

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

export interface ApiResponse<T = any> {
  code: number
  message: string
  data: T
}
```

## 页面类型定义

```typescript
// views/AppStore/types/index.ts

import type { AppStore, CreateAppRequest, UpdateAppRequest } from '@/types'

export type { AppStore, CreateAppRequest, UpdateAppRequest }

export interface FormState {
  visible: boolean
  mode: 'create' | 'edit'
  data: CreateAppRequest | UpdateAppRequest | null
}

export interface DetailState {
  visible: boolean
  app: AppStore | null
}

export interface DeleteState {
  visible: boolean
  app: AppStore | null
}

export interface FilterState {
  category?: string
  type?: string
  keyword?: string
}
```

## HTTP 请求封装

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
