# 间距规范

## 外边距 (margin/padding)

| 用途 | 值 |
|------|-----|
| 页面间距 | 16px |
| 卡片间距 | 12px |
| 卡片内边距 | 16px |
| 表单间距 | 12px |
| 表单组间距 | 6px |
| 模态框内边距 | 16px |
| 内容区内边距 | 20px |

## 间距层级

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

## 过渡动画

```css
/* 标准过渡 */
transition: all 0.3s ease;

/* 快速过渡 */
transition: all 0.2s ease;

/* 缓动效果 */
transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
```
