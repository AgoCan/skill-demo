# Squirrel 前端错误码处理规范

## 错误码定义

后端 API 返回的错误响应格式：

```typescript
interface ErrorResponse {
  code: number
  message: string
  data?: any
}
```

## 错误码分类

### 通用错误码 (0-9999)

| 错误码 | 说明 |
|--------|------|
| 0 | 成功 |
| 400 | 请求参数错误 |
| 401 | 未授权 |
| 403 | 禁止访问 |
| 404 | 资源不存在 |
| 500 | 服务器内部错误 |

### 认证相关 (66000-66019)

| 错误码 | 英文消息 | 中文消息 | 处理建议 |
|--------|----------|----------|----------|
| 66001 | authentication failed | 认证失败 | 检查用户名密码 |
| 66002 | invalid username or password | 用户名或密码错误 | 提示用户重新输入 |
| 66003 | failed to generate token | 生成 Token 失败 | 联系管理员 |
| 66004 | invalid token | 无效的 Token | 重新登录 |
| 66005 | token expired | Token 已过期 | 重新登录 |

### 服务器管理 (60000-60059)

| 错误码 | 英文消息 | 中文消息 | 处理建议 |
|--------|----------|----------|----------|
| 60001 | server not found | 服务器不存在 | 检查服务器 ID |
| 60002 | server alias already exists | 服务器别名已存在 | 更换别名 |
| 60003 | server not found by UUID | 通过 UUID 未找到服务器 | 检查服务器 UUID |
| 60004 | server already exists | 服务器已存在 | 检查重复添加 |
| 60005 | server update failed | 服务器更新失败 | 检查网络连接 |
| 60006 | server delete failed | 服务器删除失败 | 检查关联资源 |
| 60021 | invalid parameter | 无效参数 | 检查输入参数 |
| 60022 | invalid auth type | 无效的认证类型 | 选择正确的认证方式 |
| 60023 | invalid SSH configuration | 无效的 SSH 配置 | 检查 SSH 配置 |
| 60024 | SSH connection test failed | SSH 连接测试失败 | 检查网络和 SSH 配置 |
| 60041 | connect failed | 连接失败 | 检查网络连接 |
| 60042 | agent is offline | Agent 离线 | 检查 Agent 状态 |
| 60043 | agent request failed | Agent 请求失败 | 检查 Agent 服务 |

### 配置管理 (65000-65019)

| 错误码 | 英文消息 | 中文消息 | 处理建议 |
|--------|----------|----------|----------|
| 65001 | config not found | 配置不存在 | 检查配置键 |
| 65002 | config key already exists | 配置键已存在 | 更换配置键 |
| 65003 | invalid config key | 无效的配置键 | 检查配置键格式 |
| 65004 | invalid config value | 无效的配置值 | 检查配置值格式 |
| 65005 | config update failed | 配置更新失败 | 检查网络连接 |
| 65006 | config delete failed | 配置删除失败 | 检查关联资源 |

### 应用管理 (71000-71019)

| 错误码 | 英文消息 | 中文消息 | 处理建议 |
|--------|----------|----------|----------|
| 71001 | application not found | 应用不存在 | 检查应用 ID |
| 71002 | application already exists | 应用已存在 | 更换应用名称 |
| 71003 | invalid application name | 无效的应用名称 | 检查应用名称格式 |
| 71004 | invalid application type | 无效的应用类型 | 选择正确的应用类型 |
| 71005 | invalid application configuration | 无效的应用配置 | 检查应用配置 |
| 71006 | application update failed | 应用更新失败 | 检查网络连接 |
| 71007 | application delete failed | 应用删除失败 | 检查关联资源 |

### 部署管理 (72000-72039)

| 错误码 | 英文消息 | 中文消息 | 处理建议 |
|--------|----------|----------|----------|
| 72001 | deployment not found | 部署不存在 | 检查部署 ID |
| 72002 | application already deployed to this server | 应用已部署到此服务器 | 检查部署状态 |
| 72003 | application not deployed to this server | 应用未部署到此服务器 | 先部署应用 |
| 72004 | failed to generate deploy ID | 生成部署 ID 失败 | 联系管理员 |
| 72005 | failed to create deployment record | 创建部署记录失败 | 检查数据库连接 |
| 72006 | invalid deployment configuration | 无效的部署配置 | 检查部署配置 |
| 72007 | docker-compose container name conflict detected | 检测到 docker-compose 容器名冲突 | 修改容器名称 |
| 72008 | docker-compose port conflict detected | 检测到 docker-compose 端口冲突 | 修改端口配置 |
| 72009 | docker-compose volume conflict detected | 检测到 docker-compose 卷冲突 | 修改卷配置 |
| 72010 | docker-compose network conflict detected | 检测到 docker-compose 网络冲突 | 修改网络配置 |
| 72021 | failed to send request to agent | 发送请求到 Agent 失败 | 检查 Agent 连接 |
| 72022 | failed to parse agent response | 解析 Agent 响应失败 | 联系管理员 |
| 72023 | agent deployment failed | Agent 部署失败 | 查看详细日志 |
| 72024 | agent delete application failed | Agent 删除应用失败 | 查看详细日志 |
| 72025 | agent stop application failed | Agent 停止应用失败 | 查看详细日志 |
| 72026 | agent start application failed | Agent 启动应用失败 | 查看详细日志 |
| 72027 | agent operation failed | Agent 操作失败 | 查看详细日志 |

### 应用商店 (73000-73019)

| 错误码 | 英文消息 | 中文消息 | 处理建议 |
|--------|----------|----------|----------|
| 73001 | application store not found | 应用商店不存在 | 检查应用商店 ID |
| 73002 | application store already exists | 应用商店已存在 | 更换应用名称 |
| 73003 | invalid compose content | 无效的 compose 内容 | 检查 docker-compose 格式 |
| 73004 | unsupported application type | 不支持的应用类型 | 选择支持的应用类型 |
| 73005 | invalid app store configuration | 无效的应用商店配置 | 检查配置 |
| 73006 | app store update failed | 应用商店更新失败 | 检查网络连接 |
| 73007 | app store delete failed | 应用商店删除失败 | 检查关联资源 |

### 监控管理 (81000-81019)

| 错误码 | 英文消息 | 中文消息 | 处理建议 |
|--------|----------|----------|----------|
| 81001 | monitor request failed | 监控请求失败 | 检查 Agent 连接 |
| 81002 | invalid monitor configuration | 无效的监控配置 | 检查监控配置 |
| 81003 | monitor data not found | 监控数据不存在 | 等待数据收集 |
| 81004 | server not found | 服务器不存在 | 检查服务器 ID |

### 脚本管理 (80000-80039)

| 错误码 | 英文消息 | 中文消息 | 处理建议 |
|--------|----------|----------|----------|
| 80001 | script not found | 脚本不存在 | 检查脚本 ID |
| 80002 | script already exists | 脚本已存在 | 更换脚本名称 |
| 80003 | invalid script content | 无效的脚本内容 | 检查脚本语法 |
| 80004 | unsupported script type | 不支持的脚本类型 | 选择支持的脚本类型 |
| 80005 | script not deployed | 脚本未部署 | 先部署脚本 |
| 80021 | script execution failed | 脚本执行失败 | 查看执行日志 |
| 80022 | script execution timeout | 脚本执行超时 | 优化脚本或增加超时时间 |
| 80023 | server not found | 服务器不存在 | 检查服务器 ID |

## 前端错误处理实现

### 1. 错误码映射文件

创建 `src/utils/errorCodes.ts`:

```typescript
export const ERROR_CODES = {
  AUTH: {
    AUTH_FAILED: 66001,
    INVALID_CREDENTIALS: 66002,
    TOKEN_GENERATE_FAILED: 66003,
    INVALID_TOKEN: 66004,
    TOKEN_EXPIRED: 66005
  },
  SERVER: {
    NOT_FOUND: 60001,
    ALIAS_EXISTS: 60002,
    UUID_NOT_FOUND: 60003,
    ALREADY_EXISTS: 60004,
    UPDATE_FAILED: 60005,
    DELETE_FAILED: 60006,
    INVALID_PARAMETER: 60021,
    INVALID_AUTH_TYPE: 60022,
    INVALID_SSH_CONFIG: 60023,
    SSH_TEST_FAILED: 60024,
    CONNECT_FAILED: 60041,
    AGENT_OFFLINE: 60042,
    AGENT_REQUEST_FAILED: 60043
  },
  CONFIG: {
    NOT_FOUND: 65001,
    KEY_ALREADY_EXISTS: 65002,
    INVALID_KEY: 65003,
    INVALID_VALUE: 65004,
    UPDATE_FAILED: 65005,
    DELETE_FAILED: 65006
  },
  APPLICATION: {
    NOT_FOUND: 71001,
    DUPLICATE: 71002,
    INVALID_NAME: 71003,
    INVALID_TYPE: 71004,
    INVALID_CONFIG: 71005,
    UPDATE_FAILED: 71006,
    DELETE_FAILED: 71007
  },
  DEPLOYMENT: {
    NOT_FOUND: 72001,
    ALREADY_DEPLOYED: 72002,
    NOT_DEPLOYED: 72003,
    ID_GENERATE_FAILED: 72004,
    CREATE_RECORD_FAILED: 72005,
    INVALID_CONFIG: 72006,
    COMPOSE_CONTAINER_NAME_CONFLICT: 72007,
    COMPOSE_PORT_CONFLICT: 72008,
    COMPOSE_VOLUME_CONFLICT: 72009,
    COMPOSE_NETWORK_CONFLICT: 72010,
    AGENT_REQUEST_FAILED: 72021,
    AGENT_RESPONSE_PARSE_FAILED: 72022,
    AGENT_DEPLOY_FAILED: 72023,
    AGENT_DELETE_FAILED: 72024,
    AGENT_STOP_FAILED: 72025,
    AGENT_START_FAILED: 72026,
    AGENT_OPERATION_FAILED: 72027
  },
  APP_STORE: {
    NOT_FOUND: 73001,
    DUPLICATE: 73002,
    INVALID_COMPOSE_CONTENT: 73003,
    UNSUPPORTED_TYPE: 73004,
    INVALID_CONFIG: 73005,
    UPDATE_FAILED: 73006,
    DELETE_FAILED: 73007
  },
  MONITOR: {
    FAILED: 81001,
    INVALID_CONFIG: 81002,
    DATA_NOT_FOUND: 81003,
    SERVER_NOT_FOUND: 81004
  },
  SCRIPT: {
    NOT_FOUND: 80001,
    DUPLICATE: 80002,
    INVALID_CONTENT: 80003,
    UNSUPPORTED_TYPE: 80004,
    NOT_DEPLOYED: 80005,
    EXECUTION_FAILED: 80021,
    TIMEOUT: 80022,
    SERVER_NOT_FOUND: 80023
  }
} as const
```

### 2. 错误消息映射文件

创建 `src/utils/errorMessages.ts`:

```typescript
import { ERROR_CODES } from './errorCodes'

export const ERROR_MESSAGES: Record<number, { en: string; zh: string }> = {
  [ERROR_CODES.AUTH.AUTH_FAILED]: {
    en: 'authentication failed',
    zh: '认证失败'
  },
  [ERROR_CODES.AUTH.INVALID_CREDENTIALS]: {
    en: 'invalid username or password',
    zh: '用户名或密码错误'
  },
  [ERROR_CODES.AUTH.TOKEN_GENERATE_FAILED]: {
    en: 'failed to generate token',
    zh: '生成 Token 失败'
  },
  [ERROR_CODES.AUTH.INVALID_TOKEN]: {
    en: 'invalid token',
    zh: '无效的 Token'
  },
  [ERROR_CODES.AUTH.TOKEN_EXPIRED]: {
    en: 'token expired',
    zh: 'Token 已过期'
  },
  [ERROR_CODES.SERVER.NOT_FOUND]: {
    en: 'server not found',
    zh: '服务器不存在'
  },
  [ERROR_CODES.SERVER.ALIAS_EXISTS]: {
    en: 'server alias already exists',
    zh: '服务器别名已存在'
  },
  [ERROR_CODES.SERVER.UUID_NOT_FOUND]: {
    en: 'server not found by UUID',
    zh: '通过 UUID 未找到服务器'
  },
  [ERROR_CODES.SERVER.ALREADY_EXISTS]: {
    en: 'server already exists',
    zh: '服务器已存在'
  },
  [ERROR_CODES.SERVER.UPDATE_FAILED]: {
    en: 'server update failed',
    zh: '服务器更新失败'
  },
  [ERROR_CODES.SERVER.DELETE_FAILED]: {
    en: 'server delete failed',
    zh: '服务器删除失败'
  },
  [ERROR_CODES.SERVER.INVALID_PARAMETER]: {
    en: 'invalid parameter',
    zh: '无效参数'
  },
  [ERROR_CODES.SERVER.INVALID_AUTH_TYPE]: {
    en: 'invalid auth type',
    zh: '无效的认证类型'
  },
  [ERROR_CODES.SERVER.INVALID_SSH_CONFIG]: {
    en: 'invalid SSH configuration',
    zh: '无效的 SSH 配置'
  },
  [ERROR_CODES.SERVER.SSH_TEST_FAILED]: {
    en: 'SSH connection test failed',
    zh: 'SSH 连接测试失败'
  },
  [ERROR_CODES.SERVER.CONNECT_FAILED]: {
    en: 'connect failed',
    zh: '连接失败'
  },
  [ERROR_CODES.SERVER.AGENT_OFFLINE]: {
    en: 'agent is offline',
    zh: 'Agent 离线'
  },
  [ERROR_CODES.SERVER.AGENT_REQUEST_FAILED]: {
    en: 'agent request failed',
    zh: 'Agent 请求失败'
  },
  [ERROR_CODES.CONFIG.NOT_FOUND]: {
    en: 'config not found',
    zh: '配置不存在'
  },
  [ERROR_CODES.CONFIG.KEY_ALREADY_EXISTS]: {
    en: 'config key already exists',
    zh: '配置键已存在'
  },
  [ERROR_CODES.CONFIG.INVALID_KEY]: {
    en: 'invalid config key',
    zh: '无效的配置键'
  },
  [ERROR_CODES.CONFIG.INVALID_VALUE]: {
    en: 'invalid config value',
    zh: '无效的配置值'
  },
  [ERROR_CODES.CONFIG.UPDATE_FAILED]: {
    en: 'config update failed',
    zh: '配置更新失败'
  },
  [ERROR_CODES.CONFIG.DELETE_FAILED]: {
    en: 'config delete failed',
    zh: '配置删除失败'
  },
  [ERROR_CODES.APPLICATION.NOT_FOUND]: {
    en: 'application not found',
    zh: '应用不存在'
  },
  [ERROR_CODES.APPLICATION.DUPLICATE]: {
    en: 'application already exists',
    zh: '应用已存在'
  },
  [ERROR_CODES.APPLICATION.INVALID_NAME]: {
    en: 'invalid application name',
    zh: '无效的应用名称'
  },
  [ERROR_CODES.APPLICATION.INVALID_TYPE]: {
    en: 'invalid application type',
    zh: '无效的应用类型'
  },
  [ERROR_CODES.APPLICATION.INVALID_CONFIG]: {
    en: 'invalid application configuration',
    zh: '无效的应用配置'
  },
  [ERROR_CODES.APPLICATION.UPDATE_FAILED]: {
    en: 'application update failed',
    zh: '应用更新失败'
  },
  [ERROR_CODES.APPLICATION.DELETE_FAILED]: {
    en: 'application delete failed',
    zh: '应用删除失败'
  },
  [ERROR_CODES.DEPLOYMENT.NOT_FOUND]: {
    en: 'deployment not found',
    zh: '部署不存在'
  },
  [ERROR_CODES.DEPLOYMENT.ALREADY_DEPLOYED]: {
    en: 'application already deployed to this server',
    zh: '应用已部署到此服务器'
  },
  [ERROR_CODES.DEPLOYMENT.NOT_DEPLOYED]: {
    en: 'application not deployed to this server',
    zh: '应用未部署到此服务器'
  },
  [ERROR_CODES.DEPLOYMENT.ID_GENERATE_FAILED]: {
    en: 'failed to generate deploy ID',
    zh: '生成部署 ID 失败'
  },
  [ERROR_CODES.DEPLOYMENT.CREATE_RECORD_FAILED]: {
    en: 'failed to create deployment record',
    zh: '创建部署记录失败'
  },
  [ERROR_CODES.DEPLOYMENT.INVALID_CONFIG]: {
    en: 'invalid deployment configuration',
    zh: '无效的部署配置'
  },
  [ERROR_CODES.DEPLOYMENT.COMPOSE_CONTAINER_NAME_CONFLICT]: {
    en: 'docker-compose container name conflict detected',
    zh: '检测到 docker-compose 容器名冲突'
  },
  [ERROR_CODES.DEPLOYMENT.COMPOSE_PORT_CONFLICT]: {
    en: 'docker-compose port conflict detected',
    zh: '检测到 docker-compose 端口冲突'
  },
  [ERROR_CODES.DEPLOYMENT.COMPOSE_VOLUME_CONFLICT]: {
    en: 'docker-compose volume conflict detected',
    zh: '检测到 docker-compose 卷冲突'
  },
  [ERROR_CODES.DEPLOYMENT.COMPOSE_NETWORK_CONFLICT]: {
    en: 'docker-compose network conflict detected',
    zh: '检测到 docker-compose 网络冲突'
  },
  [ERROR_CODES.DEPLOYMENT.AGENT_REQUEST_FAILED]: {
    en: 'failed to send request to agent',
    zh: '发送请求到 Agent 失败'
  },
  [ERROR_CODES.DEPLOYMENT.AGENT_RESPONSE_PARSE_FAILED]: {
    en: 'failed to parse agent response',
    zh: '解析 Agent 响应失败'
  },
  [ERROR_CODES.DEPLOYMENT.AGENT_DEPLOY_FAILED]: {
    en: 'agent deployment failed',
    zh: 'Agent 部署失败'
  },
  [ERROR_CODES.DEPLOYMENT.AGENT_DELETE_FAILED]: {
    en: 'agent delete application failed',
    zh: 'Agent 删除应用失败'
  },
  [ERROR_CODES.DEPLOYMENT.AGENT_STOP_FAILED]: {
    en: 'agent stop application failed',
    zh: 'Agent 停止应用失败'
  },
  [ERROR_CODES.DEPLOYMENT.AGENT_START_FAILED]: {
    en: 'agent start application failed',
    zh: 'Agent 启动应用失败'
  },
  [ERROR_CODES.DEPLOYMENT.AGENT_OPERATION_FAILED]: {
    en: 'agent operation failed',
    zh: 'Agent 操作失败'
  },
  [ERROR_CODES.APP_STORE.NOT_FOUND]: {
    en: 'application store not found',
    zh: '应用商店不存在'
  },
  [ERROR_CODES.APP_STORE.DUPLICATE]: {
    en: 'application store already exists',
    zh: '应用商店已存在'
  },
  [ERROR_CODES.APP_STORE.INVALID_COMPOSE_CONTENT]: {
    en: 'invalid compose content',
    zh: '无效的 compose 内容'
  },
  [ERROR_CODES.APP_STORE.UNSUPPORTED_TYPE]: {
    en: 'unsupported application type',
    zh: '不支持的应用类型'
  },
  [ERROR_CODES.APP_STORE.INVALID_CONFIG]: {
    en: 'invalid app store configuration',
    zh: '无效的应用商店配置'
  },
  [ERROR_CODES.APP_STORE.UPDATE_FAILED]: {
    en: 'app store update failed',
    zh: '应用商店更新失败'
  },
  [ERROR_CODES.APP_STORE.DELETE_FAILED]: {
    en: 'app store delete failed',
    zh: '应用商店删除失败'
  },
  [ERROR_CODES.MONITOR.FAILED]: {
    en: 'monitor request failed',
    zh: '监控请求失败'
  },
  [ERROR_CODES.MONITOR.INVALID_CONFIG]: {
    en: 'invalid monitor configuration',
    zh: '无效的监控配置'
  },
  [ERROR_CODES.MONITOR.DATA_NOT_FOUND]: {
    en: 'monitor data not found',
    zh: '监控数据不存在'
  },
  [ERROR_CODES.MONITOR.SERVER_NOT_FOUND]: {
    en: 'server not found',
    zh: '服务器不存在'
  },
  [ERROR_CODES.SCRIPT.NOT_FOUND]: {
    en: 'script not found',
    zh: '脚本不存在'
  },
  [ERROR_CODES.SCRIPT.DUPLICATE]: {
    en: 'script already exists',
    zh: '脚本已存在'
  },
  [ERROR_CODES.SCRIPT.INVALID_CONTENT]: {
    en: 'invalid script content',
    zh: '无效的脚本内容'
  },
  [ERROR_CODES.SCRIPT.UNSUPPORTED_TYPE]: {
    en: 'unsupported script type',
    zh: '不支持的脚本类型'
  },
  [ERROR_CODES.SCRIPT.NOT_DEPLOYED]: {
    en: 'script not deployed',
    zh: '脚本未部署'
  },
  [ERROR_CODES.SCRIPT.EXECUTION_FAILED]: {
    en: 'script execution failed',
    zh: '脚本执行失败'
  },
  [ERROR_CODES.SCRIPT.TIMEOUT]: {
    en: 'script execution timeout',
    zh: '脚本执行超时'
  },
  [ERROR_CODES.SCRIPT.SERVER_NOT_FOUND]: {
    en: 'server not found',
    zh: '服务器不存在'
  }
}

export function getErrorMessage(code: number, locale: 'en' | 'zh' = 'zh'): string {
  const error = ERROR_MESSAGES[code]
  if (error) {
    return error[locale]
  }
  return locale === 'zh' ? '未知错误' : 'Unknown error'
}
```

### 3. 错误处理 Composable

创建 `src/composables/useErrorHandler.ts`:

```typescript
import { ref } from 'vue'
import { useI18n } from 'vue-i18n'
import { useRouter } from 'vue-router'
import { ElMessage, ElNotification } from 'element-plus'
import { getErrorMessage } from '@/utils/errorMessages'

export interface ApiError {
  code: number
  message: string
  data?: any
}

export function useErrorHandler() {
  const { locale } = useI18n()
  const router = useRouter()
  const error = ref<ApiError | null>(null)

  const handleError = (err: any, options?: { showMessage?: boolean; showNotification?: boolean }) => {
    const { showMessage = true, showNotification = false } = options || {}

    let apiError: ApiError

    if (err?.response?.data) {
      apiError = err.response.data
    } else if (err?.code && err?.message) {
      apiError = err
    } else {
      apiError = {
        code: 500,
        message: err?.message || 'Unknown error'
      }
    }

    error.value = apiError

    const message = getErrorMessage(apiError.code, locale.value as 'en' | 'zh')

    if (showMessage) {
      ElMessage.error(message)
    }

    if (showNotification) {
      ElNotification({
        title: locale.value === 'zh' ? '错误' : 'Error',
        message,
        type: 'error',
        duration: 5000
      })
    }

    handleSpecialErrors(apiError.code)

    return apiError
  }

  const handleSpecialErrors = (code: number) => {
    switch (code) {
      case 66004:
      case 66005:
        router.push('/login')
        break
      default:
        break
    }
  }

  const clearError = () => {
    error.value = null
  }

  return {
    error,
    handleError,
    clearError
  }
}
```

### 4. Axios 拦截器配置

创建 `src/utils/request.ts`:

```typescript
import axios, { AxiosError, AxiosRequestConfig, AxiosResponse } from 'axios'
import { ElMessage } from 'element-plus'
import { useErrorHandler } from '@/composables/useErrorHandler'

const service = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL || '/api',
  timeout: 30000
})

service.interceptors.request.use(
  (config: AxiosRequestConfig) => {
    const token = localStorage.getItem('token')
    if (token && config.headers) {
      config.headers['Authorization'] = `Bearer ${token}`
    }
    return config
  },
  (error: AxiosError) => {
    return Promise.reject(error)
  }
)

service.interceptors.response.use(
  (response: AxiosResponse) => {
    return response.data
  },
  (error: AxiosError) => {
    const { handleError } = useErrorHandler()
    handleError(error)
    return Promise.reject(error)
  }
)

export default service
```

### 5. 使用示例

```typescript
import { ref } from 'vue'
import { useErrorHandler } from '@/composables/useErrorHandler'
import request from '@/utils/request'
import { ERROR_CODES } from '@/utils/errorCodes'

export function useServerList() {
  const { handleError } = useErrorHandler()
  const loading = ref(false)
  const servers = ref([])

  const fetchServers = async () => {
    try {
      loading.value = true
      const response = await request.get('/servers')
      servers.value = response.data
    } catch (err) {
      handleError(err)
    } finally {
      loading.value = false
    }
  }

  const deleteServer = async (id: string) => {
    try {
      loading.value = true
      await request.delete(`/servers/${id}`)
      await fetchServers()
    } catch (err) {
      handleError(err)
    } finally {
      loading.value = false
    }
  }

  return {
    loading,
    servers,
    fetchServers,
    deleteServer
  }
}
```

## 错误处理最佳实践

1. **统一错误处理**: 使用 `useErrorHandler` composable 统一处理所有 API 错误
2. **国际化支持**: 根据用户语言显示对应的错误消息
3. **特殊错误处理**: 对于 Token 过期等特殊错误码，自动跳转到登录页
4. **用户友好提示**: 使用 Element Plus 的 Message 和 Notification 组件显示错误
5. **错误日志**: 在生产环境中记录错误日志用于问题排查
6. **重试机制**: 对于网络错误等可恢复错误，实现自动重试逻辑
