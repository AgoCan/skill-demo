---
name: "compact-page-style"
description: "Squirrel 紧凑风格页面设计规范。包含统一的字体、间距、圆角、阴影等样式标准。在创建或调整页面样式时调用。"
---

# 紧凑风格页面设计规范

## 概述

这是 Squirrel 运维平台的统一紧凑风格设计规范，适用于所有页面组件。

## 设计原则

- **紧凑**: 减小字体、间距、圆角，提高信息密度
- **一致**: 所有页面使用统一的样式标准
- **轻盈**: 使用浅色背景和柔和阴影
- **现代**: 渐变、圆角、微交互

## 字体规范

### 字号层级

| 用途 | 字号 | 行高 | 字重 |
|------|------|------|------|
| 页面标题 | 18px | - | 600 |
| 卡片标题 | 14px | - | 600 |
| 正文文本 | 13px | - | 500 |
| 小文本 | 12px | - | 500 |
| 极小文本 | 11px | - | 500 |

### 文本颜色

```css
/* 主标题 */
color: #1e3a5f;

/* 正文 */
color: #64748b;

/* 浅色文本 */
color: #94a3b8;

/* 白色文本 */
color: #ffffff;
```

## 间距规范

### 外边距 (margin/padding)

| 用途 | 值 |
|------|-----|
| 页面间距 | 16px |
| 卡片间距 | 12px |
| 卡片内边距 | 16px |
| 表单间距 | 12px |
| 表单组间距 | 6px |
| 模态框内边距 | 16px |
| 内容区内边距 | 20px |

### 间距层级

```css
/* 大间距 */
gap: 16px;

/* 中间距 */
gap: 12px;

/* 小间距 */
gap: 8px;

/* 极小间距 */
gap: 6px;
gap: 4px;
gap: 2px;
```

## 圆角规范

| 元素 | 圆角 |
|------|------|
| 页面卡片 | 8px |
| 按钮 | 6px |
| 输入框 | 6px |
| 模态框 | 8px |
| 标签/徽章 | 12px |
| 导航项 | 4px |
| 用户信息 | 12px |
| 退出按钮 | 6px |

## 图标尺寸

| 用途 | 尺寸 |
|------|------|
| 统计卡片图标 | 44px × 44px |
| 卡片图标 | 36px × 36px |
| 导航图标 | 16px × 16px |
| 用户图标 | 16px × 16px |
| 小图标 | 12px × 12px |
| 按钮图标 | 16px × 16px |
| 退出按钮 | 32px × 32px |

## 卡片样式

### 基础卡片

```css
.card {
  background: #ffffff;
  border-radius: 8px;
  padding: 16px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
  transition: all 0.3s ease;
}

.card:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.08);
}
```

### 统计卡片

```css
.stat-card {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 16px;
  background: #ffffff;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
  transition: all 0.3s ease;
}

.stat-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.08);
}

.stat-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 44px;
  height: 44px;
  border-radius: 8px;
  font-size: 20px;
}

.stat-value {
  font-size: 24px;
  font-weight: 700;
  color: #1e3a5f;
}

.stat-label {
  font-size: 12px;
  color: #64748b;
}
```

## 按钮样式

### 主按钮

```css
.btn-primary {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 8px 16px;
  font-size: 13px;
  font-weight: 500;
  color: #ffffff;
  background: linear-gradient(135deg, #4fc3f7 0%, #29b6f6 100%);
  border-radius: 6px;
  transition: all 0.3s ease;
  box-shadow: 0 2px 8px rgba(79, 195, 247, 0.4);
}

.btn-primary:hover {
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(79, 195, 247, 0.5);
}
```

### 次要按钮

```css
.btn-secondary {
  flex: 1;
  padding: 8px;
  font-size: 13px;
  font-weight: 500;
  color: #64748b;
  background: #f8fafc;
  border-radius: 6px;
  transition: all 0.3s ease;
}

.btn-secondary:hover {
  background: #f1f5f9;
  color: #1e3a5f;
}
```

### 图标按钮

```css
.btn-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 28px;
  height: 28px;
  border-radius: 6px;
  background: #f8fafc;
  color: #64748b;
  transition: all 0.3s ease;
}

.btn-icon:hover {
  background: #f1f5f9;
  color: #4fc3f7;
}

.btn-icon.danger:hover {
  background: #fee2e2;
  color: #dc2626;
}
```

## 表单样式

### 表单组

```css
.form-group {
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.form-group label {
  font-size: 12px;
  font-weight: 500;
  color: #1e3a5f;
}
```

### 输入框

```css
.form-group input,
.form-group select,
.form-group textarea {
  padding: 8px 12px;
  font-size: 13px;
  border: 2px solid #e2e8f0;
  border-radius: 6px;
  background: #f8fafc;
  color: #1e3a5f;
  transition: all 0.3s ease;
}

.form-group input:focus,
.form-group select:focus,
.form-group textarea:focus {
  outline: none;
  border-color: #4fc3f7;
  background: #ffffff;
}
```

### 表单行

```css
.form-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
}
```

## 状态徽章

```css
.status-badge {
  padding: 4px 10px;
  border-radius: 12px;
  font-size: 11px;
  font-weight: 500;
}

/* 在线状态 */
.status-badge.online {
  background: #dcfce7;
  color: #16a34a;
}

/* 离线状态 */
.status-badge.offline {
  background: #fee2e2;
  color: #dc2626;
}

/* 维护状态 */
.status-badge.maintenance {
  background: #fef3c7;
  color: #d97706;
}

/* 运行中 */
.status-badge.running {
  background: #dcfce7;
  color: #16a34a;
}

/* 已停止 */
.status-badge.stopped {
  background: #f1f5f9;
  color: #64748b;
}

/* 部署中 */
.status-badge.deploying {
  background: #e0f2fe;
  color: #0284c7;
}

/* 成功 */
.status-badge.success {
  background: #dcfce7;
  color: #16a34a;
}

/* 失败 */
.status-badge.failed {
  background: #fee2e2;
  color: #dc2626;
}
```

## 模态框样式

```css
.modal-overlay {
  position: fixed;
  inset: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(0, 0, 0, 0.5);
  z-index: 1000;
  padding: 16px;
}

.modal {
  width: 100%;
  max-width: 400px;
  background: #ffffff;
  border-radius: 8px;
  overflow: hidden;
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px 20px;
  border-bottom: 1px solid #f1f5f9;
}

.modal-header h3 {
  font-size: 14px;
  font-weight: 600;
  color: #1e3a5f;
}

.btn-close {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 28px;
  height: 28px;
  border-radius: 6px;
  background: #f8fafc;
  color: #64748b;
  transition: all 0.3s ease;
}

.btn-close:hover {
  background: #f1f5f9;
  color: #1e3a5f;
}

.modal-body {
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.modal-footer {
  display: flex;
  gap: 8px;
  padding-top: 12px;
  border-top: 1px solid #f1f5f9;
}
```

## 页面头部

```css
.page-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.header-left {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.page-title {
  font-size: 18px;
  font-weight: 600;
  color: #1e3a5f;
}

.page-subtitle {
  font-size: 12px;
  color: #64748b;
}

.header-actions {
  display: flex;
  gap: 8px;
}
```

## 网格布局

```css
/* 统计卡片网格 */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 12px;
}

/* 卡片网格 */
.card-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 12px;
}

/* 响应式卡片网格 */
.cards-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 12px;
}
```

## 进度条

```css
.progress-bar {
  height: 6px;
  background: #f1f5f9;
  border-radius: 3px;
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #4fc3f7 0%, #29b6f6 100%);
  border-radius: 3px;
  transition: width 0.5s ease;
}

.progress-text {
  font-size: 11px;
  font-weight: 500;
  color: #4fc3f7;
  text-align: right;
}
```

## 资源条

```css
.resource-bar {
  flex: 1;
  height: 4px;
  background: #f1f5f9;
  border-radius: 2px;
  overflow: hidden;
}

.bar-fill {
  height: 100%;
  background: linear-gradient(90deg, #4fc3f7 0%, #29b6f6 100%);
  border-radius: 2px;
  transition: width 0.5s ease;
}

.resource-value {
  width: 32px;
  font-size: 11px;
  font-weight: 500;
  color: #1e3a5f;
  text-align: right;
}
```

## 配色方案

### 主色调

```css
/* 主蓝色 */
--primary: #4fc3f7;
--primary-dark: #29b6f6;

/* 绿色 */
--success: #16a34a;
--success-bg: #dcfce7;

/* 红色 */
--danger: #dc2626;
--danger-bg: #fee2e2;

/* 橙色 */
--warning: #d97706;
--warning-bg: #fef3c7;

/* 灰色 */
--gray-100: #f8fafc;
--gray-200: #f1f5f9;
--gray-300: #e2e8f0;
--gray-400: #94a3b8;
--gray-500: #64748b;
--gray-600: #475569;
--gray-700: #1e3a5f;
```

### 渐变色

```css
/* 主渐变 */
background: linear-gradient(135deg, #4fc3f7 0%, #29b6f6 100%);

/* 侧边栏渐变 */
background: linear-gradient(180deg, #1e3a5f 0%, #0f1f33 100%);
```

## 过渡动画

```css
/* 标准过渡 */
transition: all 0.3s ease;

/* 快速过渡 */
transition: all 0.2s ease;

/* 缓动效果 */
transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
```

## 阴影规范

```css
/* 卡片阴影 */
box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);

/* 卡片悬停阴影 */
box-shadow: 0 4px 16px rgba(0, 0, 0, 0.08);

/* 按钮阴影 */
box-shadow: 0 2px 8px rgba(79, 195, 247, 0.4);

/* 按钮悬停阴影 */
box-shadow: 0 4px 12px rgba(79, 195, 247, 0.5);

/* 侧边栏阴影 */
box-shadow: 4px 0 12px rgba(0, 0, 0, 0.1);
```

## 代码片段

### 卡片列表项

```vue
<div class="card-item">
  <div class="card-header">
    <div class="card-icon">
      <Icon icon="lucide:box" />
    </div>
    <div class="status-badge running">运行中</div>
  </div>
  <div class="card-body">
    <h3 class="card-title">应用名称</h3>
    <p class="card-desc">应用描述</p>
    <div class="card-meta">
      <div class="meta-item">
        <Icon icon="lucide:server" />
        <span>服务器名称</span>
      </div>
    </div>
  </div>
  <div class="card-footer">
    <button class="btn-icon">
      <Icon icon="lucide:pencil" />
    </button>
    <button class="btn-icon">
      <Icon icon="lucide:settings" />
    </button>
    <button class="btn-icon danger">
      <Icon icon="lucide:trash-2" />
    </button>
  </div>
</div>
```

### 表单输入

```vue
<div class="form-group">
  <label>标签文本</label>
  <input type="text" placeholder="占位符" />
</div>
```

## 使用说明

### 适用页面

- Dashboard（仪表盘）
- Servers（服务器管理）
- Applications（应用管理）
- Monitor（监控）
- Scripts（脚本管理）
- Configs（配置管理）
- Deployments（部署管理）
- AppStore（应用商店）

### 使用场景

- 创建新页面时参考此规范
- 调整现有页面样式时保持一致
- 添加新组件时遵循统一风格
