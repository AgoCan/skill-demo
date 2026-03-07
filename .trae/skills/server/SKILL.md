---
name: server-management
description: 当用户需要开发 Squirrel 服务器管理页面、实现服务器列表、表单、详情等功能时调用。包含组件实现、API 接口和国际化配置。
---

# Squirrel 服务器管理页面开发规范

服务器管理页面提供对服务器的增删改查功能，包括服务器列表展示、添加/编辑服务器、查看详情等功能。

## 触发条件（When）

当用户请求以下任务时触发此 Skill：

- 开发服务器管理页面
- 实现服务器列表展示
- 创建服务器表单
- 实现服务器详情查看
- 对接服务器管理 API

## 执行步骤（How）

### 开发工作流

在开始执行前复制以下清单，并在每一步完成后显式标记状态。

- Step 1：创建目录结构（views/Server/）
- Step 2：定义类型（types/index.ts）
- Step 3：实现 API 接口（api/server.ts）
- Step 4：创建服务器表格组件（ServerTable.vue）
- Step 5：创建表单组件（ServerForm.vue）
- Step 6：创建详情组件（ServerDetail.vue）
- Step 7：创建删除确认组件（DeleteConfirm.vue）
- Step 8：实现主页面逻辑（index.vue）
- Step 9：添加国际化配置（lang/zh-CN/server.ts）
- Step 10：配置路由；（反馈闭环）验证所有功能是否正常工作

### 核心开发要点

1. **目录结构**
   ```
   views/Server/
   ├── index.vue                 # 页面入口
   ├── components/
   │   ├── ServerTable.vue       # 服务器列表表格
   │   ├── ServerForm.vue        # 添加/编辑表单
   │   ├── ServerDetail.vue      # 服务器详情弹窗
   │   └── DeleteConfirm.vue     # 删除确认弹窗
   ├── composables/
   │   └── useServer.ts          # 服务器相关的可组合函数
   ├── types/
   │   └── index.ts              # 类型定义
   └── styles/
       └── server.scss           # 服务器相关组件样式
   ```

2. **组件实现要点**
   - ServerTable：使用表格行布局，不是方块卡片
   - ServerForm：支持添加和编辑两种模式，编辑时密码和密钥可选填
   - ServerDetail：展示基本信息和系统信息
   - DeleteConfirm：删除确认弹窗，显示警告图标

3. **API 接口**
   - GET /api/v1/server - 获取服务器列表
   - GET /api/v1/server/{id} - 获取服务器详情
   - POST /api/v1/server - 创建服务器
   - POST /api/v1/server/{id} - 更新服务器
   - DELETE /api/v1/server/{id} - 删除服务器

4. **注意事项**
   - 终端连接只有按钮，不生成终端组件
   - 使用项目统一的配色方案（主色 #4fc3f7）
   - 所有文本使用 $t() 函数包裹

## 输出结果（What）

完成开发后，应产出以下内容：

1. **类型定义**：Server、CreateServerRequest、UpdateServerRequest 等
2. **API 接口**：fetchServers、createServer、updateServer、deleteServer
3. **组件文件**：ServerTable、ServerForm、ServerDetail、DeleteConfirm
4. **主页面**：index.vue 包含所有逻辑
5. **国际化配置**：lang/zh-CN/server.ts

## 参考文件

详细参考信息请参见：

- [api.md](./api.md) - API 接口示例数据
- [types-reference.md](./types-reference.md) - 类型定义
- [i18n-reference.md](./i18n-reference.md) - 国际化配置

## 快速参考

### 路由配置

```typescript
{
  path: '/servers',
  name: 'Servers',
  component: () => import('@/views/Server/index.vue')
}
```

### 主要功能

- 服务器列表展示
- 添加/编辑服务器
- 查看服务器详情
- 删除服务器
- 终端连接按钮（仅按钮）

## 设计规范

- 使用项目统一的配色方案（主色 #4fc3f7）
- 表格行悬停效果
- 状态徽章使用圆点和背景色
- 操作按钮使用图标按钮
- 弹窗使用圆角卡片设计
- 表单分组展示（基本信息、认证信息）

## 最佳实践

1. **列表布局**：使用表格行布局，不是方块卡片
2. **认证可选**：编辑时密码和密钥可以不填写
3. **状态管理**：使用响应式数据管理弹窗显示状态
4. **错误处理**：API 调用添加 try-catch 处理
5. **国际化**：所有文本使用 $t() 函数包裹
