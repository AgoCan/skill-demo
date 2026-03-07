# vue-i18n 代码示例

本文档提供 vue-i18n 的完整代码示例，包括语言切换组件、动态导航菜单等。

## 目录

- [语言切换组件](#语言切换组件)
- [动态导航菜单](#动态导航菜单)
- [在组件中使用](#在组件中使用)
- [样式参考](#样式参考)

## 语言切换组件

### 完整的语言切换器组件

**components/LangSwitcher.vue**

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

<style scoped>
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
  font-size: 14px;
  transition: background 0.2s;
}

.lang-btn:hover {
  background: #e8ecf0;
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

.lang-menu button {
  display: block;
  width: 100%;
  padding: 8px 12px;
  text-align: left;
  border: none;
  background: none;
  cursor: pointer;
  border-radius: 4px;
  font-size: 14px;
}

.lang-menu button:hover {
  background: #f5f7fa;
}

.lang-menu button.active {
  background: #e0f2fe;
  color: #0284c7;
}
</style>
```

### 使用示例

```vue
<template>
  <header class="header">
    <div class="logo">Squirrel</div>
    <LangSwitcher />
  </header>
</template>

<script setup lang="ts">
import LangSwitcher from '@/components/LangSwitcher.vue'
</script>
```

## 动态导航菜单

### 响应式的导航菜单

```vue
<template>
  <nav class="sidebar">
    <router-link
      v-for="item in navItems"
      :key="item.path"
      :to="item.path"
      class="nav-item"
    >
      <Icon :icon="item.icon" />
      <span>{{ item.label }}</span>
    </router-link>
  </nav>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { useI18n } from 'vue-i18n'

const { t } = useI18n()

const navItems = computed(() => [
  { path: '/', label: t('layout.overview'), icon: 'lucide:layout-dashboard' },
  { path: '/servers', label: t('layout.servers'), icon: 'lucide:server' },
  { path: '/applications', label: t('layout.applications'), icon: 'lucide:box' },
  { path: '/monitor', label: t('layout.monitor'), icon: 'lucide:activity' },
  { path: '/scripts', label: t('layout.scripts'), icon: 'lucide:terminal' },
  { path: '/configs', label: t('layout.configs'), icon: 'lucide:settings' }
])
</script>

<style scoped>
.sidebar {
  width: 200px;
  background: #f8fafc;
  padding: 16px 0;
}

.nav-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 12px 24px;
  color: #64748b;
  text-decoration: none;
  transition: all 0.2s;
}

.nav-item:hover {
  background: #e2e8f0;
  color: #334155;
}

.nav-item.router-link-active {
  background: #e0f2fe;
  color: #0284c7;
}
</style>
```

## 在组件中使用

### 模板中使用

```vue
<template>
  <div class="login-page">
    <h1>{{ $t('login.welcomeBack') }}</h1>
    <form @submit.prevent="handleLogin">
      <input
        :placeholder="$t('login.username')"
        v-model="username"
      />
      <input
        type="password"
        :placeholder="$t('login.password')"
        v-model="password"
      />
      <button type="submit">
        {{ $t('login.signIn') }}
      </button>
    </form>
  </div>
</template>
```

### Script 中使用

```vue
<script setup lang="ts">
import { computed } from 'vue'
import { useI18n } from 'vue-i18n'

const { t } = useI18n()

const pageTitle = computed(() => {
  return t('layout.overview')
})

const showMessage = (success: boolean) => {
  const message = success ? t('common.success') : t('common.failed')
  alert(message)
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
  { key: 'name', title: t('common.name') },
  { key: 'status', title: t('common.status') },
  { key: 'createTime', title: t('common.createTime') },
  { key: 'action', title: t('common.action') }
])
</script>
```

## 样式参考

### 语言切换器样式

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
  font-size: 14px;
  transition: background 0.2s;
  
  &:hover {
    background: #e8ecf0;
  }
  
  .icon {
    width: 16px;
    height: 16px;
  }
  
  .chevron {
    transition: transform 0.2s;
    
    &.open {
      transform: rotate(180deg);
    }
  }
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
  
  button {
    display: block;
    width: 100%;
    padding: 8px 12px;
    text-align: left;
    border: none;
    background: none;
    cursor: pointer;
    border-radius: 4px;
    font-size: 14px;
    transition: background 0.2s;
    
    &:hover {
      background: #f5f7fa;
    }
    
    &.active {
      background: #e0f2fe;
      color: #0284c7;
    }
  }
}
```

## 高级用法

### 带参数的翻译

```typescript
export default {
  welcome: '欢迎回来，{name}！',
  itemsCount: '共 {count} 个项目'
}
```

```vue
<template>
  <p>{{ $t('common.welcome', { name: 'John' }) }}</p>
  <p>{{ $t('common.itemsCount', { count: 10 }) }}</p>
</template>
```

### 复数形式

```typescript
export default {
  car: '车 | 车',
  apple: '没有苹果 | 一个苹果 | {count} 个苹果'
}
```

```vue
<template>
  <p>{{ $tc('common.car', 1) }}</p>
  <p>{{ $tc('common.car', 2) }}</p>
  <p>{{ $tc('common.apple', 0) }}</p>
  <p>{{ $tc('common.apple', 1) }}</p>
  <p>{{ $tc('common.apple', 10, { count: 10 }) }}</p>
</template>
```

### 日期和数字格式化

```typescript
const { d, n } = useI18n()

const formattedDate = d(new Date(), 'short')
const formattedNumber = n(1000, 'currency')
```
