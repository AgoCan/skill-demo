---
name: "error-code-i18n"
description: "当用户需要添加错误码翻译、创建错误消息处理或实现错误码到消息映射时调用。提供前端错误码国际化配置方案。"
---

# 前端错误码国际化配置

## 触发条件（When）

- 用户需要添加新的错误码翻译
- 用户需要创建错误消息处理机制
- 用户需要实现错误码到多语言消息的映射
- 用户需要了解错误码范围和模块对应关系

## 执行步骤（How）

1. 确认错误码所属模块（参考错误码范围表）
2. 在对应语言包中添加错误消息
3. 更新错误码映射配置
4. 在组件或拦截器中使用错误处理工具

## 输出结果（What）

- 完整的错误消息语言包
- 错误处理工具函数
- API 拦截器配置示例

---

## 错误码范围

| 模块 | 错误码范围 |
|------|-----------|
| Server | 60000-60059 |
| Config | 65000-65019 |
| Auth | 66000-66019 |
| Application | 71000-71019 |
| Deployment | 72000-72039 |
| AppStore | 73000-73019 |
| Script | 80000-80039 |
| Monitor | 81000-81019 |

## 目录结构

```
src/lang/
├── zh-CN/
│   └── error.ts        # 中文错误消息
└── en-US/
    └── error.ts        # 英文错误消息
```

## 详细参考

- [错误码速查表](error-codes.md) - 完整错误码列表与说明
- [语言包配置](language-pack.md) - 中英文错误消息模板
- [错误处理工具](error-handler.md) - errorHandler.ts 与拦截器配置
- [使用示例](usage-examples.md) - 组件中使用示例与 useErrorHandler

## 快速使用

### 1. 获取错误消息

```typescript
import { getErrorMessage } from '@/utils/errorHandler'

const message = getErrorMessage({ code: 60001 })
// 中文: '服务器未找到'
// 英文: 'Server not found'
```

### 2. 在组件中处理错误

```typescript
import { useErrorHandler } from '@/composables/useErrorHandler'

const { handleError } = useErrorHandler()

try {
  await apiCall()
} catch (err) {
  handleError(err)  // 自动显示国际化错误消息
}
```

## 最佳实践

1. **集中管理**：所有错误码映射集中在一处
2. **自动降级**：无翻译时返回后端原始消息
3. **统一处理**：通过 axios 拦截器统一处理
4. **类型安全**：使用 TypeScript 定义 ApiError 接口
