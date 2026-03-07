# 样式规范

## 目录

- [配色方案](#配色方案)
- [尺寸规范](#尺寸规范)
- [字体规范](#字体规范)
- [间距规范](#间距规范)
- [阴影与圆角](#阴影与圆角)
- [过渡动画](#过渡动画)
- [完整 CSS 样式](#完整-css-样式)

## 配色方案

### 主色调

| 用途 | 颜色值 | 使用场景 |
|------|--------|----------|
| 侧边栏背景 | `linear-gradient(180deg, #1e3a5f 0%, #0f1f33 100%)` | 侧边栏整体背景 |
| Logo 颜色 | `#4fc3f7` | Logo 图标、激活状态图标 |
| 导航激活背景 | `linear-gradient(135deg, #4fc3f7 0%, #29b6f6 100%)` | 激活的导航项背景 |
| 页面标题 | `#1e3a5f` | 顶部栏页面标题文字 |

### 辅助色

| 用途 | 颜色值 | 使用场景 |
|------|--------|----------|
| 导航默认文字 | `rgba(255, 255, 255, 0.6)` | 未激活的导航项文字 |
| 导航悬停背景 | `rgba(79, 195, 247, 0.15)` | 导航项悬停背景 |
| 用户信息背景 | `#f5f7fa` | 用户信息区域背景 |
| 退出按钮悬停背景 | `#fee2e2` | 退出按钮悬停背景 |
| 退出按钮悬停文字 | `#ef4444` | 退出按钮悬停文字颜色 |
| 边框颜色 | `#e8ecf1` | 顶部栏底部边框 |
| 主背景色 | `#f5f7fa` | 页面整体背景 |

## 尺寸规范

### 侧边栏

| 元素 | 尺寸 | 说明 |
|------|------|------|
| 侧边栏宽度 | `200px` | 固定宽度 |
| Logo 图标 | `24px × 24px` | Logo 区域图标 |
| 导航图标 | `16px × 16px` | 导航项图标 |
| 导航项高度 | `约 34px` | padding 10px 12px |
| 导航项圆角 | `4px` | 导航项圆角 |

### 顶部栏

| 元素 | 尺寸 | 说明 |
|------|------|------|
| Header padding | `10px 10px` | 顶部栏内边距 |
| 页面标题字号 | `18px` | 页面标题文字大小 |
| 用户图标 | `16px × 16px` | 用户信息图标 |
| 退出按钮 | `32px × 32px` | 退出按钮尺寸 |
| 退出按钮圆角 | `6px` | 退出按钮圆角 |

### 主内容区

| 元素 | 尺寸 | 说明 |
|------|------|------|
| 内容区内边距 | `20px` | 主内容区内边距 |

## 字体规范

### 字重

| 用途 | 字重 | 使用场景 |
|------|------|----------|
| Logo 文字 | `700` | Logo 区域文字 |
| 导航文字 | `500` | 导航项文字 |
| 页面标题 | `600` | 顶部栏标题 |
| 用户名 | `500` | 用户信息区域 |

### 字号

| 用途 | 字号 | 使用场景 |
|------|------|----------|
| Logo 文字 | `16px` | Logo 区域文字 |
| 导航文字 | `13px` | 导航项文字 |
| 页面标题 | `18px` | 顶部栏标题 |
| 用户名 | `13px` | 用户信息区域 |

### 字间距

| 用途 | 字间距 | 使用场景 |
|------|--------|----------|
| Logo 文字 | `0.5px` | Logo 区域文字 |

## 间距规范

### 侧边栏

| 元素 | 间距 | 说明 |
|------|------|------|
| 侧边栏内边距 | `16px 0` | 侧边栏上下内边距 |
| Logo 区域内边距 | `0 16px` | Logo 区域左右内边距 |
| Logo 下边距 | `20px` | Logo 与导航的间距 |
| 导航区域内边距 | `0 8px` | 导航区域左右内边距 |
| 导航项间距 | `2px` | 导航项之间的间距 |
| 导航项内边距 | `10px 12px` | 导航项内边距 |
| 图标与文字间距 | `8px` | 导航图标与文字间距 |

### 顶部栏

| 元素 | 间距 | 说明 |
|------|------|------|
| Header 左右间距 | `10px` | Header 左右内边距 |
| 用户信息内边距 | `6px 12px` | 用户信息区域内边距 |
| 用户图标与文字间距 | `6px` | 用户图标与文字间距 |
| Header 右侧元素间距 | `12px` | 用户信息与退出按钮间距 |

## 阴影与圆角

### 阴影

| 元素 | 阴影值 | 使用场景 |
|------|--------|----------|
| 侧边栏 | `4px 0 12px rgba(0, 0, 0, 0.1)` | 侧边栏右侧阴影 |
| 激活导航项 | `0 2px 8px rgba(79, 195, 247, 0.4)` | 激活导航项阴影 |

### 圆角

| 元素 | 圆角值 | 使用场景 |
|------|--------|----------|
| 导航项 | `4px` | 导航项圆角 |
| 用户信息区域 | `12px` | 用户信息背景圆角 |
| 退出按钮 | `6px` | 退出按钮圆角 |

## 过渡动画

### 过渡时长

| 用途 | 时长 | 使用场景 |
|------|------|----------|
| 所有过渡 | `0.3s` | 通用过渡时长 |

### 缓动函数

| 用途 | 缓动函数 | 使用场景 |
|------|----------|----------|
| 导航项 | `cubic-bezier(0.4, 0, 0.2, 1)` | 导航项过渡效果 |
| 其他元素 | `ease` | 其他元素过渡效果 |

### 交互效果

| 元素 | 效果 | 说明 |
|------|------|------|
| 导航项悬停 | `transform: translateX(2px)` | 右移 2px |
| 退出按钮悬停 | `transform: rotate(-5deg)` | 旋转 -5 度 |

## 完整 CSS 样式

### 侧边栏样式

```css
.sidebar {
  width: 200px;
  background: linear-gradient(180deg, #1e3a5f 0%, #0f1f33 100%);
  display: flex;
  flex-direction: column;
  padding: 16px 0;
  box-shadow: 4px 0 12px rgba(0, 0, 0, 0.1);
}

.logo {
  display: flex;
  align-items: center;
  padding: 0 16px;
  margin-bottom: 20px;
}

.logo-icon {
  width: 24px;
  height: 24px;
  color: #4fc3f7;
}

.logo-text {
  margin-left: 8px;
  font-size: 16px;
  font-weight: 700;
  color: #ffffff;
  letter-spacing: 0.5px;
}

.nav {
  flex: 1;
  display: flex;
  flex-direction: column;
  padding: 0 8px;
}

.nav-item {
  display: flex;
  align-items: center;
  padding: 10px 12px;
  margin-bottom: 2px;
  border-radius: 4px;
  color: rgba(255, 255, 255, 0.6);
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  font-size: 13px;
  font-weight: 500;
}

.nav-item:hover {
  background: rgba(79, 195, 247, 0.15);
  color: #4fc3f7;
  transform: translateX(2px);
}

.nav-item.active {
  background: linear-gradient(135deg, #4fc3f7 0%, #29b6f6 100%);
  color: #ffffff;
  box-shadow: 0 2px 8px rgba(79, 195, 247, 0.4);
}

.nav-icon {
  width: 16px;
  height: 16px;
  margin-right: 8px;
}
```

### 顶部栏样式

```css
.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px 10px;
  background: #ffffff;
  border-bottom: 1px solid #e8ecf1;
}

.header-left {
  display: flex;
  align-items: center;
}

.page-title {
  font-size: 18px;
  font-weight: 600;
  color: #1e3a5f;
}

.header-right {
  display: flex;
  align-items: center;
  gap: 12px;
}

.user-info {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 6px 12px;
  background: #f5f7fa;
  border-radius: 12px;
}

.user-icon {
  width: 16px;
  height: 16px;
  color: #4fc3f7;
}

.user-name {
  font-size: 13px;
  font-weight: 500;
  color: #1e3a5f;
}

.logout-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 32px;
  height: 32px;
  border-radius: 6px;
  background: #f5f7fa;
  color: #64748b;
  transition: all 0.3s ease;
}

.logout-btn:hover {
  background: #fee2e2;
  color: #ef4444;
  transform: rotate(-5deg);
}
```

### 主内容区样式

```css
.main-layout {
  display: flex;
  width: 100%;
  height: 100vh;
  background: #f5f7fa;
}

.main-content {
  flex: 1;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.content {
  flex: 1;
  padding: 20px;
  overflow-y: auto;
}
```
