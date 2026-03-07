---
name: app-store
description: 当用户需要开发 Squirrel 应用商店页面、实现应用模板列表、表单、搜索、分类筛选、详情查看等功能时调用。
---

# Squirrel 应用商店页面开发

## 触发条件（When）

当用户请求以下任务时触发此 Skill：

- 开发应用商店页面功能
- 实现应用模板列表展示
- 创建/编辑/删除应用模板表单
- 实现应用搜索和分类筛选
- 查看应用详情功能
- 对接应用商店相关 API 接口

## 执行步骤（How）

### Step 1：确认页面结构

应用商店页面采用以下目录结构：

```
views/AppStore/
├── index.vue                 # 页面入口
├── components/
│   ├── AppTable.vue          # 应用列表表格
│   ├── AppForm.vue           # 添加/编辑表单
│   ├── AppDetail.vue         # 应用详情弹窗
│   ├── DeleteConfirm.vue     # 删除确认弹窗
│   └── CategoryFilter.vue    # 分类筛选组件
├── composables/
│   └── useAppStore.ts        # 可组合函数
└── types/
    └── index.ts              # 页面类型定义
```

### Step 2：实现核心功能

按优先级实现以下功能：

| 优先级 | 功能 | 说明 |
|--------|------|------|
| ⭐⭐⭐⭐ | 应用列表 | 表格行布局展示应用，含图标、名称、描述、类型、分类、版本、下载次数、状态 |
| ⭐⭐⭐⭐ | 模糊搜索 | 按名称、描述、标签实时过滤 |
| ⭐⭐⭐⭐ | 分类筛选 | 按分类（Web应用/数据库/中间件/DevOps）筛选 |
| ⭐⭐⭐⭐ | 类型筛选 | 按类型筛选 |
| ⭐⭐⭐⭐ | 查看详情 | 弹窗展示完整信息，模板内容支持复制 |
| ⭐⭐⭐ | 添加应用 | 表单创建新模板，必填项验证 |
| ⭐⭐⭐ | 编辑应用 | 修改现有模板，表单预填充 |
| ⭐⭐⭐ | 删除应用 | 带确认对话框的删除功能 |

### Step 3：对接 API 接口

API 接口定义详见 [api.md](./api.md)：

- `GET /api/v1/app-store` - 获取应用列表
- `GET /api/v1/app-store/:id` - 获取应用详情
- `POST /api/v1/app-store` - 创建应用
- `POST /api/v1/app-store/:id` - 更新应用
- `DELETE /api/v1/app-store/:id` - 删除应用

### Step 4：配置国际化

所有文本使用 `$t()` 函数包裹，语言包配置详见 [i18n.md](./i18n.md)。

### Step 5：遵循设计规范

- 使用项目统一配色（主色 #4fc3f7）
- 表格行悬停效果
- 官方徽章使用金色
- 类型标签使用不同颜色区分
- 状态徽章：绿色（活跃）/ 灰色（已弃用）
- 调用 `compact-page-style` skill 遵循紧凑风格

## 输出结果（What）

完成后的页面应具备：

1. **列表展示**：表格行布局，显示应用基本信息和操作按钮
2. **搜索筛选**：支持关键词搜索和多条件筛选
3. **详情查看**：弹窗展示完整应用信息
4. **增删改查**：完整的 CRUD 操作功能
5. **国际化支持**：中英文切换

## 参考文件

| 文件 | 说明 |
|------|------|
| [api.md](./api.md) | API 接口定义与返回数据示例 |
| [components.md](./components.md) | 组件实现代码参考 |
| [types.md](./types.md) | 类型定义参考 |
| [i18n.md](./i18n.md) | 国际化语言包配置 |

## 相关 Skill

- `compact-page-style` - 紧凑风格页面设计规范
- `component-reusability` - 可复用组件设计
- `vue-i18n-setup` - 国际化配置
