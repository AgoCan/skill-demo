# Deployment 组件实现

## 目录

- [通用组件](#通用组件)
- [DeploymentTable 组件](#deploymenttable-组件)
- [DeploymentForm 组件](#deploymentform-组件)
- [DeploymentDetail 组件](#deploymentdetail-组件)
- [UndeployConfirm 组件](#undeployconfirm-组件)
- [主页面逻辑](#主页面逻辑)

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

## DeploymentTable 组件

使用表格行布局展示部署列表，显示部署 ID、应用信息、服务器信息、状态、部署时间。

```vue
<template>
  <div class="deployment-table-container">
    <table class="deployment-table">
      <thead>
        <tr>
          <th>{{ $t('deployment.deployId') }}</th>
          <th>{{ $t('deployment.applicationName') }}</th>
          <th>{{ $t('deployment.serverAddress') }}</th>
          <th>{{ $t('deployment.status') }}</th>
          <th>{{ $t('deployment.deployedAt') }}</th>
          <th>{{ $t('common.operation') }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="deployment in deployments" :key="deployment.id" class="deployment-row">
          <td class="deploy-id-cell">
            <span class="deploy-id-text">{{ deployment.deploy_id }}</span>
          </td>
          <td class="application-cell">
            <div class="application-wrapper">
              <Icon :icon="getTypeIcon(deployment.application.type)" class="type-icon" />
              <div class="application-info">
                <span class="application-name">{{ deployment.application.name }}</span>
                <span class="application-version">{{ deployment.application.version }}</span>
              </div>
            </div>
          </td>
          <td class="server-cell">
            <div class="server-wrapper">
              <Icon icon="lucide:server" class="server-icon" />
              <div class="server-info">
                <span class="server-ip">{{ deployment.server.ip_address }}</span>
                <span class="agent-port">:{{ deployment.server.agent_port }}</span>
              </div>
            </div>
          </td>
          <td class="status-cell">
            <span class="status-badge" :class="`status-badge--${deployment.status}`">
              {{ getStatusLabel(deployment.status) }}
            </span>
          </td>
          <td class="deployed-at-cell">
            <span class="deployed-at-text">{{ deployment.deployed_at }}</span>
          </td>
          <td class="action-cell">
            <button class="action-btn view-btn" :title="$t('deployment.viewDetail')" @click="$emit('view', deployment)">
              <Icon icon="lucide:eye" />
            </button>
            <button
              v-if="deployment.status === 'stopped'"
              class="action-btn start-btn"
              :title="$t('deployment.start')"
              @click="$emit('start', deployment)"
            >
              <Icon icon="lucide:play" />
            </button>
            <button
              v-if="deployment.status === 'running'"
              class="action-btn stop-btn"
              :title="$t('deployment.stop')"
              @click="$emit('stop', deployment)"
            >
              <Icon icon="lucide:pause" />
            </button>
            <button class="action-btn undeploy-btn" :title="$t('deployment.undeploy')" @click="$emit('undeploy', deployment)">
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
import type { Deployment } from '@/types'

const props = defineProps<{
  deployments: Deployment[]
}>()

defineEmits<{
  view: [deployment: Deployment]
  start: [deployment: Deployment]
  stop: [deployment: Deployment]
  undeploy: [deployment: Deployment]
}>()

const getTypeIcon = (type: string) => {
  const icons: Record<string, string> = {
    compose: 'lucide:box',
    script: 'lucide:terminal',
    binary: 'lucide:file-code'
  }
  return icons[type] || 'lucide:package'
}

const getStatusLabel = (status: string) => {
  const labels: Record<string, string> = {
    running: '运行中',
    stopped: '已停止',
    not_deployed: '未部署',
    error: '错误'
  }
  return labels[status] || status
}
</script>
```

---

## DeploymentForm 组件

支持新建部署，选择应用和服务器。

```vue
<template>
  <Modal v-model:visible="visible" :title="$t('deployment.addDeployment')" width="500px">
    <Form ref="formRef" :model="formData" :rules="rules">
      <FormItem :label="$t('deployment.selectApplication')" prop="application_id" required>
        <Select v-model="formData.application_id" :options="applicationOptions" :placeholder="$t('deployment.selectApplication')" />
      </FormItem>
      <FormItem :label="$t('deployment.selectServer')" prop="server_id" required>
        <Select v-model="formData.server_id" :options="serverOptions" :placeholder="$t('deployment.selectServer')" />
      </FormItem>
    </Form>
    <template #footer>
      <Button @click="handleCancel">{{ $t('common.cancel') }}</Button>
      <Button type="primary" :loading="loading" @click="handleSubmit">{{ $t('common.confirm') }}</Button>
    </template>
  </Modal>
</template>

<script setup lang="ts">
import { ref, watch, computed } from 'vue'
import type { CreateDeploymentRequest } from '@/types'

interface Props {
  visible: boolean
  applications: Array<{ id: number; name: string }>
  servers: Array<{ id: number; ip_address: string }>
}

const props = defineProps<Props>()

const emit = defineEmits<{
  'update:visible': [value: boolean]
  submit: [data: CreateDeploymentRequest]
}>()

const formRef = ref()
const loading = ref(false)
const formData = ref<CreateDeploymentRequest>({
  application_id: 0,
  server_id: 0
})

const rules = {
  application_id: [{ required: true, message: '请选择应用', trigger: 'change' }],
  server_id: [{ required: true, message: '请选择服务器', trigger: 'change' }]
}

const applicationOptions = computed(() =>
  props.applications.map(app => ({ label: app.name, value: app.id }))
)

const serverOptions = computed(() =>
  props.servers.map(server => ({ label: server.ip_address, value: server.id }))
)

const handleSubmit = async () => {
  await formRef.value?.validate()
  emit('submit', formData.value)
}

const handleCancel = () => {
  emit('update:visible', false)
}

watch(() => props.visible, (val) => {
  if (!val) {
    formData.value = { application_id: 0, server_id: 0 }
  }
})
</script>
```

---

## DeploymentDetail 组件

显示部署完整信息，包括基本信息和操作按钮。

**要点：**
- 显示部署 ID、应用信息、服务器信息、状态、部署时间
- 操作按钮：启动、停止、卸载

---

## UndeployConfirm 组件

卸载确认弹窗。

**要点：**
- 显示警告图标和卸载提示
- 确认/取消按钮

---

## 主页面逻辑

```typescript
// 主要功能
const deployments = ref<Deployment[]>([])
const showForm = ref(false)
const showUndeployConfirm = ref(false)
const showDetail = ref(false)
const filterServerId = ref<number | undefined>(undefined)
const toastVisible = ref(false)
const toastMessage = ref('')

// 过滤
const filteredDeployments = computed(() => {
  if (filterServerId.value) {
    return deployments.value.filter(d => d.server.id === filterServerId.value)
  }
  return deployments.value
})

// 加载部署列表
const loadDeployments = async () => {
  deployments.value = await fetchDeployments(filterServerId.value)
}

// 表单提交
const handleFormSubmit = async (data: CreateDeploymentRequest) => {
  await createDeployment(data.application_id, data)
  await loadDeployments()
  showToast($t('deployment.createSuccess'), 'success')
}

// 启动应用
const handleStart = async (deployment: Deployment) => {
  await startDeployment(deployment.id)
  await loadDeployments()
  showToast($t('deployment.startSuccess'), 'success')
}

// 停止应用
const handleStop = async (deployment: Deployment) => {
  await stopDeployment(deployment.id)
  await loadDeployments()
  showToast($t('deployment.stopSuccess'), 'success')
}

// 卸载确认
const confirmUndeploy = async () => {
  await undeployDeployment(undeployingDeployment.value.id)
  await loadDeployments()
  showToast($t('deployment.undeploySuccess'), 'success')
}

// 服务器筛选
const handleServerFilter = (serverId: number | undefined) => {
  filterServerId.value = serverId
  loadDeployments()
}
```
