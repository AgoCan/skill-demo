# 错误处理工具

## 目录

- [错误码映射配置](#错误码映射配置)
- [错误处理工具函数](#错误处理工具函数)
- [API 请求拦截器](#api-请求拦截器)

## 错误码映射配置

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
```

## 错误处理工具函数

```typescript
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

## API 请求拦截器

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
      window.location.href = '/login'
    }

    return Promise.reject(error)
  }
)

export default instance
```
