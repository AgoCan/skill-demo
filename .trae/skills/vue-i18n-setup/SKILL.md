---
name: vue-i18n-setup
description: 当用户需要为 Vue3 项目添加国际化支持、创建多语言界面或实现语言切换功能时调用。包括语言包配置、切换组件实现和持久化存储。
---

# Vue3 + vue-i18n 国际化配置

为 Vue3 项目配置完整的国际化解决方案，包括语言包结构、切换组件和持久化存储。

## 触发条件（When）

当用户请求以下任务时触发此 Skill：

- 为 Vue3 项目添加国际化支持
- 创建多语言界面
- 实现语言切换功能
- 配置 vue-i18n
- 翻译界面文本

## 执行步骤（How）

### 配置工作流

在开始执行前复制以下清单，并在每一步完成后显式标记状态。

- Step 1：安装 vue-i18n@9 依赖
- Step 2：创建语言包目录结构（src/lang/）
- Step 3：配置 i18n 实例（src/lang/index.ts）
- Step 4：创建中英文语言包文件
- Step 5：在 main.ts 中引入 i18n
- Step 6：创建语言切换组件
- Step 7：验证语言切换功能是否正常工作；（反馈闭环）若切换失败，检查语言包路径和配置

### 核心配置要点

1. **目录结构**
   ```
   src/lang/
   ├── index.ts              # i18n 配置入口
   ├── zh-CN/                # 中文语言包
   │   ├── index.ts
   │   ├── common.ts
   │   └── ...
   └── en-US/                # 英文语言包
       ├── index.ts
       ├── common.ts
       └── ...
   ```

2. **i18n 配置**
   - 使用 `legacy: false` 模式（Composition API）
   - 从 localStorage 读取持久化的语言设置
   - 设置 fallbackLocale 为 'zh-CN'

3. **语言包组织**
   - 按功能模块划分：common、login、layout 等
   - 使用嵌套命名空间：`$t('layout.overview')`

4. **语言切换组件**
   - 显示当前语言名称
   - 支持下拉菜单选择
   - 切换后保存到 localStorage

## 输出结果（What）

完成配置后，应产出以下内容：

1. **i18n 配置文件**：src/lang/index.ts
2. **语言包文件**：zh-CN 和 en-US 目录及其内容
3. **语言切换组件**：可复用的切换器组件
4. **使用示例**：在组件中使用 $t() 的示例代码

## 参考文件

详细配置步骤和代码示例请参见：

- [setup-guide.md](./setup-guide.md) - 详细配置步骤
- [code-examples.md](./code-examples.md) - 完整代码示例

## 最佳实践

1. **按功能模块组织语言包**：login、overview、layout 等
2. **使用嵌套命名空间**：`$t('layout.overview')`、`$t('login.username')`
3. **持久化存储**：切换语言时保存到 localStorage
4. **动态计算**：导航菜单等使用 computed 包裹，语言切换自动更新
5. **fallbackLocale**：设置默认语言作为后备
