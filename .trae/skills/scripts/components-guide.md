# 组件实现指南

本文档提供脚本管理模块的组件实现指南和示例代码。

## 目录

- [ScriptTable 组件](#scripttable-组件)
- [ScriptEditor 组件](#scripteditor-组件)
- [ExecuteDialog 组件](#executedialog-组件)
- [ResultList 组件](#resultlist-组件)
- [主页面逻辑](#主页面逻辑)

## ScriptTable 组件

### 功能要点

- 使用表格行布局展示脚本列表
- 显示脚本名称
- 操作按钮：编辑、删除、执行、查看结果
- 脚本名称过长时使用省略号显示

### 实现示例

```vue
<template>
  <div class="script-table-container">
    <table class="script-table">
      <thead>
        <tr>
          <th>{{ $t('scripts.scriptName') }}</th>
          <th>{{ $t('scripts.operation') }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="script in scripts" :key="script.id" class="script-row">
          <td class="name-cell">
            <div class="name-wrapper">
              <Icon icon="lucide:file-code" class="name-icon" />
              <span class="name-text" :title="script.name">{{ script.name }}</span>
            </div>
          </td>
          <td class="action-cell">
            <button class="action-btn edit-btn" :title="$t('scripts.editScript')" @click="$emit('edit', script)">
              <Icon icon="lucide:edit-2" />
            </button>
            <button class="action-btn execute-btn" :title="$t('scripts.executeScript')" @click="$emit('execute', script)">
              <Icon icon="lucide:play" />
            </button>
            <button class="action-btn result-btn" :title="$t('scripts.viewResults')" @click="$emit('viewResults', script)">
              <Icon icon="lucide:list" />
            </button>
            <button class="action-btn delete-btn" :title="$t('scripts.deleteScript')" @click="$emit('delete', script)">
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
import type { Script } from '@/types'

const props = defineProps<{
  scripts: Script[]
}>()

defineEmits<{
  edit: [script: Script]
  delete: [script: Script]
  execute: [script: Script]
  viewResults: [script: Script]
}>()
</script>
```

## ScriptEditor 组件

### 功能要点

- 支持添加和编辑两种模式
- 脚本名称：文本输入框，必填
- 脚本内容：代码编辑器，必填，支持多行文本
- 表单验证：必填项验证、shebang 验证

### 实现要点

```typescript
const validateScript = (content: string): boolean => {
  if (!content || content.trim() === '') {
    return false
  }
  if (!content.startsWith('#!')) {
    return false
  }
  return true
}
```

## ExecuteDialog 组件

### 功能要点

- 执行脚本对话框
- 选择服务器下拉框
- 显示脚本名称和内容预览
- 确认/取消按钮

### 实现要点

```vue
<template>
  <div class="execute-dialog">
    <div class="dialog-content">
      <h3>{{ $t('scripts.executeScript') }}</h3>
      <div class="script-preview">
        <p><strong>{{ $t('scripts.scriptName') }}:</strong> {{ script?.name }}</p>
      </div>
      <div class="server-select">
        <label>{{ $t('scripts.selectServer') }}</label>
        <select v-model="selectedServerId">
          <option v-for="server in servers" :key="server.id" :value="server.id">
            {{ server.hostname }} ({{ server.ip_address }})
          </option>
        </select>
      </div>
      <div class="dialog-actions">
        <button @click="$emit('cancel')">{{ $t('scripts.cancel') }}</button>
        <button @click="handleExecute">{{ $t('scripts.execute') }}</button>
      </div>
    </div>
  </div>
</template>
```

## ResultList 组件

### 功能要点

- 显示脚本执行结果列表
- 显示任务ID、服务器IP、状态、输出、创建时间
- 状态标签：运行中（黄色）、成功（绿色）、失败（红色）

### 实现要点

```vue
<template>
  <div class="result-list">
    <table class="result-table">
      <thead>
        <tr>
          <th>{{ $t('scripts.taskId') }}</th>
          <th>{{ $t('scripts.serverIP') }}</th>
          <th>{{ $t('scripts.status') }}</th>
          <th>{{ $t('scripts.output') }}</th>
          <th>{{ $t('scripts.createdAt') }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="result in results" :key="result.id">
          <td>{{ result.task_id }}</td>
          <td>{{ result.server_ip }}</td>
          <td>
            <span :class="`status-badge status-${result.status}`">
              {{ getStatusText(result.status) }}
            </span>
          </td>
          <td class="output-cell">{{ result.output }}</td>
          <td>{{ result.created_at }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup lang="ts">
import { useI18n } from 'vue-i18n'
import type { ScriptResult } from '@/types'

const { t } = useI18n()

const props = defineProps<{
  results: ScriptResult[]
}>()

const getStatusText = (status: string) => {
  const statusMap = {
    running: t('scripts.statusRunning'),
    success: t('scripts.statusSuccess'),
    failed: t('scripts.statusFailed')
  }
  return statusMap[status] || status
}
</script>

<style scoped>
.status-running {
  background: #fef3c7;
  color: #92400e;
}

.status-success {
  background: #d1fae5;
  color: #065f46;
}

.status-failed {
  background: #fee2e2;
  color: #991b1b;
}
</style>
```

## 主页面逻辑

### 主要功能

```typescript
const scripts = ref<Script[]>([])
const showEditor = ref(false)
const showExecuteDialog = ref(false)
const showResultList = ref(false)
const searchKeyword = ref('')
const toastVisible = ref(false)
const toastMessage = ref('')

const filteredScripts = computed(() => {
  if (!searchKeyword.value) {
    return scripts.value
  }
  const keyword = searchKeyword.value.toLowerCase()
  return scripts.value.filter(script => 
    script.name.toLowerCase().includes(keyword)
  )
})

const loadScripts = async () => {
  scripts.value = await fetchScripts()
}

const handleFormSubmit = async (data: CreateScriptRequest | UpdateScriptRequest) => {
  if (editingScript.value) {
    await updateScript(editingScript.value.id, data as UpdateScriptRequest)
  } else {
    await createScript(data as CreateScriptRequest)
  }
  await loadScripts()
}

const handleExecute = async (script: Script) => {
  selectedScript.value = script
  showExecuteDialog.value = true
}

const confirmExecute = async (serverId: number) => {
  if (!selectedScript.value) return
  await executeScript({
    script_id: selectedScript.value.id,
    server_id: serverId
  })
  toastMessage.value = t('scripts.executeSuccess')
  toastVisible.value = true
  setTimeout(() => {
    toastVisible.value = false
  }, 2000)
}

const handleViewResults = async (script: Script) => {
  selectedScript.value = script
  results.value = await fetchScriptResults(script.id)
  showResultList.value = true
}

const confirmDelete = async () => {
  await deleteScript(deletingScript.value.id)
  await loadScripts()
}
```

## 样式规范

### 表格样式

```scss
.script-table {
  width: 100%;
  border-collapse: collapse;
  
  .script-row {
    border-bottom: 1px solid #e5e7eb;
    
    &:hover {
      background: #f9fafb;
    }
  }
  
  .name-cell {
    padding: 12px 16px;
    
    .name-wrapper {
      display: flex;
      align-items: center;
      gap: 8px;
    }
    
    .name-text {
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
    }
  }
  
  .action-cell {
    padding: 12px 16px;
    display: flex;
    gap: 8px;
  }
}
```

### 操作按钮样式

```scss
.action-btn {
  padding: 6px;
  border: none;
  background: transparent;
  cursor: pointer;
  border-radius: 4px;
  transition: background 0.2s;
  
  &:hover {
    background: #f3f4f6;
  }
  
  &.edit-btn:hover {
    color: #3b82f6;
  }
  
  &.execute-btn:hover {
    color: #10b981;
  }
  
  &.result-btn:hover {
    color: #8b5cf6;
  }
  
  &.delete-btn:hover {
    color: #ef4444;
  }
}
```
