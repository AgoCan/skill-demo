# Application 国际化配置

## 目录

1. [i18n 配置入口](#i18n-配置入口)
2. [中文语言包](#中文语言包)
3. [英文语言包](#英文语言包)

---

## i18n 配置入口

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

---

## 中文语言包

### 语言包入口

```typescript
// lang/zh-CN/index.ts
import common from './common'
import layout from './layout'
import application from './application'

export default {
  common,
  layout,
  application
}
```

### 通用文本

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

### 布局文本

```typescript
// lang/zh-CN/layout.ts
export default {
  overview: '概览',
  servers: '服务器',
  applications: '应用',
  appStore: '应用商店',
  configs: '配置管理',
  monitor: '监控',
  logout: '退出登录'
}
```

### 应用管理文本

```typescript
// lang/zh-CN/application.ts
export default {
  title: '应用管理',
  listTitle: '应用列表',
  addApplication: '添加应用',
  editApplication: '编辑应用',
  deleteApplication: '删除应用',
  viewDetail: '查看详情',
  applicationName: '应用名称',
  applicationType: '应用类型',
  applicationVersion: '应用版本',
  applicationDescription: '应用描述',
  applicationContent: '应用内容',
  operation: '操作',
  save: '保存',
  cancel: '取消',
  confirm: '确认',
  confirmDelete: '确认删除',
  deleteWarning: '确定要删除应用 "{name}" 吗？此操作不可恢复。',
  required: '必填项',
  createSuccess: '应用创建成功',
  updateSuccess: '应用更新成功',
  deleteSuccess: '应用删除成功',
  operationFailed: '操作失败',
  applicationDetail: '应用详情',
  basicInfo: '基本信息',
  searchPlaceholder: '搜索应用名称或描述...',
  typeCompose: 'Compose',
  typeScript: '脚本',
  typeBinary: '二进制',
  contentPreview: '内容预览',
  copy: '复制',
  copySuccess: '已复制到剪贴板',
  sortAsc: '升序',
  sortDesc: '降序',
  importFromStore: '从应用商店导入',
  deploy: '部署'
}
```

---

## 英文语言包

### 语言包入口

```typescript
// lang/en-US/index.ts
import common from './common'
import layout from './layout'
import application from './application'

export default {
  common,
  layout,
  application
}
```

### 通用文本

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

### 布局文本

```typescript
// lang/en-US/layout.ts
export default {
  overview: 'Overview',
  servers: 'Servers',
  applications: 'Applications',
  appStore: 'App Store',
  configs: 'Configs',
  monitor: 'Monitor',
  logout: 'Logout'
}
```

### 应用管理文本

```typescript
// lang/en-US/application.ts
export default {
  title: 'Application Management',
  listTitle: 'Application List',
  addApplication: 'Add Application',
  editApplication: 'Edit Application',
  deleteApplication: 'Delete Application',
  viewDetail: 'View Detail',
  applicationName: 'Application Name',
  applicationType: 'Application Type',
  applicationVersion: 'Application Version',
  applicationDescription: 'Application Description',
  applicationContent: 'Application Content',
  operation: 'Operation',
  save: 'Save',
  cancel: 'Cancel',
  confirm: 'Confirm',
  confirmDelete: 'Confirm Delete',
  deleteWarning: 'Are you sure you want to delete application "{name}"? This action cannot be undone.',
  required: 'Required',
  createSuccess: 'Application created successfully',
  updateSuccess: 'Application updated successfully',
  deleteSuccess: 'Application deleted successfully',
  operationFailed: 'Operation failed',
  applicationDetail: 'Application Detail',
  basicInfo: 'Basic Info',
  searchPlaceholder: 'Search application name or description...',
  typeCompose: 'Compose',
  typeScript: 'Script',
  typeBinary: 'Binary',
  contentPreview: 'Content Preview',
  copy: 'Copy',
  copySuccess: 'Copied to clipboard',
  sortAsc: 'Ascending',
  sortDesc: 'Descending',
  importFromStore: 'Import from App Store',
  deploy: 'Deploy'
}
```

---

## 使用示例

### 在组件中使用

```vue
<template>
  <div>
    <h1>{{ $t('application.title') }}</h1>
    <input :placeholder="$t('application.searchPlaceholder')" />
    <button>{{ $t('common.save') }}</button>
  </div>
</template>

<script setup lang="ts">
import { useI18n } from 'vue-i18n'

const { t } = useI18n()

const showSuccess = () => {
  alert(t('application.createSuccess'))
}
</script>
```

### 动态文本插值

```vue
<template>
  <p>{{ $t('application.deleteWarning', { name: application.name }) }}</p>
</template>
```

### 切换语言

```typescript
import { useI18n } from 'vue-i18n'

const { locale } = useI18n()

const switchLanguage = (lang: 'zh-CN' | 'en-US') => {
  locale.value = lang
  localStorage.setItem('locale', lang)
}
```
