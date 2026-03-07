---
name: application
description: 当用户需要开发 Squirrel 应用管理页面、实现应用列表、表单、详情等功能时调用。包含组件实现、API 接口和国际化配置。
---

# Squirrel 应用管理页面开发

## 触发条件（When）

当用户需要：
- 开发应用管理（Application）页面的任何功能
- 实现应用列表、添加、编辑、删除、详情查看等操作
- 配置应用相关的 API 接口
- 添加应用相关的国际化文本
- 实现应用搜索、排序、过滤功能

## 执行步骤（How）

### 1. 理解应用管理模块定位

应用管理负责管理从应用商店导入的应用实例，提供完整的 CRUD 操作。

**核心概念**：
- **应用实例（Application）**：从应用商店导入的具体应用配置，代表一个可以部署到服务器上的应用
- **应用类型**：compose（Docker Compose）、script（脚本）、binary（二进制）

**模块关系**：
```
App Store（应用模板）→ [导入] → Application（应用实例）→ [部署] → Deployment（部署实例）
```

详细说明请参考 [source/application.md](source/application.md)

### 2. 实现应用管理功能

#### 2.1 目录结构

```
views/Application/
├── index.vue                 # 页面入口
├── components/
│   ├── ApplicationTable.vue  # 应用列表表格
│   ├── ApplicationForm.vue   # 添加/编辑表单
│   ├── ApplicationDetail.vue # 应用详情
│   └── DeleteConfirm.vue     # 删除确认弹窗
├── composables/
│   └── useApplication.ts     # 应用相关的可组合函数
└── types/
    └── index.ts              # 页面类型定义
```

#### 2.2 核心功能清单

在开发过程中，按以下清单逐项实现：

- [ ] **应用列表展示**：表格行布局，显示名称、类型、版本、描述
- [ ] **模糊搜索**：按应用名称和描述进行实时过滤
- [ ] **排序功能**：支持按名称、类型、版本排序
- [ ] **添加应用**：表单验证（名称、类型、版本、内容必填）
- [ ] **编辑应用**：预填充数据，保存后刷新列表
- [ ] **删除应用**：确认对话框，删除后刷新列表
- [ ] **查看详情**：显示完整配置，代码语法高亮
- [ ] **部署应用**：跳转到部署页面

#### 2.3 类型定义

```typescript
// types/index.ts
export interface Application {
  id: number
  name: string
  description: string
  type: 'compose' | 'script' | 'binary'
  content: string
  version: string
}

export interface CreateApplicationRequest {
  name: string
  description: string
  type: string
  content: string
  version: string
}

export interface UpdateApplicationRequest {
  id: number
  name: string
  description: string
  type: string
  content: string
  version: string
}
```

详细的组件实现和代码示例请参考 [source/application-components.md](source/application-components.md)

### 3. 配置 API 接口

#### 3.1 API 接口列表

| 方法 | 端点 | 说明 |
|------|------|------|
| GET | /api/v1/application | 获取应用列表 |
| GET | /api/v1/application/:id | 获取应用详情 |
| POST | /api/v1/application | 创建应用 |
| POST | /api/v1/application/:id | 更新应用 |
| DELETE | /api/v1/application/:id | 删除应用 |

#### 3.2 API 封装示例

```typescript
// api/application.ts
import { get, post, del } from '@/utils/request'
import type { Application, CreateApplicationRequest, UpdateApplicationRequest } from '@/types'

export function fetchApplications(): Promise<Application[]> {
  return get('/application')
}

export function createApplication(data: CreateApplicationRequest): Promise<string> {
  return post('/application', data)
}

export function updateApplication(id: number, data: UpdateApplicationRequest): Promise<string> {
  return post(`/application/${id}`, data)
}

export function deleteApplication(id: number): Promise<string> {
  return del(`/application/${id}`)
}
```

完整的 API 文档和响应示例请参考 [source/application-api.md](source/application-api.md)

### 4. 配置国际化

#### 4.1 必需的国际化键值

```typescript
// lang/zh-CN/application.ts
export default {
  title: '应用管理',
  listTitle: '应用列表',
  addApplication: '添加应用',
  editApplication: '编辑应用',
  deleteApplication: '删除应用',
  viewDetail: '查看详情',
  applicationName: '应用名称',
  applicationType: '应用类型',
  applicationVersion: '应用版本',
  applicationDescription: '应用描述',
  applicationContent: '应用内容',
  searchPlaceholder: '搜索应用名称或描述...',
  createSuccess: '应用创建成功',
  updateSuccess: '应用更新成功',
  deleteSuccess: '应用删除成功',
  confirmDelete: '确认删除',
  deleteWarning: '确定要删除应用 "{name}" 吗？此操作不可恢复。',
  required: '必填项'
}
```

完整的国际化配置请参考 [source/application-i18n.md](source/application-i18n.md)

### 5. 遵循设计规范

- 使用项目统一的配色方案（主色 #4fc3f7）
- 表格行悬停效果
- 操作按钮使用图标按钮
- 弹窗使用圆角卡片设计
- 表单分组展示（基本信息、应用内容）
- 遵循 Squirrel 紧凑风格页面设计规范（调用 `compact-page-style` skill）

## 输出结果（What）

完成应用管理页面开发后，应产出：

1. **功能完整的页面**：
   - 应用列表展示（表格布局）
   - 模糊搜索和排序功能
   - 添加/编辑/删除应用功能
   - 应用详情查看功能
   - 部署入口

2. **规范的代码结构**：
   - 清晰的组件划分
   - 完整的类型定义
   - 统一的 API 封装
   - 完善的国际化支持

3. **符合设计规范**：
   - 统一的视觉风格
   - 良好的交互体验
   - 响应式布局

## 相关 Skills

- `compact-page-style`: Squirrel 紧凑风格页面设计规范
- `component-reusability`: 可复用组件设计原则
- `vue-i18n-setup`: Vue3 国际化配置
- `layout-architecture`: Squirrel 运维平台前端架构

## 参考文档

- [应用管理模块详细说明](source/application.md)
- [API 接口文档](source/application-api.md)
- [组件实现详解](source/application-components.md)
- [国际化配置](source/application-i18n.md)
