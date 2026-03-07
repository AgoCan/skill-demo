# 国际化配置参考

本文档提供脚本管理模块的国际化语言包配置。

## 目录

- [中文语言包](#中文语言包)
- [英文语言包](#英文语言包)
- [使用示例](#使用示例)

## 中文语言包

### lang/zh-CN/scripts.ts

```typescript
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

## 英文语言包

### lang/en-US/scripts.ts

```typescript
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

## 使用示例

### 在模板中使用

```vue
<template>
  <div>
    <h1>{{ $t('scripts.title') }}</h1>
    <button>{{ $t('scripts.addScript') }}</button>
    <p>{{ $t('scripts.deleteWarning', { name: script.name }) }}</p>
  </div>
</template>
```

### 在 Script 中使用

```vue
<script setup lang="ts">
import { useI18n } from 'vue-i18n'

const { t } = useI18n()

const showSuccess = () => {
  alert(t('scripts.createSuccess'))
}

const showError = () => {
  alert(t('scripts.operationFailed'))
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
  { key: 'name', title: t('scripts.scriptName') },
  { key: 'operation', title: t('scripts.operation') }
])
</script>
```

### 状态标签

```vue
<template>
  <span :class="`status-${result.status}`">
    {{ getStatusText(result.status) }}
  </span>
</template>

<script setup lang="ts">
import { useI18n } from 'vue-i18n'

const { t } = useI18n()

const getStatusText = (status: string) => {
  const statusMap = {
    running: t('scripts.statusRunning'),
    success: t('scripts.statusSuccess'),
    failed: t('scripts.statusFailed')
  }
  return statusMap[status] || status
}
</script>
```

## 语言包组织

### 目录结构

```
src/lang/
├── zh-CN/
│   ├── index.ts
│   ├── common.ts
│   └── scripts.ts      # 脚本管理语言包
└── en-US/
    ├── index.ts
    ├── common.ts
    └── scripts.ts      # 脚本管理语言包
```

### 引入到主语言包

```typescript
import scripts from './scripts'

export default {
  common,
  scripts,
  // ...
}
```
