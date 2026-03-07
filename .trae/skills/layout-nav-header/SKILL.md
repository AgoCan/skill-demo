---
name: "layout-nav-header"
description: "当用户需要创建或修改 Squirrel 运维平台的布局导航栏、侧边栏、顶部栏时调用。包含导航菜单、Logo、用户信息、退出按钮等组件的设计规范和实现。"
---

# 布局导航栏和顶部栏设计

## 触发条件（When）

当用户满足以下任一条件时触发此 Skill：

- 需要创建新的布局组件或页面框架
- 需要修改侧边栏导航样式或结构
- 需要调整顶部栏布局或组件
- 需要更新导航菜单项配置
- 需要修改 Logo、用户信息或退出按钮样式
- 询问布局相关的样式规范或配色方案

## 执行步骤（How）

### 1. 确认需求范围

- 明确用户需要创建还是修改布局组件
- 确认需要调整的具体部分（侧边栏、顶部栏、导航菜单等）
- 了解是否需要调整样式、结构或功能

### 2. 参考设计规范

根据需求查阅相应的规范文件：

- **组件结构**：参见 [component-structure.md](./component-structure.md)
- **样式规范**：参见 [style-specifications.md](./style-specifications.md)
- **完整实现**：参见 [implementation.md](./implementation.md)

### 3. 实施修改

- 遵循 Squirrel 运维平台的设计语言
- 保持与现有布局组件的一致性
- 确保响应式布局和可访问性

### 4. 验证结果

- 检查样式是否符合规范
- 验证导航菜单功能正常
- 确认用户信息和退出按钮交互正确

## 输出结果（What）

根据具体需求，输出可能包含：

- **布局组件代码**：符合规范的 Vue 组件实现
- **样式调整方案**：具体的 CSS 样式修改建议
- **配置更新**：导航菜单项的配置代码
- **设计说明**：配色方案、尺寸规范等设计决策说明

## 核心设计原则

### 组件结构

```
main-layout
├── sidebar (侧边栏)
│   ├── logo (Logo 区域)
│   └── nav (导航菜单)
└── main-content (主内容区)
    ├── header (顶部栏)
    │   ├── header-left (页面标题)
    │   └── header-right (用户信息 + 退出按钮)
    └── content (页面内容)
```

### 关键配色

- **侧边栏背景**: `linear-gradient(180deg, #1e3a5f 0%, #0f1f33 100%)`
- **Logo 颜色**: `#4fc3f7`
- **导航激活色**: `linear-gradient(135deg, #4fc3f7 0%, #29b6f6 100%)`
- **页面标题**: `#1e3a5f`

### 核心尺寸

- **侧边栏宽度**: 200px
- **Logo 图标**: 24px × 24px
- **导航图标**: 16px × 16px
- **页面标题字号**: 18px

## 导航菜单项

```typescript
const navItems = [
  { path: '/dashboard', label: '概览', icon: 'lucide:layout-dashboard' },
  { path: '/servers', label: '服务器', icon: 'lucide:server' },
  { path: '/applications', label: '应用', icon: 'lucide:box' },
  { path: '/monitor', label: '监控', icon: 'lucide:activity' },
  { path: '/scripts', label: '脚本', icon: 'lucide:terminal' },
  { path: '/configs', label: '配置', icon: 'lucide:settings' },
  { path: '/deployments', label: '部署', icon: 'lucide:rocket' },
  { path: '/appstore', label: '应用商店', icon: 'lucide:shopping-bag' }
]
```

## 详细参考文档

- [组件结构详解](./component-structure.md) - 各组件的详细结构说明
- [样式规范](./style-specifications.md) - 完整的 CSS 样式规范
- [完整实现代码](./implementation.md) - 可直接使用的 Vue 组件代码
