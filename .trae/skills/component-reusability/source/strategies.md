# 组件复用性优化策略

## 目录

- [提取公共逻辑](#提取公共逻辑)
- [使用HOC（高阶组件）](#使用hoc高阶组件)
- [使用Renderless Components](#使用renderless-components)
- [使用Provide/Inject](#使用provideinject)
- [组件性能优化](#组件性能优化)
- [组件测试策略](#组件测试策略)
- [常见问题](#常见问题)

---

## 提取公共逻辑

使用Composables提取可复用的逻辑。

**示例：**
```typescript
// composables/useLoading.ts
export function useLoading() {
  const loading = ref(false)

  const withLoading = async (fn: () => Promise<any>) => {
    loading.value = true
    try {
      return await fn()
    } finally {
      loading.value = false
    }
  }

  return { loading, withLoading }
}

// 在组件中使用
const { loading, withLoading } = useLoading()

const handleSubmit = async () => {
  await withLoading(async () => {
    await submitForm()
  })
}
```

---

## 使用HOC（高阶组件）

为组件添加通用功能。

**示例：**
```typescript
// withLoading.ts
export function withLoading<T extends object>(component: T) {
  return defineComponent({
    setup(props, { slots }) {
      const loading = ref(false)

      return () => (
        loading.value ? <Loading /> : h(component, props, slots)
      )
    }
  })
}
```

---

## 使用Renderless Components

只提供逻辑，不提供UI。

**示例：**
```vue
<!-- RenderlessCheckbox.vue -->
<script setup lang="ts">
const props = defineProps<{ modelValue: boolean }>()
const emit = defineEmits<{ 'update:modelValue': [value: boolean] }>()

const checked = computed({
  get: () => props.modelValue,
  set: (val) => emit('update:modelValue', val)
})
</script>

<template>
  <slot :checked="checked" :toggle="() => checked = !checked"></slot>
</template>

<!-- 使用 -->
<RenderlessCheckbox v-model="checked">
  <template #default="{ checked, toggle }">
    <div :class="{ active: checked }" @click="toggle">
      自定义复选框
    </div>
  </template>
</RenderlessCheckbox>
```

---

## 使用Provide/Inject

跨层级组件通信。

**示例：**
```typescript
// 父组件
provide('theme', {
  primaryColor: '#1890ff',
  borderRadius: '4px'
})

// 子组件
const theme = inject('theme')
```

---

## 组件性能优化

### 1. 使用v-once

静态内容使用v-once避免重复渲染。

```vue
<template>
  <div v-once>静态内容</div>
</template>
```

### 2. 使用computed缓存

```typescript
const filteredList = computed(() => {
  return list.value.filter(item => item.active)
})
```

### 3. 使用v-memo

Vue3.2+支持，条件缓存。

```vue
<template>
  <div v-memo="[item.id]">
    {{ item.name }}
  </div>
</template>
```

### 4. 懒加载组件

```typescript
const HeavyComponent = defineAsyncComponent(() =>
  import('./HeavyComponent.vue')
)
```

### 5. 虚拟滚动

长列表使用虚拟滚动。

```vue
<VirtualList :items="largeList" :item-size="50">
  <template #default="{ item }">
    <div>{{ item.name }}</div>
  </template>
</VirtualList>
```

---

## 组件测试策略

### 单元测试

```typescript
import { mount } from '@vue/test-utils'
import Button from './Button.vue'

describe('Button', () => {
  it('renders correctly', () => {
    const wrapper = mount(Button, {
      props: { type: 'primary' }
    })
    expect(wrapper.classes()).toContain('button-primary')
  })

  it('emits click event', async () => {
    const wrapper = mount(Button)
    await wrapper.trigger('click')
    expect(wrapper.emitted()).toHaveProperty('click')
  })
})
```

### 快照测试

```typescript
it('matches snapshot', () => {
  const wrapper = mount(Button)
  expect(wrapper.html()).toMatchSnapshot()
})
```

---

## 常见问题

### Q1: 何时创建通用组件？

**A:** 当满足以下条件时：
- 在多个地方使用相同或相似的UI元素
- 组件逻辑相对独立
- 组件可以通过参数配置适应不同场景

### Q2: 如何避免组件过度抽象？

**A:**
- 不要为了复用而复用
- 保持组件简单，避免过度配置
- 提供默认行为，减少配置负担
- 遵循YAGNI原则（You Aren't Gonna Need It）

### Q3: 如何处理组件样式冲突？

**A:**
- 使用CSS Modules或Scoped CSS
- 使用CSS变量实现主题定制
- 提供样式覆盖接口
- 使用BEM命名规范

### Q4: 如何处理组件版本兼容性？

**A:**
- 遵循语义化版本（SemVer）
- 提供迁移指南
- 使用TypeScript类型检查
- 编写充分的测试
