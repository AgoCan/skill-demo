# 语言包配置

## 目录

- [中文语言包](#中文语言包)
- [英文语言包](#英文语言包)
- [引入到主语言包](#引入到主语言包)

## 中文语言包

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

## 英文语言包

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

## 引入到主语言包

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
