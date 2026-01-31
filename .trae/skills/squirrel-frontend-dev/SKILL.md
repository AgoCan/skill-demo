---
name: "squirrel-frontend-dev"
description: "Squirrel 运维平台前端开发技能。提供 API 接口、设计规则、页面功能的完整参考。在开发 Squirrel 前端时调用。"
---

# Squirrel 运维平台前端开发技能

## 项目概述
为 Squirrel API Server 构建前端界面，使用 Vue3 + TypeScript + Vite 技术栈，遵循反主流设计美学的现代化运维平台。

## API 接口参考

### 认证 (Auth)
```
POST /api/v1/login
```
用户登录

### 服务器管理 (Server)
```
GET    /api/v1/server           # 获取所有服务器
GET    /api/v1/server/{id}      # 获取单个服务器
POST   /api/v1/server           # 创建服务器
POST   /api/v1/server/{id}      # 更新服务器
DELETE /api/v1/server/{id}      # 删除服务器
```

### 应用管理 (Application)
```
GET    /api/v1/application           # 获取所有应用
GET    /api/v1/application/{id}      # 获取单个应用
POST   /api/v1/application           # 创建应用
POST   /api/v1/application/{id}      # 更新应用
DELETE /api/v1/application/{id}      # 删除应用
```

### 配置管理 (Config)
```
GET    /api/v1/config           # 获取所有配置
GET    /api/v1/config/{id}      # 获取单个配置
POST   /api/v1/config           # 创建配置
POST   /api/v1/config/{id}      # 更新配置
DELETE /api/v1/config/{id}      # 删除配置
```

### 部署管理 (Deployment)
```
POST /api/v1/deployment/deploy/{id}   # 部署应用到服务器
POST /api/v1/deployment/stop/{id}      # 停止部署
POST /api/v1/deployment/start/{id}     # 启动部署
```

### 脚本管理 (Script)
```
GET    /api/v1/scripts               # 获取所有脚本
GET    /api/v1/scripts/{id}          # 获取单个脚本
POST   /api/v1/scripts               # 创建脚本
POST   /api/v1/scripts/{id}          # 更新脚本
DELETE /api/v1/scripts/{id}          # 删除脚本
POST   /api/v1/scripts/execute       # 执行脚本
GET    /api/v1/scripts/{id}/results  # 获取脚本执行结果
```

### 监控 (Monitor)
```
GET /api/v1/monitor/stats/{server_id}              # 获取系统统计
GET /api/v1/monitor/stats/io/{server_id}/{disk}     # 获取单个磁盘IO
GET /api/v1/monitor/stats/io/{server_id}/all       # 获取所有磁盘IO
GET /api/v1/monitor/stats/net/{server_id}/{net}    # 获取单个网络IO
GET /api/v1/monitor/stats/net/{server_id}/all      # 获取所有网络IO
GET /api/v1/monitor/base/{server_id}/{start}/{end} # 获取基础监控数据
GET /api/v1/monitor/disk/{server_id}/{start}/{end} # 获取磁盘监控数据
GET /api/v1/monitor/net/{server_id}/{start}/{end}  # 获取网络监控数据
```

### 应用商店 (AppStore)
```
GET    /api/v1/app-store           # 获取所有应用模板
GET    /api/v1/app-store/{id}      # 获取单个应用模板
POST   /api/v1/app-store           # 创建应用模板
POST   /api/v1/app-store/{id}      # 更新应用模板
DELETE /api/v1/app-store/{id}      # 删除应用模板
```

## 页面功能清单

### 登录页 (Login)
- 用户名密码登录
- 记住密码
- 错误提示
- 深色背景

### 服务器管理 (Server)
- 服务器列表展示
- 创建服务器
- 编辑服务器
- 删除服务器
- 服务器状态显示

### 应用管理 (Application)
- 应用列表展示
- 创建应用
- 编辑应用
- 删除应用
- 应用配置编辑器

### 监控页面 (Monitor)
- 系统统计展示 (CPU、内存、磁盘、网络)
- 磁盘 IO 监控
- 网络 IO 监控
- 历史数据图表

### 部署管理 (Deployment)
- 部署列表
- 部署应用
- 启动/停止部署
- 部署状态显示

### 脚本管理 (Script)
- 脚本列表
- 创建脚本
- 编辑脚本
- 删除脚本
- 执行脚本
- 查看执行结果

### 配置管理 (Config)
- 配置列表
- 创建配置
- 编辑配置
- 删除配置

### 应用商店 (AppStore)
- 应用模板列表
- 查看应用详情
- 使用模板创建应用

## 设计规则

### 角色设定
你是一位资深独立设计师，专注于"反主流"的网页美学。你鄙视千篇一律的 SaaS 模板，追求每个像素都有温度。设计出来的平台是让人感觉很轻盈的状态。

### 编写代码目录

front

### ❌ 绝对禁止项

#### 配色禁止
- 紫色/靛蓝色/蓝紫渐变（#6366F1、#8B5CF6）
- 纯平背景色
- Tailwind 默认色板

#### 布局禁止
- Hero + 三卡片布局
- 完美居中对齐
- 等宽多栏（必须不对称）

#### 文案禁止
- 高深的专业名词和无意义的空话
- Lorem Ipsum 占位文本
- 被动语态和长句

#### 组件禁止
- Shadcn/Material UI 默认组件（必须深度定制）
- Emoji 作为功能图标
- 线性动画（ease-in-out）

### ✅ 必须遵守项

#### 文案风格
- 口语化，像朋友聊天
- 具体化，有数字和场景
- 可以幽默、自嘲、甚至挑衅
- 每句话不超过 15 个字

#### 图片系统
- 图标：使用 Iconify 图标库（https://iconify.design）
- 占位图：使用 Picsum Photos（https://picsum.photos）
- 真实图片：使用 Pexels 搜索（https://www.pexels.com）
- 插画：使用 unDraw（https://undraw.co）

#### 注意事项
- 必须有导航栏和侧边栏
- 所有页面都必须有浅色背景
- 蓝色系列是运维平台的现代化颜色
- 不要使用终端的方式创建目录内容

## 技术栈
- Vue3
- TypeScript
- Vite

始终记住：追求每个像素都有温度，鄙视千篇一律的模板！