# 通用组件库设计

## 目录

- [基础组件](#基础组件)
- [复合组件](#复合组件)

---

## 基础组件

### 1. Button（按钮）

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

---

### 2. Input（输入框）

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

---

### 3. Select（选择器）

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

---

### 4. Table（表格）

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

---

### 5. Modal（模态框）

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

---

### 6. Form（表单）

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

---

## 复合组件

### FormItem（表单项）

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

### Card（卡片）

```typescript
interface CardProps {
  header?: string
  shadow?: 'always' | 'hover' | 'never'
  bodyStyle?: Record<string, any>
}
```

### Tag（标签）

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

### Badge（徽标）

```typescript
interface BadgeProps {
  value?: string | number
  max?: number
  isDot?: boolean
  hidden?: boolean
  type?: 'primary' | 'success' | 'warning' | 'danger' | 'info'
}
```
