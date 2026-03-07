# Configs 类型定义

## 全局类型定义

```typescript
// types/index.ts

export interface Config {
  id: number
  key: string
  value: string
}

export interface CreateConfigRequest {
  key: string
  value: string
}

export interface UpdateConfigRequest {
  id: number
  key: string
  value: string
}

export interface PageResponse<T> {
  list: T[]
  page: number
  size: number
  total: number
}

export interface ApiResponse<T = any> {
  code: number
  message: string
  data: T
}
```

## 页面类型定义

```typescript
// views/Configs/types/index.ts

import type { Config, CreateConfigRequest, UpdateConfigRequest } from '@/types'

export type { Config, CreateConfigRequest, UpdateConfigRequest }

export interface FormState {
  visible: boolean
  mode: 'create' | 'edit'
  data: CreateConfigRequest | UpdateConfigRequest | null
}

export interface DeleteState {
  visible: boolean
  config: Config | null
}
```
