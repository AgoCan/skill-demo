# 登录页面代码实现

## 目录

- [依赖项](#依赖项)
- [路由配置](#路由配置)
- [Mock 数据](#mock-数据)
- [Vue 3 组件代码](#vue-3-组件代码)

## 依赖项

```json
{
  "vue": "^3.x",
  "vue-router": "^4.x",
  "@iconify/vue": "^4.x"
}
```

## 路由配置

```typescript
{
  path: '/login',
  name: 'Login',
  component: () => import('@/views/Login.vue'),
  meta: { requiresAuth: false }
}
```

## Mock 数据

```typescript
export const mockLogin = {
  token: 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.mock.token',
  user: {
    id: '1',
    username: 'admin',
    email: 'admin@squirrel.com'
  }
}
```

## Vue 3 组件代码

```vue
<template>
  <div class="login-page">
    <div class="login-container">
      <div class="login-left">
        <div class="brand">
          <Icon icon="lucide:squirrel" class="brand-icon" />
          <h1 class="brand-title">Squirrel</h1>
          <p class="brand-subtitle">轻量级运维平台</p>
        </div>
        <div class="features">
          <div class="feature-item">
            <Icon icon="lucide:zap" class="feature-icon" />
            <span>快速部署</span>
          </div>
          <div class="feature-item">
            <Icon icon="lucide:shield-check" class="feature-icon" />
            <span>安全可靠</span>
          </div>
          <div class="feature-item">
            <Icon icon="lucide:bar-chart-3" class="feature-icon" />
            <span>实时监控</span>
          </div>
        </div>
      </div>
      <div class="login-right">
        <div class="login-form-wrapper">
          <h2 class="login-title">欢迎回来</h2>
          <p class="login-subtitle">登录你的账户</p>
          <form class="login-form" @submit.prevent="handleLogin">
            <div class="form-group">
              <label class="form-label">用户名</label>
              <div class="input-wrapper">
                <Icon icon="lucide:user" class="input-icon" />
                <input
                  v-model="formData.username"
                  type="text"
                  class="form-input"
                  placeholder="请输入用户名"
                  required
                />
              </div>
            </div>
            <div class="form-group">
              <label class="form-label">密码</label>
              <div class="input-wrapper">
                <Icon icon="lucide:lock" class="input-icon" />
                <input
                  v-model="formData.password"
                  :type="showPassword ? 'text' : 'password'"
                  class="form-input"
                  placeholder="请输入密码"
                  required
                />
                <button
                  type="button"
                  class="toggle-password"
                  @click="showPassword = !showPassword"
                >
                  <Icon :icon="showPassword ? 'lucide:eye-off' : 'lucide:eye'" />
                </button>
              </div>
            </div>
            <div class="form-options">
              <label class="checkbox-label">
                <input v-model="formData.remember" type="checkbox" />
                <span>记住我</span>
              </label>
            </div>
            <button type="submit" class="login-btn" :disabled="loading">
              <span v-if="loading">登录中...</span>
              <span v-else>登录</span>
            </button>
          </form>
          <p class="login-tip">提示：使用 admin / admin123 登录</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { Icon } from '@iconify/vue'
import { mockLogin } from '@/mock'

const router = useRouter()

const formData = ref({
  username: '',
  password: '',
  remember: false
})

const showPassword = ref(false)
const loading = ref(false)

const handleLogin = async () => {
  loading.value = true
  await new Promise(resolve => setTimeout(resolve, 800))
  
  if (formData.value.username === 'admin' && formData.value.password === 'admin123') {
    localStorage.setItem('token', mockLogin.token)
    router.push('/dashboard')
  } else {
    alert('用户名或密码错误')
  }
  
  loading.value = false
}
</script>

<style scoped>
.login-page {
  width: 100%;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #1e3a5f 0%, #0f1f33 100%);
  padding: 20px;
}

.login-container {
  display: flex;
  width: 100%;
  max-width: 1000px;
  height: 600px;
  background: #ffffff;
  border-radius: 24px;
  overflow: hidden;
  box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
}

.login-left {
  flex: 1;
  background: linear-gradient(135deg, #1e3a5f 0%, #0f1f33 100%);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 40px;
  position: relative;
  overflow: hidden;
}

.login-left::before {
  content: '';
  position: absolute;
  width: 300px;
  height: 300px;
  background: rgba(79, 195, 247, 0.1);
  border-radius: 50%;
  top: -100px;
  right: -100px;
}

.login-left::after {
  content: '';
  position: absolute;
  width: 200px;
  height: 200px;
  background: rgba(79, 195, 247, 0.05);
  border-radius: 50%;
  bottom: -50px;
  left: -50px;
}

.brand {
  text-align: center;
  margin-bottom: 60px;
  z-index: 1;
}

.brand-icon {
  width: 64px;
  height: 64px;
  color: #4fc3f7;
  margin-bottom: 16px;
}

.brand-title {
  font-size: 36px;
  font-weight: 700;
  color: #ffffff;
  margin-bottom: 8px;
  letter-spacing: 1px;
}

.brand-subtitle {
  font-size: 16px;
  color: rgba(255, 255, 255, 0.6);
}

.features {
  display: flex;
  flex-direction: column;
  gap: 20px;
  z-index: 1;
}

.feature-item {
  display: flex;
  align-items: center;
  gap: 12px;
  color: rgba(255, 255, 255, 0.8);
  font-size: 14px;
}

.feature-icon {
  width: 20px;
  height: 20px;
  color: #4fc3f7;
}

.login-right {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 40px;
}

.login-form-wrapper {
  width: 100%;
  max-width: 360px;
}

.login-title {
  font-size: 28px;
  font-weight: 700;
  color: #1e3a5f;
  margin-bottom: 8px;
}

.login-subtitle {
  font-size: 14px;
  color: #64748b;
  margin-bottom: 32px;
}

.login-form {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.form-label {
  font-size: 14px;
  font-weight: 500;
  color: #1e3a5f;
}

.input-wrapper {
  position: relative;
  display: flex;
  align-items: center;
}

.input-icon {
  position: absolute;
  left: 14px;
  width: 20px;
  height: 20px;
  color: #94a3b8;
  pointer-events: none;
}

.form-input {
  width: 100%;
  padding: 14px 14px 14px 44px;
  font-size: 14px;
  border: 2px solid #e2e8f0;
  border-radius: 12px;
  background: #f8fafc;
  color: #1e3a5f;
  transition: all 0.3s ease;
}

.form-input:focus {
  outline: none;
  border-color: #4fc3f7;
  background: #ffffff;
  box-shadow: 0 0 0 4px rgba(79, 195, 247, 0.1);
}

.toggle-password {
  position: absolute;
  right: 14px;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 24px;
  height: 24px;
  background: none;
  border: none;
  color: #94a3b8;
  cursor: pointer;
  transition: color 0.3s ease;
}

.toggle-password:hover {
  color: #4fc3f7;
}

.form-options {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.checkbox-label {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 14px;
  color: #64748b;
  cursor: pointer;
}

.checkbox-label input {
  width: 16px;
  height: 16px;
  accent-color: #4fc3f7;
}

.login-btn {
  padding: 14px;
  font-size: 16px;
  font-weight: 600;
  color: #ffffff;
  background: linear-gradient(135deg, #4fc3f7 0%, #29b6f6 100%);
  border-radius: 12px;
  transition: all 0.3s ease;
  box-shadow: 0 4px 12px rgba(79, 195, 247, 0.4);
}

.login-btn:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: 0 6px 16px rgba(79, 195, 247, 0.5);
}

.login-btn:active:not(:disabled) {
  transform: translateY(0);
}

.login-btn:disabled {
  opacity: 0.7;
  cursor: not-allowed;
}

.login-tip {
  text-align: center;
  font-size: 12px;
  color: #94a3b8;
  margin-top: 20px;
}
</style>
```

## 设计要点

1. **分屏布局**: 左侧品牌展示，右侧登录表单
2. **深色渐变**: 使用深蓝色渐变营造专业感
3. **光晕效果**: 背景圆形光晕增加层次感
4. **图标使用**: 使用 Iconify 图标库 (`lucide` 图标集)
5. **交互反馈**: 输入框聚焦、按钮悬停等微交互
6. **响应式设计**: 适配不同屏幕尺寸
