# Deployment 类型定义

## 全局类型定义

```typescript
// types/index.ts

export type DeploymentStatus = 'running' | 'stopped' | 'not_deployed' | 'error'

export interface Deployment {
  id: number
  deploy_id: number
  application: ApplicationInfo
  server: ServerInfo
  status: DeploymentStatus
  deployed_at: string
}

export interface ApplicationInfo {
  id: number
  name: string
  description: string
  type: string
  version: string
}

export interface ServerInfo {
  id: number
  ip_address: string
  agent_port: number
}

export interface CreateDeploymentRequest {
  application_id: number
  server_id: number
}

export interface ApiResponse<T = any> {
  code: number
  message: string
  data: T
}
```

## 页面类型定义

```typescript
// views/Deployment/types/index.ts

import type { Deployment, CreateDeploymentRequest, DeploymentStatus } from '@/types'

export type { Deployment, CreateDeploymentRequest, DeploymentStatus }

export interface FormState {
  visible: boolean
  data: CreateDeploymentRequest | null
}

export interface UndeployState {
  visible: boolean
  deployment: Deployment | null
}

export interface DetailState {
  visible: boolean
  deployment: Deployment | null
}
```

## 部署状态说明

| 状态 | 说明 | 可执行操作 |
|------|------|-----------|
| `running` | 应用正在正常运行 | 停止、卸载 |
| `stopped` | 应用已停止 | 启动、卸载 |
| `not_deployed` | 应用已创建但未部署 | 等待部署完成 |
| `error` | 应用运行出错 | 停止、卸载、重新部署 |
