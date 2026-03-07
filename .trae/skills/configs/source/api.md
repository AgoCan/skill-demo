# Configs API 接口文档

## 基础信息

- **Base URL**: `{{url}}/api/v1`
- **Content-Type**: `application/json`
- **认证方式**: JWT Token

---

## API 列表

### 1. 获取配置列表

**接口信息**
- **Method**: `GET`
- **Endpoint**: `/api/v1/config`

**请求示例**
```http
GET {{url}}/api/v1/config
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
      "key": "app_name",
      "value": "Squirrel"
    },
    {
      "id": 2,
      "key": "app_version",
      "value": "1.0.0"
    }
  ]
}
```

---

### 2. 获取配置详情

**接口信息**
- **Method**: `GET`
- **Endpoint**: `/api/v1/config/{id}`

**请求示例**
```http
GET {{url}}/api/v1/config/1
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
    "key": "app_name",
    "value": "Squirrel"
  }
}
```

---

### 3. 创建配置

**接口信息**
- **Method**: `POST`
- **Endpoint**: `/api/v1/config`

**请求体**
```json
{
  "key": "app_name",
  "value": "Squirrel"
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

### 4. 更新配置

**接口信息**
- **Method**: `POST`
- **Endpoint**: `/api/v1/config/{id}`

**请求体**
```json
{
  "id": 1,
  "key": "app_name",
  "value": "Squirrel Dev"
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

### 5. 删除配置

**接口信息**
- **Method**: `DELETE`
- **Endpoint**: `/api/v1/config/{id}`

**请求示例**
```http
DELETE {{url}}/api/v1/config/1
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

---

## 错误码说明

| 错误码 | 说明 |
|--------|------|
| 0 | 成功 |
| 400 | 参数错误 |
| 404 | 配置不存在 |
| 500 | 服务器内部错误 |
