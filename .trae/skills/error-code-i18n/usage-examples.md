# 使用示例

## 目录

- [组件中使用](#组件中使用)
- [useErrorHandler 组合函数](#useerrorhandler-组合函数)

## 组件中使用

```vue
<template>
  <el-button @click="deleteServer">删除服务器</el-button>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { getErrorMessage } from '@/utils/errorHandler'
import { deleteServer as apiDeleteServer } from '@/api/server'

const loading = ref(false)

const deleteServer = async () => {
  try {
    loading.value = true
    await apiDeleteServer(serverId.value)
    ElMessage.success('删除成功')
  } catch (error) {
    console.error(getErrorMessage(error))
  } finally {
    loading.value = false
  }
}
</script>
```

## useErrorHandler 组合函数

**src/composables/useErrorHandler.ts**

```typescript
import { ref } from 'vue'
import { useI18n } from 'vue-i18n'
import { ElMessage } from 'element-plus'
import type { ApiError } from '@/utils/errorHandler'
import { getErrorMessage, isNetworkError, isAuthError } from '@/utils/errorHandler'

export function useErrorHandler() {
  const { t } = useI18n()
  const error = ref<string | null>(null)
  const errorCode = ref<number | null>(null)

  const handleError = (err: ApiError | unknown) => {
    if (isNetworkError(err)) {
      error.value = t('error.common.networkError')
      ElMessage.error(error.value)
      return
    }

    if (err && typeof err === 'object' && 'code' in err) {
      const apiError = err as ApiError
      errorCode.value = apiError.code
      error.value = getErrorMessage(apiError)

      ElMessage.error(error.value)

      // 认证错误跳转
      if (isAuthError(apiError)) {
        window.location.href = '/login'
      }
    } else {
      error.value = t('error.common.unknownError')
      ElMessage.error(error.value)
    }
  }

  const clearError = () => {
    error.value = null
    errorCode.value = null
  }

  return {
    error,
    errorCode,
    handleError,
    clearError,
  }
}
```

在组件中使用：

```vue
<script setup lang="ts">
import { useErrorHandler } from '@/composables/useErrorHandler'

const { error, handleError, clearError } = useErrorHandler()

const onSubmit = async () => {
  try {
    await submitForm()
  } catch (err) {
    handleError(err)
  }
}
</script>
```
