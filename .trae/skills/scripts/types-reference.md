# 类型定义参考

本文档提供脚本管理模块的所有 TypeScript 类型定义。

## 目录

- [基础类型](#基础类型)
- [请求类型](#请求类型)
- [响应类型](#响应类型)
- [页面状态类型](#页面状态类型)
- [API 函数类型](#api-函数类型)

## 基础类型

### Script

```typescript
export interface Script {
  id: number
  name: string
  content: string
}
```

### ScriptResult

```typescript
export interface ScriptResult {
  id: number
  task_id: number
  script_id: number
  server_id: number
  server_ip: string
  agent_port: number
  output: string
  status: string
  error_message: string
  created_at: string
}
```

### ApiResponse

```typescript
export interface ApiResponse<T = any> {
  code: number
  message: string
  data: T
}
```

## 请求类型

### CreateScriptRequest

创建脚本请求类型

```typescript
export interface CreateScriptRequest {
  name: string
  content: string
}
```

### UpdateScriptRequest

更新脚本请求类型

```typescript
export interface UpdateScriptRequest {
  id: number
  name: string
  content: string
}
```

### ExecuteScriptRequest

执行脚本请求类型

```typescript
export interface ExecuteScriptRequest {
  script_id: number
  server_id: number
}
```

## 页面状态类型

### FormState

表单状态类型

```typescript
export interface FormState {
  visible: boolean
  mode: 'create' | 'edit'
  data: CreateScriptRequest | UpdateScriptRequest | null
}
```

### ExecuteState

执行对话框状态类型

```typescript
export interface ExecuteState {
  visible: boolean
  script: Script | null
}
```

### ResultState

结果列表状态类型

```typescript
export interface ResultState {
  visible: boolean
  script: Script | null
}
```

## API 函数类型

### API 函数签名

```typescript
export function fetchScripts(): Promise<Script[]>

export function fetchScriptDetail(scriptId: number): Promise<Script>

export function createScript(data: CreateScriptRequest): Promise<string>

export function updateScript(scriptId: number, data: UpdateScriptRequest): Promise<string>

export function deleteScript(scriptId: number): Promise<string>

export function executeScript(data: ExecuteScriptRequest): Promise<string>

export function fetchScriptResults(scriptId: number): Promise<ScriptResult[]>
```

## 使用示例

### 获取脚本列表

```typescript
const scripts = ref<Script[]>([])

const loadScripts = async () => {
  try {
    scripts.value = await fetchScripts()
  } catch (error) {
    console.error('Failed to load scripts:', error)
  }
}
```

### 创建脚本

```typescript
const handleCreate = async (data: CreateScriptRequest) => {
  try {
    await createScript(data)
    await loadScripts()
  } catch (error) {
    console.error('Failed to create script:', error)
  }
}
```

### 执行脚本

```typescript
const handleExecute = async (scriptId: number, serverId: number) => {
  try {
    await executeScript({
      script_id: scriptId,
      server_id: serverId
    })
  } catch (error) {
    console.error('Failed to execute script:', error)
  }
}
```

### 获取执行结果

```typescript
const results = ref<ScriptResult[]>([])

const loadResults = async (scriptId: number) => {
  try {
    results.value = await fetchScriptResults(scriptId)
  } catch (error) {
    console.error('Failed to load results:', error)
  }
}
```

## 状态枚举

### ScriptStatus

脚本执行状态

```typescript
export enum ScriptStatus {
  RUNNING = 'running',
  SUCCESS = 'success',
  FAILED = 'failed'
}
```

### 状态颜色映射

```typescript
export const statusColorMap = {
  [ScriptStatus.RUNNING]: '#f59e0b',  // 黄色
  [ScriptStatus.SUCCESS]: '#10b981',  // 绿色
  [ScriptStatus.FAILED]: '#ef4444'    // 红色
}
```
