# Application API 文档

## 基础信息

- **Base URL**: `{{url}}/api/v1`
- **Content-Type**: `application/json`
- **认证方式**: JWT Token

---

## API 列表

### 1. 获取应用列表

获取所有应用实例列表。

**接口信息**
- **Method**: `GET`
- **Endpoint**: `/api/v1/application`

**请求示例**
```http
GET {{url}}/api/v1/application
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
      "name": "nginx",
      "description": "Nginx web server",
      "type": "compose",
      "content": "version: '3'\nservices:\n  nginx:\n    image: nginx:latest\n    container_name: nginx-server\n    restart: always\n    ports:\n      - \"80:80\"\n    volumes:\n      - nginx_html:/usr/share/nginx/html\nvolumes:\n  nginx_html:",
      "version": "1.25.0"
    },
    {
      "id": 2,
      "name": "mysql",
      "description": "MySQL database",
      "type": "compose",
      "content": "version: '3'\nservices:\n  mysql:\n    image: mysql:8.0\n    environment:\n      MYSQL_ROOT_PASSWORD: root\n    ports:\n      - \"3306:3306\"",
      "version": "8.0.35"
    }
  ]
}
```

**响应字段说明**

| 字段 | 类型 | 说明 |
|------|------|------|
| code | number | 响应码，0 表示成功 |
| message | string | 响应消息 |
| data | array | 应用列表数据 |
| data[].id | number | 应用 ID |
| data[].name | string | 应用名称 |
| data[].description | string | 应用描述 |
| data[].type | string | 应用类型 |
| data[].content | string | 应用配置内容 |
| data[].version | string | 应用版本 |

---

### 2. 获取应用详情

根据 ID 获取单个应用实例的详细信息。

**接口信息**
- **Method**: `GET`
- **Endpoint**: `/api/v1/application/{id}`

**路径参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | number | 是 | 应用 ID |

**请求示例**
```http
GET {{url}}/api/v1/application/1
Content-Type: application/json
Authorization: Bearer {{token}}
```

**成功响应示例**
```json
{
  "code": 0,
  "message": "success",
  "data": {
    "id": 1,
    "name": "nginx",
    "description": "Nginx web server",
    "type": "compose",
    "content": "version: '3'\nservices:\n  nginx:\n    image: nginx:latest\n    container_name: nginx-server\n    restart: always\n    ports:\n      - \"80:80\"\n    volumes:\n      - nginx_html:/usr/share/nginx/html\nvolumes:\n  nginx_html:",
    "version": "1.25.0"
  }
}
```

---

### 3. 创建应用

创建一个新的应用实例。

**接口信息**
- **Method**: `POST`
- **Endpoint**: `/api/v1/application`

**请求体参数**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| name | string | 是 | 应用名称 |
| description | string | 否 | 应用描述 |
| type | string | 是 | 应用类型（如：compose） |
| content | string | 是 | 应用配置内容 |
| version | string | 否 | 应用版本号 |

**请求示例**
```http
POST {{url}}/api/v1/application
Content-Type: application/json
Authorization: Bearer {{token}}

{
  "name": "nginx",
  "description": "Nginx web server",
  "type": "compose",
  "content": "version: '3'\nservices:\n  nginx:\n    image: nginx:latest\n    container_name: nginx-server\n    restart: always\n    ports:\n      - \"80:80\"\n    volumes:\n      - nginx_html:/usr/share/nginx/html\nvolumes:\n  nginx_html:",
  "version": "1.25.0"
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

**错误响应示例**
```json
{
  "code": 400,
  "message": "参数错误"
}
```

---

### 4. 更新应用

更新指定 ID 的应用实例信息。

**接口信息**
- **Method**: `POST`
- **Endpoint**: `/api/v1/application/{id}`

**路径参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | number | 是 | 应用 ID |

**请求体参数**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| name | string | 是 | 应用名称 |
| description | string | 否 | 应用描述 |
| type | string | 是 | 应用类型 |
| content | string | 是 | 应用配置内容 |
| version | string | 否 | 应用版本号 |

**请求示例**
```http
POST {{url}}/api/v1/application/1
Content-Type: application/json
Authorization: Bearer {{token}}

{
  "name": "nginx",
  "description": "Nginx web server updated",
  "type": "compose",
  "content": "version: '3'\nservices:\n  nginx:\n    image: nginx:1.25\n    ports:\n      - '80:80'",
  "version": "1.0.1"
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

### 5. 删除应用

删除指定 ID 的应用实例。

**接口信息**
- **Method**: `DELETE`
- **Endpoint**: `/api/v1/application/{id}`

**路径参数**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | number | 是 | 应用 ID |

**请求示例**
```http
DELETE {{url}}/api/v1/application/1
Content-Type: application/json
Authorization: Bearer {{token}}
```

**成功响应示例**
```json
{
  "code": 0,
  "message": "success",
  "data": "success"
}
```

**错误响应示例**

应用不存在：
```json
{
  "code": 404,
  "message": "应用不存在"
}
```

---

## 错误码说明

| 错误码 | 说明 |
|--------|------|
| 0 | 成功 |
| 400 | 参数错误 |
| 404 | 资源不存在 |
| 500 | 服务器内部错误 |

---

## 数据类型定义

### Application
应用实例数据结构。

```typescript
interface Application {
  id?: number;           // 应用 ID（自动生成，创建时不需要）
  name: string;          // 应用名称
  description: string;   // 应用描述
  type: string;          // 应用类型（如：compose）
  content: string;       // 应用配置内容
  version: string;       // 应用版本号
}
```

---

## 使用示例

### 示例 1：创建一个 Nginx 应用

```bash
curl -X POST http://localhost:10700/api/v1/application \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "name": "nginx",
    "description": "Nginx web server",
    "type": "compose",
    "content": "version: '"'"'3'"'"'\nservices:\n  nginx:\n    image: nginx:latest\n    container_name: nginx-server\n    restart: always\n    ports:\n      - \"80:80\"\n    volumes:\n      - nginx_html:/usr/share/nginx/html\nvolumes:\n  nginx_html:",
    "version": "1.25.0"
  }'
```

### 示例 2：获取所有应用列表

```bash
curl -X GET http://localhost:10700/api/v1/application \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### 示例 3：更新应用配置

```bash
curl -X POST http://localhost:10700/api/v1/application/1 \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "name": "nginx",
    "description": "Nginx web server updated",
    "type": "compose",
    "content": "version: '"'"'3'"'"'\nservices:\n  nginx:\n    image: nginx:1.25\n    ports:\n      - "80:80"\n    volumes:\n      - nginx_html:/usr/share/nginx/html",
    "version": "1.0.1"
  }'
```

### 示例 4：删除应用

```bash
curl -X DELETE http://localhost:10700/api/v1/application/1 \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

---

## 注意事项

1. **应用删除限制**：已部署的应用无法直接删除，需要先卸载所有相关部署
2. **内容验证**：对于 compose 类型的应用，系统会验证内容的格式和语法
3. **版本管理**：建议使用语义化版本号管理应用版本
4. **权限控制**：所有 API 接口都需要有效的 JWT Token 认证
5. **并发控制**：多个请求同时操作同一应用时，以后到达的请求为准

---

## 相关文档

- [Application 模块文档](./application.md)
- [Deployment API 文档](./deployment-api.md)
- [App Store API 文档](./appstore-api.md)
