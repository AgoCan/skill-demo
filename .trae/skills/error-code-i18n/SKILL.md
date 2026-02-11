---
name: "error-code-i18n"
description: "Setup error code internationalization for frontend based on backend API error codes. Invoke when user needs to add error code translations, create error message handling, or implement error code to message mapping."
---

# 前端错误码国际化配置

基于后端 API 错误码的前端国际化解决方案，提供错误码到多语言消息的映射和统一处理。

## 错误码总览

| 模块 | 错误码范围 |
|------|-----------|
| Server | 60000-60059 |
| Config | 65000-65019 |
| Auth | 66000-66019 |
| Application | 71000-71019 |
| Deployment | 72000-72039 |
| AppStore | 73000-73019 |
| Script | 80000-80039 |
| Monitor | 81000-81019 |

## 目录结构

```
src/lang/
├── zh-CN/
│   └── error.ts        # 中文错误消息
└── en-US/
    └── error.ts         # 英文错误消息
```

## 1. 创建错误消息语言包

**src/lang/zh-CN/error.ts**

```typescript
export default {
  // Server (60000-60059)
  server: {
    60001: '服务器未找到',
    60002: '服务器别名已存在',
    60003: '通过UUID未找到服务器',
    60004: '服务器已存在',
    60005: '服务器更新失败',
    60006: '服务器删除失败',
    60021: '无效的参数',
    60022: '无效的认证类型',
    60023: '无效的SSH配置',
    60024: 'SSH连接测试失败',
    60041: '连接失败',
    60042: 'Agent离线',
    60043: 'Agent请求失败',
  },

  // Config (65000-65019)
  config: {
    65001: '配置未找到',
    65002: '配置键已存在',
    65003: '无效的配置键',
    65004: '无效的配置值',
    65005: '配置更新失败',
    65006: '配置删除失败',
  },

  // Auth (66000-66019)
  auth: {
    66001: '认证失败',
    66002: '用户名或密码无效',
    66003: '生成Token失败',
    66004: '无效的Token',
    66005: 'Token已过期',
  },

  // Application (71000-71019)
  application: {
    71001: '应用未找到',
    71002: '应用已存在',
    71003: '无效的应用名称',
    71004: '无效的应用类型',
    71005: '无效的应用配置',
    71006: '应用更新失败',
    71007: '应用删除失败',
  },

  // Deployment (72000-72039)
  deployment: {
    72001: '部署未找到',
    72002: '应用已部署到此服务器',
    72003: '应用未部署到此服务器',
    72004: '生成部署ID失败',
    72005: '创建部署记录失败',
    72006: '无效的部署配置',
    72007: '检测到docker-compose容器名称冲突',
    72008: '检测到docker-compose端口冲突',
    72009: '检测到docker-compose卷冲突',
    72010: '检测到docker-compose网络冲突',
    72021: '发送请求到Agent失败',
    72022: '解析Agent响应失败',
    72023: 'Agent部署失败',
    72024: 'Agent删除应用失败',
    72025: 'Agent停止应用失败',
    72026: 'Agent启动应用失败',
    72027: 'Agent操作失败',
  },

  // AppStore (73000-73019)
  appStore: {
    73001: '应用商店未找到',
    73002: '应用商店已存在',
    73003: '无效的compose内容',
    73004: '不支持的应用类型',
    73005: '无效的应用商店配置',
    73006: '应用商店更新失败',
    73007: '应用商店删除失败',
  },

  // Script (80000-80039)
  script: {
    80001: '脚本未找到',
    80002: '脚本已存在',
    80003: '无效的脚本内容',
    80004: '不支持的脚本类型',
    80005: '脚本未部署',
    80021: '脚本执行失败',
    80022: '脚本执行超时',
    80023: '服务器未找到',
  },

  // Monitor (81000-81019)
  monitor: {
    81001: '监控请求失败',
    81002: '无效的监控配置',
    81003: '监控数据未找到',
    81004: '服务器未找到',
  },

  // 通用错误
  common: {
    networkError: '网络错误，请稍后重试',
    unknownError: '未知错误',
    requestError: '请求失败',
  },
}
```

**src/lang/en-US/error.ts**

```typescript
export default {
  // Server (60000-60059)
  server: {
    60001: 'Server not found',
    60002: 'Server alias already exists',
    60003: 'Server not found by UUID',
    60004: 'Server already exists',
    60005: 'Server update failed',
    60006: 'Server delete failed',
    60021: 'Invalid parameter',
    60022: 'Invalid auth type',
    60023: 'Invalid SSH configuration',
    60024: 'SSH connection test failed',
    60041: 'Connect failed',
    60042: 'Agent is offline',
    60043: 'Agent request failed',
  },

  // Config (65000-65019)
  config: {
    65001: 'Config not found',
    65002: 'Config key already exists',
    65003: 'Invalid config key',
    65004: 'Invalid config value',
    65005: 'Config update failed',
    65006: 'Config delete failed',
  },

  // Auth (66000-66019)
  auth: {
    66001: 'Authentication failed',
    66002: 'Invalid username or password',
    66003: 'Failed to generate token',
    66004: 'Invalid token',
    66005: 'Token expired',
  },

  // Application (71000-71019)
  application: {
    71001: 'Application not found',
    71002: 'Application already exists',
    71003: 'Invalid application name',
    71004: 'Invalid application type',
    71005: 'Invalid application configuration',
    71006: 'Application update failed',
    71007: 'Application delete failed',
  },

  // Deployment (72000-72039)
  deployment: {
    72001: 'Deployment not found',
    72002: 'Application already deployed to this server',
    72003: 'Application not deployed to this server',
    72004: 'Failed to generate deploy ID',
    72005: 'Failed to create deployment record',
    72006: 'Invalid deployment configuration',
    72007: 'Docker-compose container name conflict detected',
    72008: 'Docker-compose port conflict detected',
    72009: 'Docker-compose volume conflict detected',
    72010: 'Docker-compose network conflict detected',
    72021: 'Failed to send request to agent',
    72022: 'Failed to parse agent response',
    72023: 'Agent deployment failed',
    72024: 'Agent delete application failed',
    72025: 'Agent stop application failed',
    72026: 'Agent start application failed',
    72027: 'Agent operation failed',
  },

  // AppStore (73000-73019)
  appStore: {
    73001: 'Application store not found',
    73002: 'Application store already exists',
    73003: 'Invalid compose content',
    73004: 'Unsupported application type',
    73005: 'Invalid app store configuration',
    73006: 'App store update failed',
    73007: 'App store delete failed',
  },

  // Script (80000-80039)
  script: {
    80001: 'Script not found',
    80002: 'Script already exists',
    80003: 'Invalid script content',
    80004: 'Unsupported script type',
    80005: 'Script not deployed',
    80021: 'Script execution failed',
    80022: 'Script execution timeout',
    80023: 'Server not found',
  },

  // Monitor (81000-81019)
  monitor: {
    81001: 'Monitor request failed',
    81002: 'Invalid monitor configuration',
    81003: 'Monitor data not found',
    81004: 'Server not found',
  },

  // Common errors
  common: {
    networkError: 'Network error, please try again later',
    unknownError: 'Unknown error',
    requestError: 'Request failed',
  },
}
```

## 2. 在主语言包中引入

**src/lang/zh-CN/index.ts**

```typescript
import error from './error'

export default {
  // ... other modules
  error,
}
```

**src/lang/en-US/index.ts**

```typescript
import error from './error'

export default {
  // ... other modules
  error,
}
```

## 3. 创建错误处理工具

**src/utils/errorHandler.ts**

```typescript
import { useI18n } from 'vue-i18n'

// 错误码到模块映射
const ERROR_CODE_MAP: Record<number, string> = {
  // Server (60000-60059)
  60001: 'server',
  60002: 'server',
  60003: 'server',
  60004: 'server',
  60005: 'server',
  60006: 'server',
  60021: 'server',
  60022: 'server',
  60023: 'server',
  60024: 'server',
  60041: 'server',
  60042: 'server',
  60043: 'server',

  // Config (65000-65019)
  65001: 'config',
  65002: 'config',
  65003: 'config',
  65004: 'config',
  65005: 'config',
  65006: 'config',

  // Auth (66000-66019)
  66001: 'auth',
  66002: 'auth',
  66003: 'auth',
  66004: 'auth',
  66005: 'auth',

  // Application (71000-71019)
  71001: 'application',
  71002: 'application',
  71003: 'application',
  71004: 'application',
  71005: 'application',
  71006: 'application',
  71007: 'application',

  // Deployment (72000-72039)
  72001: 'deployment',
  72002: 'deployment',
  72003: 'deployment',
  72004: 'deployment',
  72005: 'deployment',
  72006: 'deployment',
  72007: 'deployment',
  72008: 'deployment',
  72009: 'deployment',
  72010: 'deployment',
  72021: 'deployment',
  72022: 'deployment',
  72023: 'deployment',
  72024: 'deployment',
  72025: 'deployment',
  72026: 'deployment',
  72027: 'deployment',

  // AppStore (73000-73019)
  73001: 'appStore',
  73002: 'appStore',
  73003: 'appStore',
  73004: 'appStore',
  73005: 'appStore',
  73006: 'appStore',
  73007: 'appStore',

  // Script (80000-80039)
  80001: 'script',
  80002: 'script',
  80003: 'script',
  80004: 'script',
  80005: 'script',
  80021: 'script',
  80022: 'script',
  80023: 'script',

  // Monitor (81000-81019)
  81001: 'monitor',
  81002: 'monitor',
  81003: 'monitor',
  81004: 'monitor',
}

// API 错误类型
export interface ApiError {
  code: number
  message?: string
  data?: unknown
}

/**
 * 获取错误消息
 */
export function getErrorMessage(error: ApiError | number): string {
  const { t } = useI18n()

  let code: number
  let message: string | undefined

  if (typeof error === 'number') {
    code = error
  } else {
    code = error.code
    message = error.message
  }

  const module = ERROR_CODE_MAP[code]

  if (module) {
    const errorKey = `error.${module}.${code}`
    return t(errorKey, { defaultValue: message || t('error.common.unknownError') })
  }

  // 如果没有对应的模块，返回原始消息或通用错误
  return message || t('error.common.unknownError')
}

/**
 * 判断是否为网络错误
 */
export function isNetworkError(error: unknown): boolean {
  if (error instanceof TypeError) {
    return error.message.includes('fetch') || error.message.includes('network')
  }
  return false
}

/**
 * 判断是否为认证错误
 */
export function isAuthError(error: ApiError): boolean {
  return error.code >= 66000 && error.code <= 66019
}

/**
 * 判断是否为权限错误
 */
export function isPermissionError(error: ApiError): boolean {
  return error.code >= 66000 && error.code <= 66019
}
```

## 4. 在 API 请求拦截器中使用

**src/utils/request.ts**

```typescript
import axios from 'axios'
import type { AxiosError, AxiosResponse } from 'axios'
import { ElMessage } from 'element-plus'
import { getErrorMessage, isNetworkError } from './errorHandler'

const instance = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL || '/api',
  timeout: 30000,
})

// 响应拦截器
instance.interceptors.response.use(
  (response: AxiosResponse) => {
    return response
  },
  (error: AxiosError<ApiError>) => {
    // 网络错误
    if (!error.response) {
      ElMessage.error(getErrorMessage({ code: 0, message: 'error.common.networkError' }))
      return Promise.reject(error)
    }

    // API 错误
    const { code, message } = error.response.data || {}
    const errorMessage = getErrorMessage({ code, message })
    ElMessage.error(errorMessage)

    // 特殊处理：认证失败跳转登录
    if (code === 66004 || code === 66005) {
      // 跳转到登录页
      window.location.href = '/login'
    }

    return Promise.reject(error)
  }
)

export default instance
```

## 5. 在组件中使用

```vue
<template>
  <el-button @click="deleteServer">删除服务器</el-button>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { getErrorMessage } from '@/utils/errorHandler'
import { deleteServer as apiDeleteServer } from '@/api/server'

const loading = ref(false)

const deleteServer = async () => {
  try {
    loading.value = true
    await apiDeleteServer(serverId.value)
    ElMessage.success('删除成功')
  } catch (error) {
    // 使用错误处理工具获取国际化消息
    console.error(getErrorMessage(error))
  } finally {
    loading.value = false
  }
}
</script>
```

## 6. 使用 Composition API 封装

**src/composables/useErrorHandler.ts**

```typescript
import { ref } from 'vue'
import { useI18n } from 'vue-i18n'
import { ElMessage } from 'element-plus'
import type { ApiError } from '@/utils/errorHandler'
import { getErrorMessage, isNetworkError, isAuthError } from '@/utils/errorHandler'

export function useErrorHandler() {
  const { t } = useI18n()
  const error = ref<string | null>(null)
  const errorCode = ref<number | null>(null)

  const handleError = (err: ApiError | unknown) => {
    if (isNetworkError(err)) {
      error.value = t('error.common.networkError')
      ElMessage.error(error.value)
      return
    }

    if (err && typeof err === 'object' && 'code' in err) {
      const apiError = err as ApiError
      errorCode.value = apiError.code
      error.value = getErrorMessage(apiError)

      ElMessage.error(error.value)

      // 认证错误跳转
      if (isAuthError(apiError)) {
        window.location.href = '/login'
      }
    } else {
      error.value = t('error.common.unknownError')
      ElMessage.error(error.value)
    }
  }

  const clearError = () => {
    error.value = null
    errorCode.value = null
  }

  return {
    error,
    errorCode,
    handleError,
    clearError,
  }
}
```

在组件中使用：

```vue
<script setup lang="ts">
import { useErrorHandler } from '@/composables/useErrorHandler'

const { error, handleError, clearError } = useErrorHandler()

const onSubmit = async () => {
  try {
    await submitForm()
  } catch (err) {
    handleError(err)
  }
}
</script>
```

## 7. 错误码快速查询表

| 错误码 | 模块 | 说明 |
|--------|------|------|
| 60001 | Server | 服务器未找到 |
| 60002 | Server | 服务器别名已存在 |
| 60004 | Server | 服务器已存在 |
| 60021 | Server | 无效参数 |
| 60041 | Server | 连接失败 |
| 60042 | Server | Agent离线 |
| 65001 | Config | 配置未找到 |
| 65002 | Config | 配置键已存在 |
| 66001 | Auth | 认证失败 |
| 66002 | Auth | 用户名或密码无效 |
| 66004 | Auth | 无效Token |
| 66005 | Auth | Token已过期 |
| 71001 | Application | 应用未找到 |
| 71002 | Application | 应用已存在 |
| 72001 | Deployment | 部署未找到 |
| 72002 | Deployment | 应用已部署到此服务器 |
| 72021 | Deployment | 发送请求到Agent失败 |
| 73001 | AppStore | 应用商店未找到 |
| 80001 | Script | 脚本未找到 |
| 80021 | Script | 脚本执行失败 |
| 81001 | Monitor | 监控请求失败 |

## 最佳实践

1. **集中管理**：所有错误码映射集中在一个地方管理
2. **自动降级**：没有对应的翻译时，返回后端原始消息
3. **统一处理**：通过 axios 拦截器统一处理错误响应
4. **类型安全**：使用 TypeScript 定义 ApiError 接口
5. **组合复用**：使用 useErrorHandler composable 在多个组件中复用
6. **特殊处理**：认证错误自动跳转登录页

## 相关文件

后端错误码定义位置：
- Server: `internal/squ-apiserver/handler/server/res/response_code.go`
- Config: `internal/squ-apiserver/handler/config/res/response_code.go`
- Auth: `internal/squ-apiserver/handler/auth/res/response_code.go`
- Application: `internal/squ-apiserver/handler/application/res/response_code.go`
- Deployment: `internal/squ-apiserver/handler/deployment/res/response_code.go`
- AppStore: `internal/squ-apiserver/handler/app_store/res/response_code.go`
- Script: `internal/squ-apiserver/handler/script/res/response_code.go`
- Monitor: `internal/squ-apiserver/handler/monitor/res/response_code.go`
