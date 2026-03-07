# 配色方案

## 主色调

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

## 渐变色

```css
/* 主渐变 */
background: linear-gradient(135deg, #4fc3f7 0%, #29b6f6 100%);

/* 侧边栏渐变 */
background: linear-gradient(180deg, #1e3a5f 0%, #0f1f33 100%);
```

## 状态徽章颜色

```css
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
