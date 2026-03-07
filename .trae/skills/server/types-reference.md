# 类型定义参考

本文档提供服务器管理模块的所有 TypeScript 类型定义。

## 目录

- [基础类型](#基础类型)
- [服务器类型](#服务器类型)
- [请求类型](#请求类型)
- [API 函数类型](#api-函数类型)

## 基础类型

### IP地址类型

```typescript
export interface IpAddress {
  ipv4: string[]
  ipv6: string[]
  name: string
}
```

### 服务器详细信息

```typescript
export interface ServerInfo {
  architecture: string
  hostname: string
  ipAddresses: IpAddress[]
  kernelVersion: string
  os: string
  platform: string
  platformVersion: string
  uptime: number
  uptimeStr: string
}
```

## 服务器类型

### Server

```typescript
export interface Server {
  id: number
  hostname: string
  ip_address: string
  ssh_username: string
  ssh_port: number
  auth_type: 'password' | 'key'
  status: 'online' | 'offline' | 'unknown' | 'active'
  server_info?: ServerInfo | null
  server_alias?: string
}
```

### 字段说明

- `id`: 服务器唯一标识
- `hostname`: 主机名
- `ip_address`: IP 地址
- `ssh_username`: SSH 用户名
- `ssh_port`: SSH 端口
- `auth_type`: 认证方式（password 或 key）
- `status`: 服务器状态（online、offline、unknown、active）
- `server_info`: 服务器详细信息（可选）
- `server_alias`: 服务器别名（可选）

## 请求类型

### CreateServerRequest

创建服务器请求类型

```typescript
export interface CreateServerRequest {
  ip_address: string
  ssh_username: string
  ssh_port: number
  ssh_password?: string
  ssh_private_key?: string
  auth_type: 'password' | 'key'
  status: 'active' | 'inactive'
  server_alias?: string
}
```

### UpdateServerRequest

更新服务器请求类型

```typescript
export interface UpdateServerRequest {
  ip_address: string
  ssh_username: string
  ssh_port: number
  auth_type: 'password' | 'key'
  status: 'active' | 'inactive'
  server_alias?: string
}
```

### 字段说明

- `ip_address`: IP 地址（必填）
- `ssh_username`: SSH 用户名（必填）
- `ssh_port`: SSH 端口（必填）
- `ssh_password`: SSH 密码（创建时必填，更新时可选）
- `ssh_private_key`: SSH 私钥（创建时必填，更新时可选）
- `auth_type`: 认证方式（password 或 key）
- `status`: 状态（active 或 inactive）
- `server_alias`: 服务器别名（可选）

## API 函数类型

### API 函数签名

```typescript
export function fetchServers(): Promise<Server[]>

export function fetchServerDetail(serverId: number): Promise<Server>

export function createServer(data: CreateServerRequest): Promise<string>

export function updateServer(serverId: number, data: UpdateServerRequest): Promise<string>

export function deleteServer(serverId: number): Promise<string>
```

## 使用示例

### 获取服务器列表

```typescript
const servers = ref<Server[]>([])

const loadServers = async () => {
  try {
    servers.value = await fetchServers()
  } catch (error) {
    console.error('Failed to load servers:', error)
  }
}
```

### 创建服务器

```typescript
const handleCreate = async (data: CreateServerRequest) => {
  try {
    await createServer(data)
    await loadServers()
  } catch (error) {
    console.error('Failed to create server:', error)
  }
}
```

### 更新服务器

```typescript
const handleUpdate = async (serverId: number, data: UpdateServerRequest) => {
  try {
    await updateServer(serverId, data)
    await loadServers()
  } catch (error) {
    console.error('Failed to update server:', error)
  }
}
```

### 删除服务器

```typescript
const handleDelete = async (serverId: number) => {
  try {
    await deleteServer(serverId)
    await loadServers()
  } catch (error) {
    console.error('Failed to delete server:', error)
  }
}
```
