# Deployment API 文档

## 基础信息

- **Base URL**: `{{url}}/api/v1`
- **Content-Type**: `application/json`
- **认证方式**: JWT Token

---

## API 列表

### 1. 获取部署列表

获取所有部署实例列表，支持按服务器 ID 筛选。

**接口信息**
- **Method**: `GET`
- **Endpoint**: `/api/v1/deployment`

**查询参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| server_id | number | 否 | 按服务器 ID 筛选部署列表 |

**请求示例**
```http
GET {{url}}/api/v1/deployment?server_id=1
Content-Type: application/json
Authorization: Bearer {{token}}
```

**成功响应示例**
```json
{
  "code": 0,
  "message": "success",
  "data": [
    {
      "id": 1,
      "deploy_id": 605539611954914580,
      "application": {
        "id": 1,
        "name": "nginx",
        "description": "",
        "type": "compose",
        "version": "1.25"
      },
      "server": {
        "id": 1,
        "ip_address": "127.0.0.1",
        "agent_port": 10750
      },
      "status": "not_deployed",
      "deployed_at": "2026-02-07 10:15:34"
    },
    {
      "id": 2,
      "deploy_id": 605539803315840276,
      "application": {
        "id": 1,
        "name": "nginx",
        "description": "",
        "type": "compose",
        "version": "1.25"
      },
      "server": {
        "id": 1,
        "ip_address": "127.0.0.1",
        "agent_port": 10750
      },
      "status": "not_deployed",
      "deployed_at": "2026-02-07 10:17:28"
    }
  ]
}
```

**响应字段说明**

| 字段 | 类型 | 说明 |
|------|------|------|
| code | number | 响应码，0 表示成功 |
| message | string | 响应消息 |
| data | array | 部署列表数据 |
| data[].id | number | 部署记录 ID |
| data[].deploy_id | number | 部署 ID（Agent 端标识） |
| data[].application | object | 应用信息 |
| data[].application.id | number | 应用 ID |
| data[].application.name | string | 应用名称 |
| data[].application.description | string | 应用描述 |
| data[].application.type | string | 应用类型 |
| data[].application.version | string | 应用版本 |
| data[].server | object | 服务器信息 |
| data[].server.id | number | 服务器 ID |
| data[].server.ip_address | string | 服务器 IP 地址 |
| data[].server.agent_port | number | Agent 端口 |
| data[].status | string | 部署状态 |
| data[].deployed_at | string | 部署时间 |

---

### 2. 部署应用

将指定应用部署到目标服务器。

**接口信息**
- **Method**: `POST`
- **Endpoint**: `/api/v1/deployment/deploy/{application_id}`

**路径参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| application_id | number | 是 | 应用 ID |

**请求体参数**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| server_id | number | 是 | 目标服务器 ID |

**请求示例**
```http
POST {{url}}/api/v1/deployment/deploy/1
Content-Type: application/json
Authorization: Bearer {{token}}

{
  "server_id": 1
}
```

**成功响应示例**
```json
{
  "code": 0,
  "message": "success",
  "data": "deploy success"
}
```

**错误响应示例**

应用不存在：
```json
{
  "code": 1001,
  "message": "应用不存在"
}
```

部署失败：
```json
{
  "code": 2001,
  "message": "部署失败"
}
```

---

### 3. 获取应用部署的服务器列表

查询指定应用在哪些服务器上有部署。

**接口信息**
- **Method**: `GET`
- **Endpoint**: `/api/v1/deployment/{deployment_id}/servers`

**路径参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| deployment_id | number | 是 | 部署 ID |

**请求示例**
```http
GET {{url}}/api/v1/deployment/1/servers
Content-Type: application/json
Authorization: Bearer {{token}}
```

**成功响应示例**
```json
{
  "code": 0,
  "message": "success",
  "data": [
    {
      "id": 1,
      "ip_address": "127.0.0.1",
      "agent_port": 10750
    },
    {
      "id": 2,
      "ip_address": "192.168.1.100",
      "agent_port": 10750
    }
  ]
}
```

---

### 4. 卸载部署

卸载指定的部署实例，调用 Agent 清理应用资源。

**接口信息**
- **Method**: `DELETE`
- **Endpoint**: `/api/v1/deployment/deploy/{deployment_id}`

**路径参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| deployment_id | number | 是 | 部署记录 ID |

**请求示例**
```http
DELETE {{url}}/api/v1/deployment/deploy/1
Content-Type: application/json
Authorization: Bearer {{token}}
```

**成功响应示例**
```json
{
  "code": 0,
  "message": "success",
  "data": "undeploy success"
}
```

**错误响应示例**

部署不存在：
```json
{
  "code": 2002,
  "message": "部署不存在"
}
```

---

### 5. 停止应用

停止正在运行的部署实例。

**接口信息**
- **Method**: `POST`
- **Endpoint**: `/api/v1/deployment/stop/{deployment_id}`

**路径参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| deployment_id | number | 是 | 部署记录 ID |

**请求示例**
```http
POST {{url}}/api/v1/deployment/stop/1
Content-Type: application/json
Authorization: Bearer {{token}}
```

**成功响应示例**
```json
{
  "code": 0,
  "message": "success",
  "data": "stop success"
}
```

**错误响应示例**

部署不存在：
```json
{
  "code": 2002,
  "message": "部署不存在"
}
```

停止失败：
```json
{
  "code": 2001,
  "message": "部署失败"
}
```

---

### 6. 启动应用

启动已停止的部署实例。

**接口信息**
- **Method**: `POST`
- **Endpoint**: `/api/v1/deployment/start/{deployment_id}`

**路径参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| deployment_id | number | 是 | 部署记录 ID |

**请求示例**
```http
POST {{url}}/api/v1/deployment/start/1
Content-Type: application/json
Authorization: Bearer {{token}}
```

**成功响应示例**
```json
{
  "code": 0,
  "message": "success",
  "data": "start success"
}
```

**错误响应示例**

部署不存在：
```json
{
  "code": 2002,
  "message": "部署不存在"
}
```

启动失败：
```json
{
  "code": 2001,
  "message": "部署失败"
}
```

---

### 7. 上报应用状态（内部接口）

Agent 定期上报应用的运行状态。

**接口信息**
- **Method**: `POST`
- **Endpoint**: `/api/v1/deployment/report`

**请求体参数**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| application_id | number | 是 | 应用 ID |
| server_id | number | 是 | 服务器 ID |
| status | string | 是 | 应用状态 |
| deploy_id | number | 是 | 部署 ID |

**请求示例**
```http
POST {{url}}/api/v1/deployment/report
Content-Type: application/json

{
  "application_id": 1,
  "server_id": 1,
  "deploy_id": 605539611954914580,
  "status": "running"
}
```

**成功响应示例**
```json
{
  "code": 0,
  "message": "success",
  "data": "success"
}
```

---

## 错误码说明

| 错误码 | 说明 |
|--------|------|
| 0 | 成功 |
| 400 | 参数错误 |
| 404 | 资源不存在 |
| 1001 | 应用不存在 |
| 2001 | 部署失败 |
| 2002 | 部署不存在 |
| 500 | 服务器内部错误 |

---

## 数据类型定义

### Deployment
部署实例数据结构。

```typescript
interface Deployment {
  id: number;                    // 部署记录 ID
  deploy_id: number;             // 部署 ID
  application: ApplicationInfo; // 应用信息
  server: ServerInfo;            // 服务器信息
  status: string;                // 部署状态
  deployed_at: string;           // 部署时间
}
```

### ApplicationInfo
应用信息结构。

```typescript
interface ApplicationInfo {
  id: number;          // 应用 ID
  name: string;       // 应用名称
  description: string; // 应用描述
  type: string;       // 应用类型
  version: string;     // 应用版本
}
```

### ServerInfo
服务器信息结构。

```typescript
interface ServerInfo {
  id: number;        // 服务器 ID
  ip_address: string; // IP 地址
  agent_port: number; // Agent 端口
}
```

### DeployRequest
部署请求结构。

```typescript
interface DeployRequest {
  server_id: number; // 目标服务器 ID
}
```

### ReportStatusRequest
状态上报请求结构。

```typescript
interface ReportStatusRequest {
  application_id: number; // 应用 ID
  server_id: number;      // 服务器 ID
  status: string;         // 应用状态
  deploy_id: number;      // 部署 ID
}
```

---

## 使用示例

### 示例 1：获取所有部署列表

```bash
curl -X GET "http://localhost:10700/api/v1/deployment" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### 示例 2：获取指定服务器上的部署列表

```bash
curl -X GET "http://localhost:10700/api/v1/deployment?server_id=1" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### 示例 3：部署应用到服务器

```bash
curl -X POST http://localhost:10700/api/v1/deployment/deploy/1 \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "server_id": 1
  }'
```

### 示例 4：停止运行中的应用

```bash
curl -X POST http://localhost:10700/api/v1/deployment/stop/1 \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### 示例 5：启动已停止的应用

```bash
curl -X POST http://localhost:10700/api/v1/deployment/start/1 \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### 示例 6：卸载部署

```bash
curl -X DELETE http://localhost:10700/api/v1/deployment/deploy/1 \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### 示例 7：获取应用部署的服务器列表

```bash
curl -X GET http://localhost:10700/api/v1/deployment/1/servers \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

---

## 部署状态说明

### 状态枚举值

| 状态值 | 说明 | 可执行操作 |
|--------|------|-----------|
| `running` | 应用正在正常运行 | 停止、卸载 |
| `stopped` | 应用已停止 | 启动、卸载 |
| `not_deployed` | 应用已创建但未部署 | 等待部署完成 |
| `error` | 应用运行出错 | 停止、卸载、重新部署 |

### 状态流转图

```
          [创建部署]
              ↓
    [not_deployed]
              ↓
         [部署成功]
              ↓
        [running] ◀────── [stopped]
              │              │
              │     [停止]    │ [启动]
              │              │
              ↓              │
           [error] ──────────┘
              │
         [卸载/清理]
              ↓
           [删除记录]
```

---

## 部署流程说明

### 部署流程

1. **验证应用**
   - 检查应用是否存在
   - 返回错误码 1001 如果应用不存在

2. **验证服务器**
   - 检查服务器是否存在
   - 验证服务器 Agent 是否可访问

3. **生成部署 ID**
   - 使用雪花算法生成唯一的 64 位整数 ID
   - 确保全局唯一性

4. **调用 Agent 部署**
   - 发送部署请求到目标服务器的 Agent
   - 包含应用完整信息和部署 ID

5. **创建部署记录**
   - 部署成功后创建数据库记录
   - 初始状态为 `not_deployed`
   - 等待 Agent 状态上报更新状态

6. **失败回滚**
   - 如果 Agent 返回失败
   - 调用 Agent 删除已部署的应用
   - 返回部署失败错误

### 卸载流程

1. **查询部署记录**
   - 获取部署详细信息
   - 返回错误码 2002 如果不存在

2. **验证服务器**
   - 检查服务器是否存在
   - 验证 Agent 可访问性

3. **调用 Agent 卸载**
   - 发送卸载请求到 Agent
   - Agent 清理容器和资源

4. **删除部署记录**
   - 从数据库删除部署记录
   - 返回成功响应

### 状态控制流程（启动/停止）

1. **查询部署记录**
   - 获取部署详细信息
   - 验证部署存在

2. **验证服务器**
   - 检查服务器状态
   - 验证 Agent 可访问性

3. **调用 Agent 控制状态**
   - 发送启动/停止请求到 Agent
   - Agent 执行相应的操作

4. **更新状态**
   - Agent 上报最新状态
   - 系统更新部署状态

---

## 注意事项

1. **部署前置条件**
   - 确保目标服务器的 Agent 正常运行
   - 确保应用配置正确有效
   - 确保服务器资源充足

2. **部署限制**
   - 同一应用在同一服务器上部署时，需确保端口不冲突
   - 容器名称需要唯一
   - 存储卷路径不能重复

3. **状态管理**
   - 部署后初始状态为 `not_deployed`
   - Agent 会定期上报实际状态
   - 可能存在短暂的状态延迟

4. **卸载注意事项**
   - 卸载前建议先停止应用
   - 确认数据是否需要备份
   - 卸载不可逆，操作前请确认

5. **并发控制**
   - 同一部署实例不支持并发操作
   - 建议等待上一个操作完成再执行下一个操作

6. **错误处理**
   - 所有操作都有明确的错误码
   - 建议根据错误码进行相应处理
   - 查看日志定位具体问题

7. **权限要求**
   - 所有 API 接口都需要有效的 JWT Token
   - 确保用户有相应的操作权限
   - Agent 需要足够的系统权限执行操作

---

## 相关文档

- [Deployment 模块文档](./deployment.md)
- [Application API 文档](./application-api.md)
- [Server API 文档](./server-api.md)
- [App Store API 文档](./appstore-api.md)
