# 组件复用性核心原则

## 目录

- [单一职责原则 (SRP)](#单一职责原则-srp)
- [可组合性原则](#可组合性原则)
- [Props设计原则](#props设计原则)
- [插槽设计原则](#插槽设计原则)

---

## 单一职责原则 (SRP)

每个组件只负责一个功能点，避免组件职责过重。

**示例：**
- ✅ `Button.vue` - 只负责按钮功能
- ✅ `Input.vue` - 只负责输入框功能
- ❌ `FormWithButtonAndInput.vue` - 职责过多，应拆分

---

## 可组合性原则

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

---

## Props设计原则

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

---

## 插槽设计原则

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

---

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
