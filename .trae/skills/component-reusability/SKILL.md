---
name: "component-reusability"
description: "Provides guidance on improving frontend component reusability and creating reusable UI components. Invoke when creating new components, refactoring existing components, or addressing component duplication issues."
---

# 前端组件复用性优化指南

## 概述

本技能提供前端组件复用性优化的最佳实践和指导原则，帮助开发者构建可复用、可维护的UI组件库。

## 何时使用

- 创建新的UI组件时
- 发现组件代码重复时
- 重构现有组件时
- 设计组件架构时
- 用户提到"组件复用性不足"时

## 核心原则

### 1. 单一职责原则 (SRP)

每个组件只负责一个功能点，避免组件职责过重。

**示例：**
- ✅ `Button.vue` - 只负责按钮功能
- ✅ `Input.vue` - 只负责输入框功能
- ❌ `FormWithButtonAndInput.vue` - 职责过多，应拆分

### 2. 可组合性原则

组件应该能够灵活组合，支持多种使用场景。

**示例：**
```vue
<!-- 可组合的组件 -->
<Modal>
  <ModalHeader>标题</ModalHeader>
  <ModalBody>内容</ModalBody>
  <ModalFooter>
    <Button>取消</Button>
    <Button type="primary">确认</Button>
  </ModalFooter>
</Modal>
```

### 3. Props设计原则

- **最小化Props**：只暴露必要的配置项
- **提供合理的默认值**：减少使用者的配置负担
- **使用TypeScript类型**：提供类型提示和验证

**示例：**
```typescript
interface ButtonProps {
  type?: 'primary' | 'secondary' | 'danger' | 'ghost'
  size?: 'small' | 'medium' | 'large'
  disabled?: boolean
  loading?: boolean
  icon?: string
  onClick?: () => void
}

const props = withDefaults(defineProps<ButtonProps>(), {
  type: 'primary',
  size: 'medium',
  disabled: false,
  loading: false
})
```

### 4. 插槽设计原则

使用插槽提供灵活性，支持自定义内容。

**示例：**
```vue
<template>
  <div class="card">
    <div v-if="$slots.header" class="card-header">
      <slot name="header"></slot>
    </div>
    <div class="card-body">
      <slot></slot>
    </div>
    <div v-if="$slots.footer" class="card-footer">
      <slot name="footer"></slot>
    </div>
  </div>
</template>
```

## 通用组件库设计

### 基础组件

#### 1. Button（按钮）

**功能：**
- 支持多种类型（primary、secondary、danger、ghost）
- 支持多种尺寸（small、medium、large）
- 支持禁用、加载状态
- 支持图标

**Props：**
```typescript
interface ButtonProps {
  type?: 'primary' | 'secondary' | 'danger' | 'ghost'
  size?: 'small' | 'medium' | 'large'
  disabled?: boolean
  loading?: boolean
  icon?: string
  block?: boolean
  plain?: boolean
  round?: boolean
  circle?: boolean
}
```

**使用示例：**
```vue
<Button type="primary" size="medium" @click="handleClick">
  提交
</Button>
<Button loading>加载中</Button>
<Button icon="search">搜索</Button>
```

#### 2. Input（输入框）

**功能：**
- 支持文本、密码、数字等类型
- 支持前后缀
- 支持清除按钮
- 支持字数限制
- 支持禁用、只读状态

**Props：**
```typescript
interface InputProps {
  modelValue?: string | number
  type?: 'text' | 'password' | 'number' | 'email' | 'tel'
  placeholder?: string
  disabled?: boolean
  readonly?: boolean
  clearable?: boolean
  maxlength?: number
  showWordLimit?: boolean
  prefixIcon?: string
  suffixIcon?: string
  size?: 'small' | 'medium' | 'large'
}
```

**使用示例：**
```vue
<Input v-model="username" placeholder="请输入用户名" clearable />
<Input v-model="password" type="password" show-password />
<Input v-model="age" type="number" :max="150" />
```

#### 3. Select（选择器）

**功能：**
- 支持单选、多选
- 支持搜索
- 支持分组
- 支持远程搜索

**Props：**
```typescript
interface SelectProps {
  modelValue?: string | number | string[] | number[]
  options?: Array<{ label: string; value: string | number; disabled?: boolean }>
  multiple?: boolean
  disabled?: boolean
  clearable?: boolean
  filterable?: boolean
  remote?: boolean
  remoteMethod?: (query: string) => Promise<any[]>
  loading?: boolean
  placeholder?: string
  size?: 'small' | 'medium' | 'large'
}
```

**使用示例：**
```vue
<Select v-model="value" :options="options" />
<Select v-model="values" multiple :options="options" />
<Select v-model="value" filterable remote :remote-method="remoteSearch" />
```

#### 4. Table（表格）

**功能：**
- 支持自定义列
- 支持排序
- 支持筛选
- 支持分页
- 支持选择行
- 支持展开行

**Props：**
```typescript
interface TableProps {
  data?: any[]
  columns?: Array<{
    prop: string
    label: string
    width?: string | number
    sortable?: boolean
    filterable?: boolean
    align?: 'left' | 'center' | 'right'
  }>
  stripe?: boolean
  border?: boolean
  highlightCurrentRow?: boolean
  showSelection?: boolean
  showPagination?: boolean
  pagination?: {
    currentPage: number
    pageSize: number
    total: number
  }
}
```

**使用示例：**
```vue
<Table
  :data="tableData"
  :columns="columns"
  :pagination="pagination"
  @page-change="handlePageChange"
/>
```

#### 5. Modal（模态框）

**功能：**
- 支持自定义头部、内容、底部
- 支持关闭遮罩关闭
- 支持自定义尺寸
- 支持全屏模式

**Props：**
```typescript
interface ModalProps {
  visible?: boolean
  title?: string
  width?: string | number
  fullscreen?: boolean
  closeOnClickModal?: boolean
  closeOnPressEscape?: boolean
  showClose?: boolean
  beforeClose?: () => boolean | Promise<boolean>
}
```

**使用示例：**
```vue
<Modal v-model:visible="showModal" title="提示" width="500px">
  <p>确定要删除吗？</p>
  <template #footer>
    <Button @click="showModal = false">取消</Button>
    <Button type="primary" @click="handleConfirm">确定</Button>
  </template>
</Modal>
```

#### 6. Form（表单）

**功能：**
- 支持表单验证
- 支持动态表单项
- 支持表单布局
- 支持重置、提交

**Props：**
```typescript
interface FormProps {
  model?: Record<string, any>
  rules?: Record<string, any>
  labelWidth?: string | number
  labelPosition?: 'left' | 'right' | 'top'
  inline?: boolean
  disabled?: boolean
}
```

**使用示例：**
```vue
<Form ref="formRef" :model="formData" :rules="rules">
  <FormItem label="用户名" prop="username">
    <Input v-model="formData.username" />
  </FormItem>
  <FormItem label="密码" prop="password">
    <Input v-model="formData.password" type="password" />
  </FormItem>
  <FormItem>
    <Button type="primary" @click="handleSubmit">提交</Button>
    <Button @click="handleReset">重置</Button>
  </FormItem>
</Form>
```

### 复合组件

#### 1. FormItem（表单项）

**Props：**
```typescript
interface FormItemProps {
  label?: string
  prop?: string
  required?: boolean
  rules?: any[]
  error?: string
  showMessage?: boolean
  inlineMessage?: boolean
}
```

#### 2. Card（卡片）

**Props：**
```typescript
interface CardProps {
  header?: string
  shadow?: 'always' | 'hover' | 'never'
  bodyStyle?: Record<string, any>
}
```

#### 3. Tag（标签）

**Props：**
```typescript
interface TagProps {
  type?: 'primary' | 'success' | 'info' | 'warning' | 'danger'
  size?: 'small' | 'medium' | 'large'
  effect?: 'dark' | 'light' | 'plain'
  closable?: boolean
  hit?: boolean
  color?: string
}
```

#### 4. Badge（徽标）

**Props：**
```typescript
interface BadgeProps {
  value?: string | number
  max?: number
  isDot?: boolean
  hidden?: boolean
  type?: 'primary' | 'success' | 'warning' | 'danger' | 'info'
}
```

## 组件复用性检查清单

### 设计阶段

- [ ] 组件职责是否单一？
- [ ] Props设计是否合理？是否有默认值？
- [ ] 是否提供了足够的插槽？
- [ ] 是否支持多种使用场景？
- [ ] 是否考虑了边界情况？

### 实现阶段

- [ ] 是否使用了TypeScript类型定义？
- [ ] 是否提供了完整的事件回调？
- [ ] 样式是否可定制？
- [ ] 是否支持主题切换？
- [ ] 是否支持国际化？

### 测试阶段

- [ ] 是否编写了单元测试？
- [ ] 是否测试了各种Props组合？
- [ ] 是否测试了边界情况？
- [ ] 是否测试了事件触发？
- [ ] 是否测试了插槽内容？

### 文档阶段

- [ ] 是否提供了使用示例？
- [ ] 是否说明了Props含义？
- [ ] 是否说明了Events含义？
- [ ] 是否说明了Slots含义？
- [ ] 是否提供了最佳实践？

## 组件复用性优化策略

### 1. 提取公共逻辑

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

### 2. 使用HOC（高阶组件）

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

### 3. 使用Renderless Components

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

### 4. 使用Provide/Inject

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

## 组件文档规范

### 组件文档模板

```markdown
# Button 按钮

基础按钮组件，支持多种类型和尺寸。

## 基础用法

\`\`\`vue
<Button type="primary">主要按钮</Button>
\`\`\`

## Props

| 参数 | 说明 | 类型 | 可选值 | 默认值 |
|------|------|------|--------|--------|
| type | 按钮类型 | string | primary / secondary / danger / ghost | primary |
| size | 按钮尺寸 | string | small / medium / large | medium |
| disabled | 是否禁用 | boolean | — | false |
| loading | 是否加载中 | boolean | — | false |

## Events

| 事件名 | 说明 | 回调参数 |
|--------|------|----------|
| click | 点击事件 | (event: MouseEvent) => void |

## Slots

| 插槽名 | 说明 |
|--------|------|
| default | 按钮内容 |
| icon | 图标 |

## 示例

### 不同类型

\`\`\`vue
<Button type="primary">主要按钮</Button>
<Button type="secondary">次要按钮</Button>
<Button type="danger">危险按钮</Button>
\`\`\`

### 不同尺寸

\`\`\`vue
<Button size="small">小按钮</Button>
<Button size="medium">中按钮</Button>
<Button size="large">大按钮</Button>
\`\`\`

### 加载状态

\`\`\`vue
<Button loading>加载中</Button>
\`\`\`
```

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

## 最佳实践总结

1. **设计优先**：先设计组件API，再实现功能
2. **渐进增强**：从简单到复杂，逐步完善
3. **文档先行**：编写清晰的文档和示例
4. **测试保障**：编写充分的单元测试
5. **持续重构**：根据使用反馈持续优化
6. **性能意识**：关注组件性能，避免不必要的渲染
7. **可访问性**：考虑无障碍访问，支持键盘操作
8. **国际化**：支持多语言，使用i18n

## 相关资源

- [Vue3官方文档](https://vuejs.org/)
- [Element Plus组件库](https://element-plus.org/)
- [Ant Design Vue](https://www.antdv.com/)
- [Vue组件设计模式](https://github.com/vuejs/core/issues/4175)
