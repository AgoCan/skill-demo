---
name: "deployment"
description: "当用户需要开发 Squirrel 部署管理页面、实现部署列表、表单、详情等功能时调用。包含组件实现、API 接口和国际化配置。"
---

# Squirrel 部署管理页面开发规范

## 触发条件（When）

当用户需要开发部署管理相关功能时调用，包括：
- 创建部署列表页面
- 实现新建部署表单
- 开发部署详情组件
- 实现启动/停止/卸载操作
- 配置部署相关国际化

## 执行步骤（How）

### 1. 理解核心概念

部署实例是应用（Application）在特定服务器（Server）上的具体运行实例，由唯一的部署 ID 标识。

```
Application（应用实例）
    │
    │ [部署到服务器]
    ▼
Deployment（部署实例）
    │
    ├──> Server（目标服务器）
    │
    └──> Agent（部署执行者）
```

### 2. 创建目录结构

```
views/Deployment/
├── index.vue                 # 页面入口
├── components/
│   ├── DeploymentTable.vue   # 部署列表表格
│   ├── DeploymentForm.vue    # 新建部署表单
│   ├── DeploymentDetail.vue  # 部署详情
│   └── UndeployConfirm.vue   # 卸载确认弹窗
├── composables/
│   └── useDeployment.ts      # 部署相关的可组合函数
├── types/
│   └── index.ts              # 页面类型定义
└── styles/
    └── deployment.scss       # 部署相关组件样式
```

### 3. 实现功能模块

按以下优先级实现：

| 功能 | 优先级 | 说明 |
|------|--------|------|
| 部署列表管理 | ⭐⭐⭐⭐ | 表格展示、服务器筛选 |
| 应用状态控制 | ⭐⭐⭐⭐ | 启动、停止操作 |
| 新建部署 | ⭐⭐⭐ | 选择应用和服务器 |
| 部署卸载 | ⭐⭐⭐ | 确认弹窗、资源清理 |
| 查看详情 | ⭐⭐⭐ | 基本信息、操作按钮 |
| 状态标识 | ⭐⭐⭐⭐ | 不同颜色区分状态 |

### 4. 遵循设计规范

- 使用项目统一的配色方案（主色 #4fc3f7）
- 表格行布局，不是方块卡片
- 操作按钮使用图标按钮
- 遵循 Squirrel 紧凑风格页面设计规范（调用 `compact-page-style` skill）

## 输出结果（What）

完整的部署管理页面，包含：
- 部署列表展示（支持按服务器筛选）
- 新建部署功能
- 启动/停止/卸载操作
- 部署详情查看
- 完整的国际化支持

---

## 详细参考文档

| 文档 | 说明 |
|------|------|
| [API 接口文档](./source/deployment-api.md) | 所有 API 接口定义和示例 |
| [类型定义](./source/types.md) | TypeScript 类型定义 |
| [组件实现](./source/components.md) | 组件代码实现细节 |
| [国际化配置](./source/i18n.md) | 语言包配置 |
| [模块说明](./source/deployment.md) | 功能详细说明和最佳实践 |

---

## API 接口速查

| 接口 | 方法 | 说明 |
|------|------|------|
| `/api/v1/deployment` | GET | 获取部署列表 |
| `/api/v1/deployment/deploy/{application_id}` | POST | 部署应用 |
| `/api/v1/deployment/start/{deployment_id}` | POST | 启动应用 |
| `/api/v1/deployment/stop/{deployment_id}` | POST | 停止应用 |
| `/api/v1/deployment/deploy/{deployment_id}` | DELETE | 卸载部署 |
| `/api/v1/deployment/{deployment_id}/servers` | GET | 获取应用部署的服务器列表 |

详细接口文档请参考 [deployment-api.md](./source/deployment-api.md)。

---

## 部署状态说明

| 状态 | 说明 | 可执行操作 |
|------|------|-----------|
| `running` | 应用正在正常运行 | 停止、卸载 |
| `stopped` | 应用已停止 | 启动、卸载 |
| `not_deployed` | 应用已创建但未部署 | 等待部署完成 |
| `error` | 应用运行出错 | 停止、卸载、重新部署 |

---

## 最佳实践

1. **列表布局**：使用表格行布局，不是方块卡片
2. **状态管理**：使用响应式数据管理弹窗显示状态
3. **错误处理**：API 调用添加 try-catch 处理
4. **国际化**：所有文本使用 $t() 函数包裹
5. **表单验证**：应用和服务器都为必填项
6. **组件复用**：参考 `component-reusability` skill 设计可复用组件
7. **筛选优化**：使用 computed 实现实时过滤
8. **状态标识**：使用不同颜色和图标区分部署状态
9. **操作权限**：根据部署状态显示不同的操作按钮
10. **回滚机制**：部署失败时自动回滚，清理已部署的应用

---

## 相关 Skill

- `compact-page-style` - 紧凑风格页面设计规范
- `component-reusability` - 组件复用设计
- `vue-i18n-setup` - 国际化配置
- `layout-architecture` - 布局架构设计
