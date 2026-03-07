# 组件实现参考

## 目录

- [通用组件](#通用组件)
  - [Button 组件](#button-组件)
  - [Toast 组件](#toast-组件)
  - [Loading 组件](#loading-组件)
  - [Empty 组件](#empty-组件)
- [业务组件](#业务组件)
  - [AppTable 组件](#apptable-组件)
  - [AppForm 组件](#appform-组件)
  - [AppDetail 组件](#appdetail-组件)
  - [DeleteConfirm 组件](#deleteconfirm-组件)
  - [CategoryFilter 组件](#categoryfilter-组件)
- [主页面逻辑](#主页面逻辑)

## 通用组件

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

## 业务组件

### AppTable 组件

使用表格行布局展示应用列表，显示应用图标、名称、描述、类型、分类、版本、下载次数、状态。

```vue
<template>
  <div class="app-table-container">
    <table class="app-table">
      <thead>
        <tr>
          <th>{{ $t('appStore.appName') }}</th>
          <th>{{ $t('appStore.type') }}</th>
          <th>{{ $t('appStore.category') }}</th>
          <th>{{ $t('appStore.version') }}</th>
          <th>{{ $t('appStore.downloads') }}</th>
          <th>{{ $t('appStore.status') }}</th>
          <th>{{ $t('appStore.operation') }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="app in apps" :key="app.id" class="app-row">
          <td class="name-cell">
            <div class="name-wrapper">
              <img v-if="app.icon" :src="app.icon" class="app-icon" />
              <Icon v-else icon="lucide:package" class="app-icon-default" />
              <div class="name-info">
                <div class="name-text">
                  {{ app.name }}
                  <span v-if="app.is_official" class="official-badge">
                    {{ $t('appStore.isOfficial') }}
                  </span>
                </div>
                <div class="description-text">{{ app.description }}</div>
              </div>
            </div>
          </td>
          <td class="type-cell">
            <span class="type-tag" :class="`type-${app.type}`">
              {{ getTypeLabel(app.type) }}
            </span>
          </td>
          <td class="category-cell">
            <span class="category-tag">
              {{ getCategoryLabel(app.category) }}
            </span>
          </td>
          <td class="version-cell">
            <span class="version-text">{{ app.version }}</span>
          </td>
          <td class="downloads-cell">
            <Icon icon="lucide:download" class="downloads-icon" />
            <span>{{ app.downloads }}</span>
          </td>
          <td class="status-cell">
            <span class="status-badge" :class="`status-${app.status}`">
              {{ getStatusLabel(app.status) }}
            </span>
          </td>
          <td class="action-cell">
            <button class="action-btn detail-btn" :title="$t('appStore.viewDetail')" @click="$emit('detail', app)">
              <Icon icon="lucide:eye" />
            </button>
            <button class="action-btn edit-btn" :title="$t('appStore.editApp')" @click="$emit('edit', app)">
              <Icon icon="lucide:edit-2" />
            </button>
            <button class="action-btn delete-btn" :title="$t('appStore.deleteApp')" @click="$emit('delete', app)">
              <Icon icon="lucide:trash-2" />
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup lang="ts">
import { Icon } from '@iconify/vue'
import { useI18n } from 'vue-i18n'
import type { AppStore } from '@/types'

const props = defineProps<{
  apps: AppStore[]
}>()

defineEmits<{
  detail: [app: AppStore]
  edit: [app: AppStore]
  delete: [app: AppStore]
}>()

const { t } = useI18n()

const getTypeLabel = (type: string) => {
  const labels: Record<string, string> = {
    compose: t('appStore.typeCompose'),
    k8s_manifest: t('appStore.typeK8s'),
    helm_chart: t('appStore.typeHelm')
  }
  return labels[type] || type
}

const getCategoryLabel = (category: string) => {
  const labels: Record<string, string> = {
    web: t('appStore.categoryWeb'),
    database: t('appStore.categoryDatabase'),
    middleware: t('appStore.categoryMiddleware'),
    devops: t('appStore.categoryDevops')
  }
  return labels[category] || category
}

const getStatusLabel = (status: string) => {
  const labels: Record<string, string> = {
    active: t('appStore.statusActive'),
    deprecated: t('appStore.statusDeprecated')
  }
  return labels[status] || status
}
</script>
```

### AppForm 组件

支持添加和编辑两种模式，表单字段：

- 应用名称：文本输入框，必填
- 描述：文本域，必填
- 类型：下拉选择，必填（compose/k8s_manifest/helm_chart）
- 分类：下拉选择，必填（web/database/middleware/devops）
- 图标：URL 输入框，可选
- 版本：文本输入框，必填
- 标签：文本输入框，逗号分隔
- 作者：文本输入框，必填
- 仓库地址：URL 输入框，可选
- 主页地址：URL 输入框，可选
- 是否官方：复选框
- 模板内容：代码编辑器，必填

### AppDetail 组件

显示应用完整信息：

- 基本信息分组：名称、描述、类型、分类、版本、标签、作者
- 链接信息：仓库地址、主页地址
- 统计信息：下载次数、官方标识
- 模板内容：代码展示区域，支持复制

### DeleteConfirm 组件

删除确认弹窗，显示警告图标和删除提示，确认/取消按钮。

### CategoryFilter 组件

分类筛选下拉菜单，支持全部分类选项和类型筛选。

## 主页面逻辑

```typescript
const apps = ref<AppStore[]>([])
const showForm = ref(false)
const showDetail = ref(false)
const showDeleteConfirm = ref(false)
const searchKeyword = ref('')
const selectedCategory = ref<string>('')
const selectedType = ref<string>('')

const filteredApps = computed(() => {
  let result = apps.value

  if (searchKeyword.value) {
    const keyword = searchKeyword.value.toLowerCase()
    result = result.filter(app => 
      app.name.toLowerCase().includes(keyword) ||
      app.description.toLowerCase().includes(keyword) ||
      app.tags.toLowerCase().includes(keyword)
    )
  }

  if (selectedCategory.value) {
    result = result.filter(app => app.category === selectedCategory.value)
  }

  if (selectedType.value) {
    result = result.filter(app => app.type === selectedType.value)
  }

  return result
})

const loadApps = async () => {
  apps.value = await fetchApps()
}

const handleFormSubmit = async (data: CreateAppRequest | UpdateAppRequest) => {
  if (editingApp.value) {
    await updateApp(editingApp.value.id, data as UpdateAppRequest)
  } else {
    await createApp(data as CreateAppRequest)
  }
  await loadApps()
}

const confirmDelete = async () => {
  await deleteApp(deletingApp.value.id)
  await loadApps()
}
```
