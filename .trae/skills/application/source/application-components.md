# Application 组件实现详解

## 目录

1. [通用组件](#通用组件)
2. [ApplicationTable 组件](#applicationtable-组件)
3. [ApplicationForm 组件](#applicationform-组件)
4. [ApplicationDetail 组件](#applicationdetail-组件)
5. [DeleteConfirm 组件](#deleteconfirm-组件)
6. [主页面逻辑](#主页面逻辑)

---

## 通用组件

### Toast 组件

```vue
<template>
  <Transition name="toast">
    <div v-if="visible" class="toast" :class="`toast--${type}`">
      <Icon :icon="getIcon()" class="toast-icon" />
      <span class="toast-message">{{ message }}</span>
    </div>
  </Transition>
</template>

<script setup lang="ts">
import { Icon } from '@iconify/vue'

interface Props {
  visible?: boolean
  message?: string
  type?: 'success' | 'error' | 'warning' | 'info'
}

const props = withDefaults(defineProps<Props>(), {
  visible: false,
  message: '',
  type: 'success'
})

const getIcon = () => {
  const icons: Record<string, string> = {
    success: 'lucide:check-circle',
    error: 'lucide:x-circle',
    warning: 'lucide:alert-triangle',
    info: 'lucide:info'
  }
  return icons[props.type]
}
</script>
```

### Button 组件

```vue
<template>
  <button
    :class="[
      'sq-button',
      `sq-button--${type}`,
      `sq-button--${size}`,
      {
        'sq-button--disabled': disabled,
        'sq-button--loading': loading,
        'sq-button--block': block
      }
    ]"
    :disabled="disabled || loading"
    @click="handleClick"
  >
    <span v-if="loading" class="sq-button__loading">
      <Icon icon="lucide:loader-2" class="animate-spin" />
    </span>
    <span v-if="icon && !loading" class="sq-button__icon">
      <Icon :icon="icon" />
    </span>
    <span class="sq-button__content">
      <slot />
    </span>
  </button>
</template>

<script setup lang="ts">
import { Icon } from '@iconify/vue'

interface Props {
  type?: 'primary' | 'secondary' | 'danger' | 'ghost'
  size?: 'small' | 'medium' | 'large'
  disabled?: boolean
  loading?: boolean
  icon?: string
  block?: boolean
}

withDefaults(defineProps<Props>(), {
  type: 'primary',
  size: 'medium',
  disabled: false,
  loading: false,
  block: false
})

const emit = defineEmits<{
  click: [event: MouseEvent]
}>()

const handleClick = (event: MouseEvent) => {
  emit('click', event)
}
</script>
```

### Loading 组件

```vue
<template>
  <div class="loading-container">
    <Icon icon="lucide:loader-2" class="loading-icon animate-spin" />
    <span v-if="text" class="loading-text">{{ text }}</span>
  </div>
</template>

<script setup lang="ts">
import { Icon } from '@iconify/vue'

interface Props {
  text?: string
}

withDefaults(defineProps<Props>(), {
  text: ''
})
</script>
```

### Empty 组件

```vue
<template>
  <div class="empty-container">
    <Icon :icon="icon" class="empty-icon" />
    <p class="empty-text">{{ description }}</p>
    <slot name="action" />
  </div>
</template>

<script setup lang="ts">
import { Icon } from '@iconify/vue'

interface Props {
  description?: string
  icon?: string
}

withDefaults(defineProps<Props>(), {
  description: '暂无数据',
  icon: 'lucide:inbox'
})
</script>
```

---

## ApplicationTable 组件

### 功能特性

- 使用表格行布局展示应用列表
- 显示应用名称、类型、版本、描述
- 支持排序功能（点击表头切换升序/降序）
- 操作按钮：查看详情、编辑、删除、部署
- 描述过长时使用省略号显示

### 实现代码

```vue
<template>
  <div class="application-table-container">
    <table class="application-table">
      <thead>
        <tr>
          <th class="sortable" @click="$emit('sort', 'name')">
            {{ $t('application.applicationName') }}
            <Icon :icon="getSortIcon('name')" class="sort-icon" />
          </th>
          <th class="sortable" @click="$emit('sort', 'type')">
            {{ $t('application.applicationType') }}
            <Icon :icon="getSortIcon('type')" class="sort-icon" />
          </th>
          <th class="sortable" @click="$emit('sort', 'version')">
            {{ $t('application.applicationVersion') }}
            <Icon :icon="getSortIcon('version')" class="sort-icon" />
          </th>
          <th>{{ $t('application.applicationDescription') }}</th>
          <th>{{ $t('common.operation') }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="app in applications" :key="app.id" class="application-row">
          <td class="name-cell">
            <div class="name-wrapper">
              <Icon :icon="getTypeIcon(app.type)" class="type-icon" />
              <span class="name-text">{{ app.name }}</span>
            </div>
          </td>
          <td class="type-cell">
            <span class="type-badge" :class="`type-badge--${app.type}`">
              {{ getTypeLabel(app.type) }}
            </span>
          </td>
          <td class="version-cell">
            <span class="version-text">{{ app.version }}</span>
          </td>
          <td class="description-cell">
            <div class="description-wrapper">
              <span class="description-text" :title="app.description">{{ app.description }}</span>
            </div>
          </td>
          <td class="action-cell">
            <button class="action-btn view-btn" :title="$t('application.viewDetail')" @click="$emit('view', app)">
              <Icon icon="lucide:eye" />
            </button>
            <button class="action-btn edit-btn" :title="$t('application.editApplication')" @click="$emit('edit', app)">
              <Icon icon="lucide:edit-2" />
            </button>
            <button class="action-btn delete-btn" :title="$t('application.deleteApplication')" @click="$emit('delete', app)">
              <Icon icon="lucide:trash-2" />
            </button>
            <button class="action-btn deploy-btn" :title="$t('application.deploy')" @click="$emit('deploy', app)">
              <Icon icon="lucide:rocket" />
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup lang="ts">
import { Icon } from '@iconify/vue'
import type { Application } from '@/types'

const props = defineProps<{
  applications: Application[]
  sortBy?: string | null
  sortOrder?: 'asc' | 'desc'
}>()

defineEmits<{
  edit: [application: Application]
  delete: [application: Application]
  view: [application: Application]
  deploy: [application: Application]
  sort: [field: string]
}>()

const getSortIcon = (field: string) => {
  if (field !== props.sortBy) return 'lucide:chevrons-up-down'
  return props.sortOrder === 'asc' ? 'lucide:chevron-up' : 'lucide:chevron-down'
}

const getTypeIcon = (type: string) => {
  const icons: Record<string, string> = {
    compose: 'lucide:box',
    script: 'lucide:terminal',
    binary: 'lucide:file-code'
  }
  return icons[type] || 'lucide:package'
}

const getTypeLabel = (type: string) => {
  const labels: Record<string, string> = {
    compose: 'Compose',
    script: '脚本',
    binary: '二进制'
  }
  return labels[type] || type
}
</script>
```

---

## ApplicationForm 组件

### 功能特性

- 支持添加和编辑两种模式
- 应用名称：文本输入框，必填
- 应用类型：下拉选择（compose、script、binary）
- 应用版本：文本输入框，必填
- 应用描述：文本域，可选
- 应用内容：代码编辑器，必填，支持语法高亮
- 表单验证：必填项验证

### 实现要点

```vue
<script setup lang="ts">
import { ref, computed } from 'vue'
import type { Application, CreateApplicationRequest, UpdateApplicationRequest } from '@/types'

const props = defineProps<{
  mode: 'create' | 'edit'
  application?: Application | null
}>()

const emit = defineEmits<{
  submit: [data: CreateApplicationRequest | UpdateApplicationRequest]
  cancel: []
}>()

const formData = ref({
  name: props.application?.name || '',
  description: props.application?.description || '',
  type: props.application?.type || 'compose',
  content: props.application?.content || '',
  version: props.application?.version || ''
})

const errors = ref<Record<string, string>>({})

const validate = () => {
  errors.value = {}
  
  if (!formData.value.name.trim()) {
    errors.value.name = '应用名称不能为空'
  }
  
  if (!formData.value.type) {
    errors.value.type = '请选择应用类型'
  }
  
  if (!formData.value.version.trim()) {
    errors.value.version = '应用版本不能为空'
  }
  
  if (!formData.value.content.trim()) {
    errors.value.content = '应用内容不能为空'
  }
  
  return Object.keys(errors.value).length === 0
}

const handleSubmit = () => {
  if (!validate()) return
  
  if (props.mode === 'edit' && props.application) {
    emit('submit', {
      id: props.application.id,
      ...formData.value
    } as UpdateApplicationRequest)
  } else {
    emit('submit', formData.value as CreateApplicationRequest)
  }
}
</script>
```

---

## ApplicationDetail 组件

### 功能特性

- 显示应用完整信息
- 基本信息：名称、类型、版本、描述
- 内容预览：代码编辑器，只读模式
- 操作按钮：编辑、删除、部署

### 实现要点

```vue
<template>
  <div class="application-detail">
    <div class="detail-header">
      <h3>{{ $t('application.basicInfo') }}</h3>
    </div>
    
    <div class="detail-content">
      <div class="info-item">
        <label>{{ $t('application.applicationName') }}</label>
        <span>{{ application.name }}</span>
      </div>
      
      <div class="info-item">
        <label>{{ $t('application.applicationType') }}</label>
        <span class="type-badge" :class="`type-badge--${application.type}`">
          {{ getTypeLabel(application.type) }}
        </span>
      </div>
      
      <div class="info-item">
        <label>{{ $t('application.applicationVersion') }}</label>
        <span>{{ application.version }}</span>
      </div>
      
      <div class="info-item">
        <label>{{ $t('application.applicationDescription') }}</label>
        <span>{{ application.description }}</span>
      </div>
    </div>
    
    <div class="detail-section">
      <h3>{{ $t('application.contentPreview') }}</h3>
      <CodeEditor
        :value="application.content"
        :language="getLanguage(application.type)"
        :readonly="true"
      />
    </div>
    
    <div class="detail-actions">
      <Button type="primary" @click="$emit('edit', application)">
        {{ $t('common.edit') }}
      </Button>
      <Button type="danger" @click="$emit('delete', application)">
        {{ $t('common.delete') }}
      </Button>
      <Button type="secondary" @click="$emit('deploy', application)">
        {{ $t('application.deploy') }}
      </Button>
    </div>
  </div>
</template>

<script setup lang="ts">
import type { Application } from '@/types'

defineProps<{
  application: Application
}>()

defineEmits<{
  edit: [application: Application]
  delete: [application: Application]
  deploy: [application: Application]
}>()

const getTypeLabel = (type: string) => {
  const labels: Record<string, string> = {
    compose: 'Compose',
    script: '脚本',
    binary: '二进制'
  }
  return labels[type] || type
}

const getLanguage = (type: string) => {
  const languages: Record<string, string> = {
    compose: 'yaml',
    script: 'bash',
    binary: 'plaintext'
  }
  return languages[type] || 'plaintext'
}
</script>
```

---

## DeleteConfirm 组件

### 功能特性

- 删除确认弹窗
- 显示警告图标和删除提示
- 确认/取消按钮

### 实现代码

```vue
<template>
  <Modal :visible="visible" @close="$emit('cancel')">
    <div class="delete-confirm">
      <div class="warning-icon">
        <Icon icon="lucide:alert-triangle" />
      </div>
      
      <h3>{{ $t('application.confirmDelete') }}</h3>
      <p>{{ $t('application.deleteWarning', { name: application?.name }) }}</p>
      
      <div class="confirm-actions">
        <Button type="secondary" @click="$emit('cancel')">
          {{ $t('common.cancel') }}
        </Button>
        <Button type="danger" @click="$emit('confirm')">
          {{ $t('common.delete') }}
        </Button>
      </div>
    </div>
  </Modal>
</template>

<script setup lang="ts">
import { Icon } from '@iconify/vue'
import type { Application } from '@/types'

defineProps<{
  visible: boolean
  application: Application | null
}>()

defineEmits<{
  confirm: []
  cancel: []
}>()
</script>
```

---

## 主页面逻辑

### 状态管理

```typescript
import { ref, computed } from 'vue'
import type { Application, CreateApplicationRequest, UpdateApplicationRequest } from '@/types'
import { 
  fetchApplications, 
  createApplication, 
  updateApplication, 
  deleteApplication 
} from '@/api/application'

const applications = ref<Application[]>([])
const showForm = ref(false)
const showDeleteConfirm = ref(false)
const showDetail = ref(false)
const editingApplication = ref<Application | null>(null)
const deletingApplication = ref<Application | null>(null)
const viewingApplication = ref<Application | null>(null)
const searchKeyword = ref('')
const sortBy = ref<string | null>(null)
const sortOrder = ref<'asc' | 'desc'>('asc')
const toastVisible = ref(false)
const toastMessage = ref('')
const toastType = ref<'success' | 'error'>('success')
```

### 过滤和排序

```typescript
const filteredApplications = computed(() => {
  let result = applications.value

  if (searchKeyword.value) {
    const keyword = searchKeyword.value.toLowerCase()
    result = result.filter(app => 
      app.name.toLowerCase().includes(keyword) ||
      app.description.toLowerCase().includes(keyword)
    )
  }

  if (sortBy.value) {
    result = [...result].sort((a, b) => {
      const aValue = a[sortBy.value as keyof Application]
      const bValue = b[sortBy.value as keyof Application]
      const comparison = String(aValue).localeCompare(String(bValue))
      return sortOrder.value === 'asc' ? comparison : -comparison
    })
  }

  return result
})
```

### 核心操作

```typescript
const loadApplications = async () => {
  try {
    applications.value = await fetchApplications()
  } catch (error) {
    showToast('加载应用列表失败', 'error')
  }
}

const handleFormSubmit = async (data: CreateApplicationRequest | UpdateApplicationRequest) => {
  try {
    if (editingApplication.value) {
      await updateApplication(editingApplication.value.id, data as UpdateApplicationRequest)
      showToast('应用更新成功', 'success')
    } else {
      await createApplication(data as CreateApplicationRequest)
      showToast('应用创建成功', 'success')
    }
    await loadApplications()
    showForm.value = false
    editingApplication.value = null
  } catch (error) {
    showToast('操作失败', 'error')
  }
}

const handleSort = (field: string) => {
  if (sortBy.value === field) {
    sortOrder.value = sortOrder.value === 'asc' ? 'desc' : 'asc'
  } else {
    sortBy.value = field
    sortOrder.value = 'asc'
  }
}

const confirmDelete = async () => {
  if (!deletingApplication.value) return
  
  try {
    await deleteApplication(deletingApplication.value.id)
    showToast('应用删除成功', 'success')
    await loadApplications()
    showDeleteConfirm.value = false
    deletingApplication.value = null
  } catch (error) {
    showToast('删除失败', 'error')
  }
}

const showToast = (message: string, type: 'success' | 'error') => {
  toastMessage.value = message
  toastType.value = type
  toastVisible.value = true
  setTimeout(() => {
    toastVisible.value = false
  }, 3000)
}
```
