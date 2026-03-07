# API 接口参考

本文档提供 Squirrel 运维平台的所有 API 接口定义。

## 目录

- [认证接口](#认证接口)
- [服务器管理](#服务器管理)
- [应用管理](#应用管理)
- [配置管理](#配置管理)
- [部署管理](#部署管理)
- [脚本管理](#脚本管理)
- [监控接口](#监控接口)
- [应用商店](#应用商店)

## 认证接口

### 用户登录

```
POST /api/v1/login
```

用户登录认证接口。

## 服务器管理

### 服务器列表

```
GET /api/v1/server
```

获取所有服务器列表。

### 获取单个服务器

```
GET /api/v1/server/{id}
```

获取指定服务器的详细信息。

### 创建服务器

```
POST /api/v1/server
```

创建新的服务器记录。

### 更新服务器

```
POST /api/v1/server/{id}
```

更新指定服务器的信息。

### 删除服务器

```
DELETE /api/v1/server/{id}
```

删除指定的服务器记录。

## 应用管理

### 应用列表

```
GET /api/v1/application
```

获取所有应用列表。

### 获取单个应用

```
GET /api/v1/application/{id}
```

获取指定应用的详细信息。

### 创建应用

```
POST /api/v1/application
```

创建新的应用记录。

### 更新应用

```
POST /api/v1/application/{id}
```

更新指定应用的信息。

### 删除应用

```
DELETE /api/v1/application/{id}
```

删除指定的应用记录。

## 配置管理

### 配置列表

```
GET /api/v1/config
```

获取所有配置列表。

### 获取单个配置

```
GET /api/v1/config/{id}
```

获取指定配置的详细信息。

### 创建配置

```
POST /api/v1/config
```

创建新的配置记录。

### 更新配置

```
POST /api/v1/config/{id}
```

更新指定配置的信息。

### 删除配置

```
DELETE /api/v1/config/{id}
```

删除指定的配置记录。

## 部署管理

### 部署应用

```
POST /api/v1/deployment/deploy/{id}
```

部署应用到服务器。

### 停止部署

```
POST /api/v1/deployment/stop/{id}
```

停止指定的部署。

### 启动部署

```
POST /api/v1/deployment/start/{id}
```

启动指定的部署。

## 脚本管理

### 脚本列表

```
GET /api/v1/scripts
```

获取所有脚本列表。

### 获取单个脚本

```
GET /api/v1/scripts/{id}
```

获取指定脚本的详细信息。

### 创建脚本

```
POST /api/v1/scripts
```

创建新的脚本记录。

### 更新脚本

```
POST /api/v1/scripts/{id}
```

更新指定脚本的信息。

### 删除脚本

```
DELETE /api/v1/scripts/{id}
```

删除指定的脚本记录。

### 执行脚本

```
POST /api/v1/scripts/execute
```

执行指定的脚本。

### 获取执行结果

```
GET /api/v1/scripts/{id}/results
```

获取脚本的执行结果。

## 监控接口

### 系统统计

```
GET /api/v1/monitor/stats/{server_id}
```

获取指定服务器的系统统计信息（CPU、内存、磁盘、网络）。

### 单个磁盘 IO

```
GET /api/v1/monitor/stats/io/{server_id}/{disk}
```

获取指定服务器的单个磁盘 IO 数据。

### 所有磁盘 IO

```
GET /api/v1/monitor/stats/io/{server_id}/all
```

获取指定服务器的所有磁盘 IO 数据。

### 单个网络 IO

```
GET /api/v1/monitor/stats/net/{server_id}/{net}
```

获取指定服务器的单个网络 IO 数据。

### 所有网络 IO

```
GET /api/v1/monitor/stats/net/{server_id}/all
```

获取指定服务器的所有网络 IO 数据。

### 基础监控数据

```
GET /api/v1/monitor/base/{server_id}/{start}/{end}
```

获取指定时间范围的基础监控数据。

### 磁盘监控数据

```
GET /api/v1/monitor/disk/{server_id}/{start}/{end}
```

获取指定时间范围的磁盘监控数据。

### 网络监控数据

```
GET /api/v1/monitor/net/{server_id}/{start}/{end}
```

获取指定时间范围的网络监控数据。

## 应用商店

### 应用模板列表

```
GET /api/v1/app-store
```

获取所有应用模板列表。

### 获取单个应用模板

```
GET /api/v1/app-store/{id}
```

获取指定应用模板的详细信息。

### 创建应用模板

```
POST /api/v1/app-store
```

创建新的应用模板。

### 更新应用模板

```
POST /api/v1/app-store/{id}
```

更新指定应用模板的信息。

### 删除应用模板

```
DELETE /api/v1/app-store/{id}
```

删除指定的应用模板。
