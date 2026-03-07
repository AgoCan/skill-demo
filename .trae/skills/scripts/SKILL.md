---
name: scripts
description: 当用户需要开发 Squirrel 脚本管理页面、实现脚本列表、脚本编辑器、执行记录等功能时调用。包含组件实现、API 接口和国际化配置。
---

# Squirrel 脚本管理页面开发规范

脚本管理页面提供对系统脚本的增删改查和执行功能，包括脚本列表展示、脚本编辑器、脚本执行、执行记录查看等功能。

## 触发条件（When）

当用户请求以下任务时触发此 Skill：

- 开发脚本管理页面
- 实现脚本列表展示
- 创建脚本编辑器
- 实现脚本执行功能
- 查看执行记录
- 对接脚本管理 API

## 执行步骤（How）

### 开发工作流

在开始执行前复制以下清单，并在每一步完成后显式标记状态。

- Step 1：创建目录结构（views/Scripts/）
- Step 2：定义类型（types/index.ts）
- Step 3：实现 API 接口（api/script.ts）
- Step 4：创建脚本表格组件（ScriptTable.vue）
- Step 5：创建脚本编辑器组件（ScriptEditor.vue）
- Step 6：创建执行对话框组件（ExecuteDialog.vue）
- Step 7：创建执行结果组件（ResultList.vue）
- Step 8：实现主页面逻辑（index.vue）
- Step 9：添加国际化配置（lang/zh-CN/scripts.ts）
- Step 10：配置路由；（反馈闭环）验证所有功能是否正常工作

### 核心开发要点

1. **目录结构**
   ```
   views/Scripts/
   ├── index.vue                 # 页面入口
   ├── components/
   │   ├── ScriptTable.vue       # 脚本列表表格
   │   ├── ScriptEditor.vue      # 脚本编辑器
   │   ├── ExecuteDialog.vue     # 执行脚本对话框
   │   └── ResultList.vue        # 执行结果列表
   ├── composables/
   │   └── useScript.ts          # 脚本相关的可组合函数
   ├── types/
   │   └── index.ts              # 页面类型定义
   └── styles/
       └── scripts.scss          # 脚本相关组件样式
   ```

2. **组件实现要点**
   - ScriptTable：使用表格行布局，显示脚本名称和操作按钮
   - ScriptEditor：支持添加和编辑两种模式，代码编辑器支持语法高亮
   - ExecuteDialog：选择服务器执行脚本
   - ResultList：显示执行结果，状态标签使用不同颜色

3. **API 接口**
   - GET /api/v1/scripts - 获取脚本列表
   - GET /api/v1/scripts/{id} - 获取脚本详情
   - POST /api/v1/scripts - 创建脚本
   - POST /api/v1/scripts/{id} - 更新脚本
   - DELETE /api/v1/scripts/{id} - 删除脚本
   - POST /api/v1/scripts/execute - 执行脚本
   - GET /api/v1/scripts/{id}/results - 获取执行结果

4. **注意事项**
   - 点击添加新内容时，应该和编辑模式类似，避免页面不可操作
   - 脚本内容必须以 shebang (#!) 开头
   - 使用项目统一的配色方案（主色 #4fc3f7）
   - 所有文本使用 $t() 函数包裹

## 输出结果（What）

完成开发后，应产出以下内容：

1. **类型定义**：Script、CreateScriptRequest、UpdateScriptRequest 等
2. **API 接口**：fetchScripts、createScript、updateScript、deleteScript 等
3. **组件文件**：ScriptTable、ScriptEditor、ExecuteDialog、ResultList
4. **主页面**：index.vue 包含所有逻辑
5. **国际化配置**：lang/zh-CN/scripts.ts

## 参考文件

详细参考信息请参见：

- [api.md](./api.md) - API 接口示例数据
- [types-reference.md](./types-reference.md) - 类型定义
- [i18n-reference.md](./i18n-reference.md) - 国际化配置
- [components-guide.md](./components-guide.md) - 组件实现指南

## 快速参考

### 路由配置

```typescript
{
  path: '/scripts',
  name: 'Scripts',
  component: () => import('@/views/Scripts/index.vue')
}
```

### 主要功能

- 脚本列表展示
- 添加/编辑脚本
- 删除脚本
- 执行脚本
- 查看执行结果
- 模糊搜索

### 功能特性优先级

1. ⭐⭐⭐⭐ 模糊搜索：实时过滤脚本列表
2. ⭐⭐⭐⭐ 脚本编辑器：支持语法高亮
3. ⭐⭐⭐⭐ 执行脚本：选择服务器执行
4. ⭐⭐⭐⭐ 执行记录：查看历史记录
5. ⭐⭐⭐ 添加脚本：创建新脚本
6. ⭐⭐⭐ 编辑脚本：修改现有脚本
7. ⭐⭐⭐ 删除脚本：带确认对话框

## 设计规范

- 使用项目统一的配色方案（主色 #4fc3f7）
- 表格行悬停效果
- 操作按钮使用图标按钮
- 弹窗使用圆角卡片设计
- 表单分组展示（基本信息）
- 遵循 Squirrel 紧凑风格页面设计规范

## 最佳实践

1. **列表布局**：使用表格行布局，不是方块卡片
2. **状态管理**：使用响应式数据管理弹窗显示状态
3. **错误处理**：API 调用添加 try-catch 处理
4. **国际化**：所有文本使用 $t() 函数包裹
5. **表单验证**：脚本名称和脚本内容都为必填项，脚本内容必须以 shebang 开头
6. **搜索优化**：使用 computed 实现实时过滤，避免不必要的重新渲染
7. **代码编辑器**：使用专业的代码编辑器组件，支持语法高亮

## 架构参考

- 遵循 Squirrel 运维平台前端布局架构（调用 `layout` skill）
- 使用 Vue3 + vue-i18n 国际化（调用 `vue-i18n-setup` skill）
- 参考 `component-reusability` skill 设计可复用组件
