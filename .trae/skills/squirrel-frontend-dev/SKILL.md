---
name: squirrel-frontend-dev
description: 当用户需要开发 Squirrel 运维平台前端功能、创建新页面、实现 API 对接或遵循项目设计规范时调用。提供 API 接口、设计规则、页面功能的完整参考。
---

# Squirrel 运维平台前端开发技能

为 Squirrel API Server 构建前端界面，使用 Vue3 + TypeScript + Vite 技术栈，遵循反主流设计美学的现代化运维平台。

## 触发条件（When）

当用户请求以下任务时触发此 Skill：

- 开发 Squirrel 运维平台前端功能
- 创建新页面或组件
- 实现 API 接口对接
- 遵循项目设计规范
- 配置路由和布局

## 执行步骤（How）

### 开发工作流

在开始执行前复制以下清单，并在每一步完成后显式标记状态。

- Step 1：明确需求，确定要开发的页面或功能
- Step 2：查阅 API 接口文档，了解后端接口定义
- Step 3：遵循设计规则，设计页面布局和样式
- Step 4：创建页面组件，实现基础功能
- Step 5：对接 API 接口，实现数据交互
- Step 6：添加路由配置，确保导航正常
- Step 7：测试验证功能是否正常；（反馈闭环）若发现问题，返回相应步骤修正

### 核心开发要点

1. **技术栈**
   - Vue3 + TypeScript + Vite
   - 使用 history 模式路由
   - 编写代码目录：front

2. **路由配置**
   - dev 调试：请求 192.168.37.20:10700/api/v1
   - 生产环境：请求自身端口 /api/v1
   - 除了 login，都需要先登录，否则返回 /login 页面

3. **布局要求**
   - 必须有导航栏和侧边栏
   - 所有页面都必须有浅色背景
   - 蓝色系列是运维平台的现代化颜色

4. **设计原则**
   - 追求每个像素都有温度
   - 鄙视千篇一律的模板
   - 反主流的网页美学

## 输出结果（What）

完成开发后，应产出以下内容：

1. **页面组件**：符合设计规范的 Vue 组件
2. **API 对接**：正确调用后端接口
3. **路由配置**：正确的路由设置
4. **样式实现**：遵循设计规则的样式

## 参考文件

详细参考信息请参见：

- [api-reference.md](./api-reference.md) - API 接口文档
- [design-guidelines.md](./design-guidelines.md) - 设计规则和规范
- [page-features.md](./page-features.md) - 页面功能清单

## 快速参考

### 登录信息

- 登录默认账号密码：demo/squ123

### 图标资源

- 图标：使用 Iconify 图标库（https://iconify.design）
- 占位图：使用 Picsum Photos（https://picsum.photos）
- 真实图片：使用 Pexels 搜索（https://www.pexels.com）
- 插画：使用 unDraw（https://undraw.co）

## 注意事项

- 不要使用终端的方式创建目录内容
- 始终记住：追求每个像素都有温度，鄙视千篇一律的模板！
