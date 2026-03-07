# 登录页面设计规范

## 目录

- [配色方案](#配色方案)
- [布局结构](#布局结构)
- [左侧品牌展示区](#左侧品牌展示区)
- [右侧登录表单区](#右侧登录表单区)
- [表单元素规范](#表单元素规范)
- [Logo SVG](#logo-svg)

## 配色方案

### 主色板

| 名称 | 色值 | 用途 |
|------|------|------|
| 背景渐变起点 | `#1e3a5f` | 深蓝色，用于背景渐变起点 |
| 背景渐变终点 | `#0f1f33` | 更深的蓝色，用于背景渐变终点 |
| 主色调 | `#4fc3f7` | 天蓝色，用于强调元素、按钮、图标 |
| 白色 | `#ffffff` | 用于表单区域背景 |

### 文本色

| 名称 | 色值 | 用途 |
|------|------|------|
| 主标题 | `#1e3a5f` | 登录标题、表单标签 |
| 副标题 | `#64748b` | 副标题、提示文字 |
| 浅色文本 | `rgba(255, 255, 255, 0.6)` | 左侧区域副标题 |
| 高亮文本 | `rgba(255, 255, 255, 0.8)` | 左侧区域特性文字 |

### 功能色

| 名称 | 色值 | 用途 |
|------|------|------|
| 边框色 | `#e2e8f0` | 输入框默认边框 |
| 输入框背景 | `#f8fafc` | 输入框默认背景 |
| 图标色 | `#94a3b8` | 输入框内图标 |
| 聚焦色 | `#4fc3f7` | 输入框聚焦边框 |

## 布局结构

### 容器尺寸

```css
.login-container {
  width: 100%;
  max-width: 1000px;
  height: 600px;
  border-radius: 24px;
  box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
}
```

### 分屏比例

- 左侧品牌展示区: `flex: 1`
- 右侧登录表单区: `flex: 1`

## 左侧品牌展示区

### 背景效果

```css
.login-left {
  background: linear-gradient(135deg, #1e3a5f 0%, #0f1f33 100%);
  position: relative;
  overflow: hidden;
}

.login-left::before {
  content: '';
  position: absolute;
  width: 300px;
  height: 300px;
  background: rgba(79, 195, 247, 0.1);
  border-radius: 50%;
  top: -100px;
  right: -100px;
}

.login-left::after {
  content: '';
  position: absolute;
  width: 200px;
  height: 200px;
  background: rgba(79, 195, 247, 0.05);
  border-radius: 50%;
  bottom: -50px;
  left: -50px;
}
```

### 品牌元素

| 元素 | 规格 |
|------|------|
| Logo 尺寸 | `64px × 64px` |
| Logo 颜色 | `#4fc3f7` |
| 标题字号 | `36px` |
| 标题字重 | `700` |
| 副标题字号 | `16px` |

### 特性列表

- 3 个特性项
- 每项包含图标 (`20px × 20px`) 和文字
- 图标颜色: `#4fc3f7`
- 文字颜色: `rgba(255, 255, 255, 0.8)`
- 间距: `gap: 20px`

## 右侧登录表单区

### 布局参数

```css
.login-right {
  background: #ffffff;
  padding: 40px;
}

.login-form-wrapper {
  max-width: 360px;
}
```

### 标题样式

| 元素 | 字号 | 字重 | 颜色 |
|------|------|------|------|
| 主标题 | `28px` | `700` | `#1e3a5f` |
| 副标题 | `14px` | `400` | `#64748b` |

## 表单元素规范

### 输入框

```css
.form-input {
  width: 100%;
  padding: 14px 14px 14px 44px;
  font-size: 14px;
  border: 2px solid #e2e8f0;
  border-radius: 12px;
  background: #f8fafc;
  color: #1e3a5f;
}

.form-input:focus {
  outline: none;
  border-color: #4fc3f7;
  background: #ffffff;
  box-shadow: 0 0 0 4px rgba(79, 195, 247, 0.1);
}
```

### 输入框图标

```css
.input-icon {
  position: absolute;
  left: 14px;
  width: 20px;
  height: 20px;
  color: #94a3b8;
}
```

### 登录按钮

```css
.login-btn {
  padding: 14px;
  font-size: 16px;
  font-weight: 600;
  color: #ffffff;
  background: linear-gradient(135deg, #4fc3f7 0%, #29b6f6 100%);
  border-radius: 12px;
  box-shadow: 0 4px 12px rgba(79, 195, 247, 0.4);
}

.login-btn:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: 0 6px 16px rgba(79, 195, 247, 0.5);
}
```

### 复选框

```css
.checkbox-label input {
  width: 16px;
  height: 16px;
  accent-color: #4fc3f7;
}
```

## Logo SVG

```svg
<g fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2">
  <path d="M15.236 22a3 3 0 0 0-2.2-5"></path>
  <path d="M16 20a3 3 0 0 1 3-3h1a2 2 0 0 0 2-2v-2a4 4 0 0 0-4-4V4m0 9h.01"></path>
  <path d="M18 6a4 4 0 0 0-4 4a7 7 0 0 0-7 7c0-5 4-5 4-10.5a4.5 4.5 0 1 0-9 0a2.5 2.5 0 0 0 5 0C7 10 3 11 3 17c0 2.8 2.2 5 5 5h10"></path>
</g>
```

使用 Iconify 图标库: `lucide:squirrel`
