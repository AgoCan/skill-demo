# Configs 组件实现

## 目录

- [ConfigTable 组件](#configtable-组件)
- [ConfigForm 组件](#configform-组件)
- [DeleteConfirm 组件](#deleteconfirm-组件)
- [主页面逻辑](#主页面逻辑)

---

## ConfigTable 组件

使用表格行布局展示配置列表，支持排序功能。

```vue
<template>
  <div class="config-table-container">
    <table class="config-table">
      <thead>
        <tr>
          <th class="sortable" @click="$emit('sort', 'key')">
            {{ $t('configs.configKey') }}
            <Icon :icon="getSortIcon('key')" class="sort-icon" />
          </th>
          <th class="sortable" @click="$emit('sort', 'value')">
            {{ $t('configs.configValue') }}
            <Icon :icon="getSortIcon('value')" class="sort-icon" />
          </th>
          <th>{{ $t('configs.operation') }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="config in configs" :key="config.id" class="config-row">
          <td class="key-cell">
            <div class="key-wrapper">
              <Icon icon="lucide:key" class="key-icon" />
              <span class="key-text">{{ config.key }}</span>
            </div>
          </td>
          <td class="value-cell">
            <div class="value-wrapper">
              <span class="value-text" :title="config.value">{{ config.value }}</span>
            </div>
          </td>
          <td class="action-cell">
            <button class="action-btn copy-btn" :title="$t('configs.copy')" @click="$emit('copy', config.value)">
              <Icon icon="lucide:copy" />
            </button>
            <button class="action-btn edit-btn" :title="$t('configs.editConfig')" @click="$emit('edit', config)">
              <Icon icon="lucide:edit-2" />
            </button>
            <button class="action-btn delete-btn" :title="$t('configs.deleteConfig')" @click="$emit('delete', config)">
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
import type { Config } from '@/types'

const props = defineProps<{
  configs: Config[]
  sortBy?: string | null
  sortOrder?: 'asc' | 'desc'
}>()

defineEmits<{
  edit: [config: Config]
  delete: [config: Config]
  copy: [value: string]
  sort: [field: string]
}>()

const getSortIcon = (field: string) => {
  if (field !== props.sortBy) return 'lucide:chevrons-up-down'
  return props.sortOrder === 'asc' ? 'lucide:chevron-up' : 'lucide:chevron-down'
}
</script>
```

---

## ConfigForm 组件

支持添加和编辑两种模式。

**要点：**
- 配置键：文本输入框，必填
- 配置值：文本输入框，必填，支持多行文本
- 表单验证：必填项验证

---

## DeleteConfirm 组件

删除确认弹窗。

**要点：**
- 显示警告图标和删除提示
- 确认/取消按钮

---

## 主页面逻辑

```typescript
// 主要功能
const configs = ref<Config[]>([])
const showForm = ref(false)
const showDeleteConfirm = ref(false)
const searchKeyword = ref('')
const sortBy = ref<string | null>(null)
const sortOrder = ref<'asc' | 'desc'>('asc')
const toastVisible = ref(false)
const toastMessage = ref('')

// 过滤和排序
const filteredConfigs = computed(() => {
  let result = configs.value

  if (searchKeyword.value) {
    const keyword = searchKeyword.value.toLowerCase()
    result = result.filter(config => 
      config.key.toLowerCase().includes(keyword) ||
      config.value.toLowerCase().includes(keyword)
    )
  }

  if (sortBy.value) {
    result = [...result].sort((a, b) => {
      const aValue = a[sortBy.value as keyof Config]
      const bValue = b[sortBy.value as keyof Config]
      const comparison = String(aValue).localeCompare(String(bValue))
      return sortOrder.value === 'asc' ? comparison : -comparison
    })
  }

  return result
})

// 加载配置列表
const loadConfigs = async () => {
  configs.value = await fetchConfigs()
}

// 表单提交
const handleFormSubmit = async (data: CreateConfigRequest | UpdateConfigRequest) => {
  if (editingConfig.value) {
    await updateConfig(editingConfig.value.id, data as UpdateConfigRequest)
  } else {
    await createConfig(data as CreateConfigRequest)
  }
  await loadConfigs()
}

// 复制配置值
const handleCopy = async (value: string) => {
  try {
    await navigator.clipboard.writeText(value)
    toastMessage.value = t('configs.copySuccess')
    toastVisible.value = true
    setTimeout(() => {
      toastVisible.value = false
    }, 2000)
  } catch (err) {
    console.error('Failed to copy:', err)
  }
}

// 排序处理
const handleSort = (field: string) => {
  if (sortBy.value === field) {
    sortOrder.value = sortOrder.value === 'asc' ? 'desc' : 'asc'
  } else {
    sortBy.value = field
    sortOrder.value = 'asc'
  }
}

// 删除确认
const confirmDelete = async () => {
  await deleteConfig(deletingConfig.value.id)
  await loadConfigs()
}
```
