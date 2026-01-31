---
name: "layout-nav-header"
description: "Squirrel 布局导航栏和顶部栏设计规范。包含侧边栏导航、Logo、用户信息、退出按钮等组件的样式和实现。在创建或修改布局时调用。"
---

# 布局导航栏和顶部栏设计

## 概述

这是 Squirrel 运维平台的主布局组件，包含侧边栏导航和顶部栏。

## 组件结构

```
main-layout
├── sidebar (侧边栏)
│   ├── logo (Logo 区域)
│   └── nav (导航菜单)
└── main-content (主内容区)
    ├── header (顶部栏)
    │   ├── header-left (页面标题)
    │   └── header-right (用户信息 + 退出按钮)
    └── content (页面内容)
```

## 侧边栏 (Sidebar)

### 样式规范

```css
.sidebar {
  width: 200px;
  background: linear-gradient(180deg, #1e3a5f 0%, #0f1f33 100%);
  display: flex;
  flex-direction: column;
  padding: 16px 0;
  box-shadow: 4px 0 12px rgba(0, 0, 0, 0.1);
}
```

### Logo 区域

```css
.logo {
  display: flex;
  align-items: center;
  padding: 0 16px;
  margin-bottom: 20px;
}

.logo-icon {
  width: 24px;
  height: 24px;
  color: #4fc3f7;
}

.logo-text {
  margin-left: 8px;
  font-size: 16px;
  font-weight: 700;
  color: #ffffff;
  letter-spacing: 0.5px;
}
```

### 导航菜单

```css
.nav {
  flex: 1;
  display: flex;
  flex-direction: column;
  padding: 0 8px;
}

.nav-item {
  display: flex;
  align-items: center;
  padding: 10px 12px;
  margin-bottom: 2px;
  border-radius: 4px;
  color: rgba(255, 255, 255, 0.6);
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  font-size: 13px;
  font-weight: 500;
}

.nav-item:hover {
  background: rgba(79, 195, 247, 0.15);
  color: #4fc3f7;
  transform: translateX(2px);
}

.nav-item.active {
  background: linear-gradient(135deg, #4fc3f7 0%, #29b6f6 100%);
  color: #ffffff;
  box-shadow: 0 2px 8px rgba(79, 195, 247, 0.4);
}

.nav-icon {
  width: 16px;
  height: 16px;
  margin-right: 8px;
}
```

## 顶部栏 (Header)

### 样式规范

```css
.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px 10px;
  background: #ffffff;
  border-bottom: 1px solid #e8ecf1;
}
```

### 页面标题

```css
.page-title {
  font-size: 18px;
  font-weight: 600;
  color: #1e3a5f;
}
```

### 用户信息

```css
.user-info {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 6px 12px;
  background: #f5f7fa;
  border-radius: 12px;
}

.user-icon {
  width: 16px;
  height: 16px;
  color: #4fc3f7;
}

.user-name {
  font-size: 13px;
  font-weight: 500;
  color: #1e3a5f;
}
```

### 退出按钮

```css
.logout-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 32px;
  height: 32px;
  border-radius: 6px;
  background: #f5f7fa;
  color: #64748b;
  transition: all 0.3s ease;
}

.logout-btn:hover {
  background: #fee2e2;
  color: #ef4444;
  transform: rotate(-5deg);
}
```

## 导航菜单项

```typescript
const navItems = [
  { path: '/dashboard', label: '概览', icon: 'lucide:layout-dashboard' },
  { path: '/servers', label: '服务器', icon: 'lucide:server' },
  { path: '/applications', label: '应用', icon: 'lucide:box' },
  { path: '/monitor', label: '监控', icon: 'lucide:activity' },
  { path: '/scripts', label: '脚本', icon: 'lucide:terminal' },
  { path: '/configs', label: '配置', icon: 'lucide:settings' },
  { path: '/deployments', label: '部署', icon: 'lucide:rocket' },
  { path: '/appstore', label: '应用商店', icon: 'lucide:shopping-bag' }
]
```

## 完整代码实现

```vue
<template>
  <div class="main-layout">
    <aside class="sidebar">
      <div class="logo">
        <Icon icon="lucide:squirrel" class="logo-icon" />
        <span class="logo-text">Squirrel</span>
      </div>
      <nav class="nav">
        <router-link
          v-for="item in navItems"
          :key="item.path"
          :to="item.path"
          class="nav-item"
          :class="{ active: isActive(item.path) }"
        >
          <Icon :icon="item.icon" class="nav-icon" />
          <span>{{ item.label }}</span>
        </router-link>
      </nav>
    </aside>
    <div class="main-content">
      <header class="header">
        <div class="header-left">
          <h1 class="page-title">{{ pageTitle }}</h1>
        </div>
        <div class="header-right">
          <div class="user-info">
            <Icon icon="lucide:user" class="user-icon" />
            <span class="user-name">Admin</span>
          </div>
          <button class="logout-btn" @click="handleLogout">
            <Icon icon="lucide:log-out" />
          </button>
        </div>
      </header>
      <main class="content">
        <router-view />
      </main>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { Icon } from '@iconify/vue'

const route = useRoute()
const router = useRouter()

const navItems = [
  { path: '/dashboard', label: '概览', icon: 'lucide:layout-dashboard' },
  { path: '/servers', label: '服务器', icon: 'lucide:server' },
  { path: '/applications', label: '应用', icon: 'lucide:box' },
  { path: '/monitor', label: '监控', icon: 'lucide:activity' },
  { path: '/scripts', label: '脚本', icon: 'lucide:terminal' },
  { path: '/configs', label: '配置', icon: 'lucide:settings' },
  { path: '/deployments', label: '部署', icon: 'lucide:rocket' },
  { path: '/appstore', label: '应用商店', icon: 'lucide:shopping-bag' }
]

const pageTitle = computed(() => {
  const item = navItems.find(n => route.path.startsWith(n.path))
  return item?.label || 'Squirrel'
})

const isActive = (path: string) => {
  return route.path === path || route.path.startsWith(path + '/')
}

const handleLogout = () => {
  localStorage.removeItem('token')
  router.push('/login')
}
</script>

<style scoped>
.main-layout {
  display: flex;
  width: 100%;
  height: 100vh;
  background: #f5f7fa;
}

.sidebar {
  width: 200px;
  background: linear-gradient(180deg, #1e3a5f 0%, #0f1f33 100%);
  display: flex;
  flex-direction: column;
  padding: 16px 0;
  box-shadow: 4px 0 12px rgba(0, 0, 0, 0.1);
}

.logo {
  display: flex;
  align-items: center;
  padding: 0 16px;
  margin-bottom: 20px;
}

.logo-icon {
  width: 24px;
  height: 24px;
  color: #4fc3f7;
}

.logo-text {
  margin-left: 8px;
  font-size: 16px;
  font-weight: 700;
  color: #ffffff;
  letter-spacing: 0.5px;
}

.nav {
  flex: 1;
  display: flex;
  flex-direction: column;
  padding: 0 8px;
}

.nav-item {
  display: flex;
  align-items: center;
  padding: 10px 12px;
  margin-bottom: 2px;
  border-radius: 4px;
  color: rgba(255, 255, 255, 0.6);
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  font-size: 13px;
  font-weight: 500;
}

.nav-item:hover {
  background: rgba(79, 195, 247, 0.15);
  color: #4fc3f7;
  transform: translateX(2px);
}

.nav-item.active {
  background: linear-gradient(135deg, #4fc3f7 0%, #29b6f6 100%);
  color: #ffffff;
  box-shadow: 0 2px 8px rgba(79, 195, 247, 0.4);
}

.nav-icon {
  width: 16px;
  height: 16px;
  margin-right: 8px;
}

.main-content {
  flex: 1;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px 10px;
  background: #ffffff;
  border-bottom: 1px solid #e8ecf1;
}

.header-left {
  display: flex;
  align-items: center;
}

.page-title {
  font-size: 18px;
  font-weight: 600;
  color: #1e3a5f;
}

.header-right {
  display: flex;
  align-items: center;
  gap: 12px;
}

.user-info {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 6px 12px;
  background: #f5f7fa;
  border-radius: 12px;
}

.user-icon {
  width: 16px;
  height: 16px;
  color: #4fc3f7;
}

.user-name {
  font-size: 13px;
  font-weight: 500;
  color: #1e3a5f;
}

.logout-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 32px;
  height: 32px;
  border-radius: 6px;
  background: #f5f7fa;
  color: #64748b;
  transition: all 0.3s ease;
}

.logout-btn:hover {
  background: #fee2e2;
  color: #ef4444;
  transform: rotate(-5deg);
}

.content {
  flex: 1;
  padding: 20px;
  overflow-y: auto;
}
</style>
```

## 配色方案

- **侧边栏背景**: `linear-gradient(180deg, #1e3a5f 0%, #0f1f33 100%)`
- **Logo 颜色**: `#4fc3f7`
- **导航激活色**: `linear-gradient(135deg, #4fc3f7 0%, #29b6f6 100%)`
- **页面标题**: `#1e3a5f`
- **用户信息背景**: `#f5f7fa`
- **退出按钮悬停**: `#fee2e2` / `#ef4444`

## 尺寸规范

- **侧边栏宽度**: 200px
- **Logo 图标**: 24px × 24px
- **导航图标**: 16px × 16px
- **导航项圆角**: 4px
- **页面标题字号**: 18px
- **用户图标**: 16px × 16px
- **退出按钮**: 32px × 32px
- **Header padding**: 10px 10px

## 交互效果

- **导航项悬停**: 背景变化 + 右移 2px
- **导航项激活**: 渐变背景 + 阴影
- **退出按钮悬停**: 背景变红 + 旋转 -5deg
- **所有过渡**: 0.3s ease
