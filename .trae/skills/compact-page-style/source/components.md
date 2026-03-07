# 组件样式

## 目录

- [卡片样式](#卡片样式)
- [按钮样式](#按钮样式)
- [表单样式](#表单样式)
- [状态徽章](#状态徽章)
- [模态框样式](#模态框样式)
- [页面头部](#页面头部)
- [网格布局](#网格布局)
- [进度条](#进度条)
- [资源条](#资源条)

---

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

---

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

---

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

---

## 状态徽章

```css
.status-badge {
  padding: 4px 10px;
  border-radius: 12px;
  font-size: 11px;
  font-weight: 500;
}
```

---

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

---

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

---

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

---

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

---

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

---

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
