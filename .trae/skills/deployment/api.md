# Deployment API 接口文档

## 概述

Deployment API 提供应用部署管理功能，包括部署列表查询、新建部署、启动/停止应用、卸载部署等操作。

## 基础信息

- **Base URL**: `/api/v1`
- **Content-Type**: `application/json`
- **认证方式**: Bearer Token

## 数据模型

### Deployment

部署实例信息

| 字段 | 类型 | 说明 |
|------|------|------|
| id | uint | 部署记录 ID（数据库主键） |
| deploy_id | uint64 | 部署 ID（在 Agent 端标识应用） |
| application | ApplicationInfo | 应用信息 |
| server | ServerInfo | 服务器信息 |
| status | string | 部署状态 |
| deployed_at | string | 部署时间 |

### ApplicationInfo

应用信息

| 字段 | 类型 | 说明 |
|------|------|------|
| id | uint | 应用 ID |
| name | string | 应用名称 |
| description | string | 应用描述 |
| type | string | 应用类型 |
| version | string | 应用版本 |

### ServerInfo

服务器信息

| 字段 | 类型 | 说明 |
|------|------|------|
| id | uint | 服务器 ID |
| ip_address | string | 服务器 IP 地址 |
| agent_port | int | Agent 端口 |

### DeployApplicationRequest

创建部署请求

| 字段 | 类型 | 说明 |
|------|------|------|
| application_id | uint | 应用 ID（从路径参数获取） |
| server_id | uint | 服务器 ID |

### ReportApplicationStatusRequest

上报应用状态请求

| 字段 | 类型 | 说明 |
|------|------|------|
| application_id | uint | 应用 ID |
| server_id | uint | 服务器 ID |
| status | string | 应用状态 |
| deploy_id | uint64 | 部署 ID |

## API 接口

### 1. 获取部署列表

获取部署实例列表，支持按服务器筛选。

**请求**

```http
GET /api/v1/deployment?server_id={server_id}
```

**请求参数**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| server_id | uint | 否 | 服务器 ID，不传则返回所有部署 |

**响应示例**

```json
{
  "code": 0,
  "message": "success",
  "data": [
    {
      "id": 1,
      "deploy_id": 1234567890123456789,
      "application": {
        "id": 1,
        "name": "nginx",
        "description": "Nginx Web Server",
        "type": "compose",
        "version": "1.0.0"
      },
      "server": {
        "id": 1,
        "ip_address": "192.168.1.100",
        "agent_port": 8080
      },
      "status": "running",
      "deployed_at": "2024-01-01 12:00:00"
    }
  ]
}
```

**错误响应**

```json
{
  "code": 1001,
  "message": "参数错误"
}
```

### 2. 创建部署

将应用部署到指定服务器。

**请求**

```http
POST /api/v1/deployment/{application_id}
```

**路径参数**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| application_id | uint | 是 | 应用 ID |

**请求体**

```json
{
  "server_id": 1
}
```

**响应示例**

```json
{
  "code": 0,
  "message": "deploy success",
  "data": null
}
```

**错误响应**

```json
{
  "code": 2001,
  "message": "应用不存在"
}
```

```json
{
  "code": 2002,
  "message": "部署失败"
}
```

### 3. 启动应用

启动已停止的应用。

**请求**

```http
POST /api/v1/deployment/{deployment_id}/start
```

**路径参数**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| deployment_id | uint | 是 | 部署记录 ID |

**响应示例**

```json
{
  "code": 0,
  "message": "start success",
  "data": null
}
```

**错误响应**

```json
{
  "code": 2003,
  "message": "部署不存在"
}
```

```json
{
  "code": 2002,
  "message": "部署失败"
}
```

### 4. 停止应用

停止正在运行的应用。

**请求**

```http
POST /api/v1/deployment/{deployment_id}/stop
```

**路径参数**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| deployment_id | uint | 是 | 部署记录 ID |

**响应示例**

```json
{
  "code": 0,
  "message": "stop success",
  "data": null
}
```

**错误响应**

```json
{
  "code": 2003,
  "message": "部署不存在"
}
```

```json
{
  "code": 2002,
  "message": "部署失败"
}
```

### 5. 卸载部署

卸载部署实例，删除应用并清理资源。

**请求**

```http
DELETE /api/v1/deployment/{deployment_id}
```

**路径参数**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| deployment_id | uint | 是 | 部署记录 ID |

**响应示例**

```json
{
  "code": 0,
  "message": "undeploy success",
  "data": null
}
```

**错误响应**

```json
{
  "code": 2003,
  "message": "部署不存在"
}
```

```json
{
  "code": 2002,
  "message": "部署失败"
}
```

### 6. 获取应用部署的服务器列表

查询指定应用在哪些服务器上有部署。

**请求**

```http
GET /api/v1/deployment/{application_id}/servers
```

**路径参数**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| application_id | uint | 是 | 应用 ID |

**响应示例**

```json
{
  "code": 0,
  "message": "success",
  "data": [
    {
      "id": 1,
      "ip_address": "192.168.1.100",
      "agent_port": 8080
    },
    {
      "id": 2,
      "ip_address": "192.168.1.101",
      "agent_port": 8080
    }
  ]
}
```

**错误响应**

```json
{
  "code": 2001,
  "message": "应用不存在"
}
```

### 7. 上报应用状态

Agent 上报应用运行状态（内部接口）。

**请求**

```http
POST /api/v1/deployment/report-status
```

**请求体**

```json
{
  "application_id": 1,
  "server_id": 1,
  "status": "running",
  "deploy_id": 1234567890123456789
}
```

**响应示例**

```json
{
  "code": 0,
  "message": "success",
  "data": null
}
```

**错误响应**

```json
{
  "code": 1001,
  "message": "参数错误"
}
```

## 状态码说明

| 状态码 | 说明 |
|--------|------|
| 0 | 成功 |
| 1001 | 参数错误 |
| 2001 | 应用不存在 |
| 2002 | 部署失败 |
| 2003 | 部署不存在 |

## 部署状态说明

| 状态 | 说明 |
|------|------|
| running | 应用正在运行 |
| stopped | 应用已停止 |
| not_deployed | 应用已创建但未部署 |
| error | 应用运行出错 |

## 部署流程

### 完整部署流程

```
1. 用户选择应用和服务器
   ↓
2. 系统生成唯一部署 ID
   ↓
3. 调用 Agent API 部署应用
   ↓
4. Agent 执行部署操作
   ↓
5. 部署成功？
   ├─ 是 → 创建部署记录，状态为 running
   └─ 否 → 回滚：调用 Agent 删除应用
          → 返回部署失败错误
```

### 部署回滚机制

当部署失败时，系统会自动执行以下回滚操作：
1. 调用 Agent API 删除已部署的应用
2. 不创建部署记录
3. 向用户返回详细的失败信息

## 使用示例

### 示例 1：获取所有部署

```bash
curl -X GET "http://localhost:8080/api/v1/deployment" \
  -H "Authorization: Bearer {token}"
```

### 示例 2：获取指定服务器的部署

```bash
curl -X GET "http://localhost:8080/api/v1/deployment?server_id=1" \
  -H "Authorization: Bearer {token}"
```

### 示例 3：创建部署

```bash
curl -X POST "http://localhost:8080/api/v1/deployment/1" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "server_id": 1
  }'
```

### 示例 4：启动应用

```bash
curl -X POST "http://localhost:8080/api/v1/deployment/1/start" \
  -H "Authorization: Bearer {token}"
```

### 示例 5：停止应用

```bash
curl -X POST "http://localhost:8080/api/v1/deployment/1/stop" \
  -H "Authorization: Bearer {token}"
```

### 示例 6：卸载部署

```bash
curl -X DELETE "http://localhost:8080/api/v1/deployment/1" \
  -H "Authorization: Bearer {token}"
```

### 示例 7：获取应用部署的服务器列表

```bash
curl -X GET "http://localhost:8080/api/v1/deployment/1/servers" \
  -H "Authorization: Bearer {token}"
```

## 注意事项

1. **部署前置条件**：确保目标服务器的 Agent 正常运行
2. **资源限制**：注意服务器资源限制，避免过度部署
3. **端口冲突**：同一台服务器上不能有端口冲突的部署
4. **数据备份**：卸载前备份重要数据
5. **权限管理**：确保 Agent 有足够的权限执行部署操作
6. **网络配置**：确保服务器之间的网络连通性
7. **监控告警**：建议配置监控和告警机制

## 相关模块

- **Application**：应用实例管理，提供可部署的应用
- **Server**：服务器管理，管理目标服务器
- **Agent**：部署执行者，在服务器上执行实际操作
