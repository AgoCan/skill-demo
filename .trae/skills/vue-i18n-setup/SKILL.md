---
name: "vue-i18n-setup"
description: "Setup vue-i18n internationalization for Vue3 projects with language switching. Invoke when user needs to add i18n support, create multi-language UI, or implement language toggle functionality."
---

# Vue3 + vue-i18n 国际化配置

为 Vue3 项目配置完整的国际化解决方案，包括语言包结构、切换组件和持久化存储。

## 安装依赖

```bash
npm install vue-i18n@9
```

## 目录结构

```
src/lang/
├── index.ts              # i18n 配置入口
├── zh-CN/                # 中文语言包
│   ├── index.ts
│   ├── common.ts         # 通用文本
│   ├── login.ts          # 登录页
│   ├── overview.ts       # 概览页
│   └── layout.ts         # 布局导航
└── en-US/                # 英文语言包
    ├── index.ts
    ├── common.ts
    ├── login.ts
    ├── overview.ts
    └── layout.ts
```

## 1. 创建 i18n 配置

**src/lang/index.ts**

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

## 2. 创建语言包

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
  // ...
}
```

**src/lang/zh-CN/layout.ts**

```typescript
export default {
  overview: '概览',
  servers: '服务器',
  applications: '应用',
  monitor: '监控',
  // ...
}
```

## 3. 在 main.ts 中引入

```typescript
import { createApp } from 'vue'
import App from './App.vue'
import { i18n } from './lang'

const app = createApp(App)
app.use(i18n)
app.mount('#app')
```

## 4. 创建语言切换组件

**Header 组件中的语言切换器**

```vue
<template>
  <div class="lang-switcher">
    <button class="lang-btn" @click="showLangMenu = !showLangMenu">
      <Icon icon="lucide:globe" />
      <span>{{ currentLocaleName }}</span>
      <Icon icon="lucide:chevron-down" :class="{ open: showLangMenu }" />
    </button>
    <div v-if="showLangMenu" class="lang-menu">
      <button
        v-for="loc in availableLocales"
        :key="loc.code"
        :class="{ active: currentLocale === loc.code }"
        @click="switchLocale(loc.code)"
      >
        {{ loc.name }}
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { useI18n } from 'vue-i18n'
import { availableLocales } from '@/lang'

const { locale } = useI18n()
const showLangMenu = ref(false)

const currentLocale = computed(() => locale.value)
const currentLocaleName = computed(() => {
  const loc = availableLocales.find(l => l.code === locale.value)
  return loc?.name || locale.value
})

const switchLocale = (code: string) => {
  locale.value = code
  localStorage.setItem('locale', code)
  showLangMenu.value = false
}
</script>
```

## 5. 在组件中使用

**模板中使用**

```vue
<template>
  <h1>{{ $t('login.welcomeBack') }}</h1>
  <p>{{ $t('overview.status') }}</p>
</template>
```

**Script 中使用**

```vue
<script setup lang="ts">
import { useI18n } from 'vue-i18n'
import { computed } from 'vue'

const { t } = useI18n()

const pageTitle = computed(() => {
  return t('layout.overview')
})
</script>
```

## 6. 动态导航菜单示例

```vue
<script setup lang="ts">
import { computed } from 'vue'
import { useI18n } from 'vue-i18n'

const { t } = useI18n()

const navItems = computed(() => [
  { path: '/', label: t('layout.overview'), icon: 'lucide:layout-dashboard' },
  { path: '/servers', label: t('layout.servers'), icon: 'lucide:server' },
  // ...
])
</script>
```

## 样式参考

```scss
.lang-switcher {
  position: relative;
}

.lang-btn {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 6px 12px;
  background: #f5f7fa;
  border-radius: 6px;
  border: none;
  cursor: pointer;
}

.lang-menu {
  position: absolute;
  top: 100%;
  right: 0;
  margin-top: 4px;
  background: #ffffff;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  padding: 4px;
  min-width: 120px;
  z-index: 100;
}

.lang-option {
  display: block;
  width: 100%;
  padding: 8px 12px;
  text-align: left;
  border: none;
  background: none;
  cursor: pointer;
  
  &.active {
    background: #e0f2fe;
    color: #0284c7;
  }
}
```

## 最佳实践

1. **按功能模块组织语言包**：login、overview、layout 等
2. **使用嵌套命名空间**：`$t('layout.overview')`、`$t('login.username')`
3. **持久化存储**：切换语言时保存到 localStorage
4. **动态计算**：导航菜单等使用 computed 包裹，语言切换自动更新
5. **fallbackLocale**：设置默认语言作为后备
