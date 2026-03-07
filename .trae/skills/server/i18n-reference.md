# 国际化配置参考

本文档提供服务器管理模块的国际化语言包配置。

## 目录

- [中文语言包](#中文语言包)
- [英文语言包](#英文语言包)
- [使用示例](#使用示例)

## 中文语言包

### lang/zh-CN/server.ts

```typescript
export default {
  title: '服务器管理',
  listTitle: '服务器列表',
  addServer: '添加服务器',
  editServer: '编辑服务器',
  deleteServer: '删除服务器',
  connectTerminal: '连接终端',
  viewDetail: '查看详情',
  hostname: '主机名',
  ipAddress: 'IP地址',
  sshPort: 'SSH端口',
  username: '用户名',
  authType: '认证方式',
  status: '状态',
  operation: '操作',
  alias: '别名',
  password: '密码',
  key: '密钥',
  online: '在线',
  offline: '离线',
  unknown: '未知',
  active: '活跃',
  inactive: '停用',
  basicInfo: '基本信息',
  authInfo: '认证信息',
  optional: '可选',
  save: '保存',
  cancel: '取消',
  confirm: '确认',
  confirmDelete: '确认删除',
  deleteWarning: '确定要删除服务器 "{name}" 吗？此操作不可恢复。',
  required: '必填项',
  invalidIp: '请输入有效的IP地址',
  invalidPort: '端口范围 1-65535',
  createSuccess: '服务器创建成功',
  updateSuccess: '服务器更新成功',
  deleteSuccess: '服务器删除成功',
  operationFailed: '操作失败',
  terminal: '终端',
  connecting: '连接中...',
  connectionFailed: '连接失败',
  reconnect: '重新连接',
  close: '关闭',
  serverDetail: '服务器详情',
  systemInfo: '系统信息',
  os: '操作系统',
  kernel: '内核版本',
  architecture: '架构',
  platform: '平台',
  platformVersion: '平台版本',
  uptime: '运行时间',
  ipAddresses: 'IP地址列表'
}
```

## 英文语言包

### lang/en-US/server.ts

```typescript
export default {
  title: 'Server Management',
  listTitle: 'Server List',
  addServer: 'Add Server',
  editServer: 'Edit Server',
  deleteServer: 'Delete Server',
  connectTerminal: 'Connect Terminal',
  viewDetail: 'View Detail',
  hostname: 'Hostname',
  ipAddress: 'IP Address',
  sshPort: 'SSH Port',
  username: 'Username',
  authType: 'Auth Type',
  status: 'Status',
  operation: 'Operation',
  alias: 'Alias',
  password: 'Password',
  key: 'Key',
  online: 'Online',
  offline: 'Offline',
  unknown: 'Unknown',
  active: 'Active',
  inactive: 'Inactive',
  basicInfo: 'Basic Info',
  authInfo: 'Auth Info',
  optional: 'Optional',
  save: 'Save',
  cancel: 'Cancel',
  confirm: 'Confirm',
  confirmDelete: 'Confirm Delete',
  deleteWarning: 'Are you sure you want to delete server "{name}"? This action cannot be undone.',
  required: 'Required',
  invalidIp: 'Please enter a valid IP address',
  invalidPort: 'Port range 1-65535',
  createSuccess: 'Server created successfully',
  updateSuccess: 'Server updated successfully',
  deleteSuccess: 'Server deleted successfully',
  operationFailed: 'Operation failed',
  terminal: 'Terminal',
  connecting: 'Connecting...',
  connectionFailed: 'Connection failed',
  reconnect: 'Reconnect',
  close: 'Close',
  serverDetail: 'Server Detail',
  systemInfo: 'System Info',
  os: 'OS',
  kernel: 'Kernel Version',
  architecture: 'Architecture',
  platform: 'Platform',
  platformVersion: 'Platform Version',
  uptime: 'Uptime',
  ipAddresses: 'IP Addresses'
}
```

## 使用示例

### 在模板中使用

```vue
<template>
  <div>
    <h1>{{ $t('server.title') }}</h1>
    <button>{{ $t('server.addServer') }}</button>
    <p>{{ $t('server.deleteWarning', { name: server.hostname }) }}</p>
  </div>
</template>
```

### 在 Script 中使用

```vue
<script setup lang="ts">
import { useI18n } from 'vue-i18n'

const { t } = useI18n()

const showSuccess = () => {
  alert(t('server.createSuccess'))
}

const showError = () => {
  alert(t('server.operationFailed'))
}
</script>
```

### 表格列定义

```vue
<script setup lang="ts">
import { computed } from 'vue'
import { useI18n } from 'vue-i18n'

const { t } = useI18n()

const columns = computed(() => [
  { key: 'hostname', title: t('server.hostname') },
  { key: 'ip_address', title: t('server.ipAddress') },
  { key: 'ssh_port', title: t('server.sshPort') },
  { key: 'ssh_username', title: t('server.username') },
  { key: 'auth_type', title: t('server.authType') },
  { key: 'status', title: t('server.status') },
  { key: 'operation', title: t('server.operation') }
])
</script>
```

### 表单字段定义

```vue
<script setup lang="ts">
import { computed } from 'vue'
import { useI18n } from 'vue-i18n'

const { t } = useI18n()

const formFields = computed(() => [
  {
    key: 'ip_address',
    label: t('server.ipAddress'),
    placeholder: t('server.invalidIp'),
    required: true
  },
  {
    key: 'ssh_port',
    label: t('server.sshPort'),
    placeholder: '22',
    required: true
  },
  {
    key: 'ssh_username',
    label: t('server.username'),
    placeholder: 'root',
    required: true
  }
])
</script>
```

## 语言包组织

### 目录结构

```
src/lang/
├── zh-CN/
│   ├── index.ts
│   ├── common.ts
│   └── server.ts      # 服务器管理语言包
└── en-US/
    ├── index.ts
    ├── common.ts
    └── server.ts      # 服务器管理语言包
```

### 引入到主语言包

```typescript
import server from './server'

export default {
  common,
  server,
  // ...
}
```
