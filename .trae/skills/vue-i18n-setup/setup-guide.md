# vue-i18n 配置指南

本文档提供 vue-i18n 的详细配置步骤，包括安装、配置和使用。

## 目录

- [安装依赖](#安装依赖)
- [创建目录结构](#创建目录结构)
- [配置 i18n 实例](#配置-i18n-实例)
- [创建语言包](#创建语言包)
- [引入到项目](#引入到项目)
- [验证配置](#验证配置)

## 安装依赖

```bash
npm install vue-i18n@9
```

## 创建目录结构

在 `src` 目录下创建 `lang` 目录：

```
src/lang/
├── index.ts              # i18n 配置入口
├── zh-CN/                # 中文语言包
│   ├── index.ts          # 中文语言包入口
│   ├── common.ts         # 通用文本
│   ├── login.ts          # 登录页
│   ├── overview.ts       # 概览页
│   └── layout.ts         # 布局导航
└── en-US/                # 英文语言包
    ├── index.ts          # 英文语言包入口
    ├── common.ts
    ├── login.ts
    ├── overview.ts
    └── layout.ts
```

## 配置 i18n 实例

### 创建 src/lang/index.ts

```typescript
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

### 配置说明

- `legacy: false`：使用 Composition API 模式
- `locale`：当前语言，从 localStorage 读取或使用默认值
- `fallbackLocale`：后备语言，当翻译缺失时使用
- `messages`：语言包对象

## 创建语言包

### 中文语言包

**src/lang/zh-CN/index.ts**

```typescript
import common from './common'
import login from './login'
import overview from './overview'
import layout from './layout'

export default {
  common,
  login,
  overview,
  layout
}
```

**src/lang/zh-CN/common.ts**

```typescript
export default {
  confirm: '确认',
  cancel: '取消',
  save: '保存',
  delete: '删除',
  loading: '加载中...',
  success: '操作成功',
  failed: '操作失败',
  search: '搜索',
  reset: '重置',
  add: '添加',
  edit: '编辑',
  detail: '详情',
  status: '状态',
  action: '操作',
  createTime: '创建时间',
  updateTime: '更新时间'
}
```

**src/lang/zh-CN/layout.ts**

```typescript
export default {
  overview: '概览',
  servers: '服务器',
  applications: '应用',
  monitor: '监控',
  scripts: '脚本',
  configs: '配置',
  logout: '退出登录',
  profile: '个人中心'
}
```

### 英文语言包

**src/lang/en-US/index.ts**

```typescript
import common from './common'
import login from './login'
import overview from './overview'
import layout from './layout'

export default {
  common,
  login,
  overview,
  layout
}
```

**src/lang/en-US/common.ts**

```typescript
export default {
  confirm: 'Confirm',
  cancel: 'Cancel',
  save: 'Save',
  delete: 'Delete',
  loading: 'Loading...',
  success: 'Success',
  failed: 'Failed',
  search: 'Search',
  reset: 'Reset',
  add: 'Add',
  edit: 'Edit',
  detail: 'Detail',
  status: 'Status',
  action: 'Action',
  createTime: 'Create Time',
  updateTime: 'Update Time'
}
```

**src/lang/en-US/layout.ts**

```typescript
export default {
  overview: 'Overview',
  servers: 'Servers',
  applications: 'Applications',
  monitor: 'Monitor',
  scripts: 'Scripts',
  configs: 'Configs',
  logout: 'Logout',
  profile: 'Profile'
}
```

## 引入到项目

在 `src/main.ts` 中引入 i18n：

```typescript
import { createApp } from 'vue'
import App from './App.vue'
import { i18n } from './lang'

const app = createApp(App)
app.use(i18n)
app.mount('#app')
```

## 验证配置

### 在组件中使用

```vue
<template>
  <div>
    <h1>{{ $t('layout.overview') }}</h1>
    <p>{{ $t('common.loading') }}</p>
  </div>
</template>

<script setup lang="ts">
import { useI18n } from 'vue-i18n'

const { t } = useI18n()

console.log(t('common.confirm'))
</script>
```

### 检查清单

- [ ] vue-i18n@9 已安装
- [ ] src/lang 目录已创建
- [ ] i18n 实例已配置
- [ ] 语言包文件已创建
- [ ] main.ts 已引入 i18n
- [ ] 组件中可以使用 $t() 函数
- [ ] 语言切换后刷新页面仍保持选择的语言
