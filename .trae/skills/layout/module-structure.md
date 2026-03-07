# 模块结构说明

## 目录

- [布局模块结构](#布局模块结构)
- [Views 模块结构](#views-模块结构)
- [Lang 模块结构](#lang-模块结构)
- [Styles 模块结构](#styles-模块结构)

## 布局模块结构

```
layout/
├── index.ts                # 布局模块入口
├── components/             # 布局组件
│   ├── Navbar/            # 导航栏
│   ├── Sidebar/           # 侧边栏
│   ├── MainContent/       # 主内容区
│   └── Footer/            # 页脚
├── composables/           # 布局相关组合式函数
├── hooks/                 # 布局相关钩子
└── types/                 # 布局类型定义
```

## Views 模块结构

采用多组件模式，每个页面独立目录：

```
views/
├── Login/                 # 登录页
│   ├── index.vue          # 页面入口
│   ├── components/        # 页面内部组件
│   │   ├── LoginForm.vue  # 登录表单
│   │   └── LoginFooter.vue # 登录页脚
│   ├── composables/       # 页面相关组合式函数
│   └── types/             # 页面类型定义
├── Server/                # 服务器管理
│   ├── index.vue          # 页面入口
│   ├── components/        # 页面内部组件
│   │   ├── ServerList.vue # 服务器列表
│   │   ├── ServerForm.vue # 服务器表单
│   │   └── ServerStatus.vue # 服务器状态
│   ├── composables/       # 页面相关组合式函数
│   └── types/             # 页面类型定义
├── Application/           # 应用管理
│   ├── index.vue          # 页面入口
│   ├── components/        # 页面内部组件
│   ├── composables/       # 页面相关组合式函数
│   └── types/             # 页面类型定义
└── Monitor/               # 监控页面
    ├── index.vue          # 页面入口
    ├── components/        # 页面内部组件
    ├── composables/       # 页面相关组合式函数
    └── types/             # 页面类型定义
```

## Lang 模块结构

```
lang/
├── index.ts                # 语言模块入口
├── zh-CN/                  # 中文语言包
│   ├── common.ts           # 通用文本
│   ├── layout.ts           # 布局文本
│   ├── login.ts            # 登录页文本
│   ├── server.ts           # 服务器管理文本
│   └── application.ts      # 应用管理文本
├── en-US/                  # 英文语言包
│   ├── common.ts           # 通用文本
│   ├── layout.ts           # 布局文本
│   ├── login.ts            # 登录页文本
│   ├── server.ts           # 服务器管理文本
│   └── application.ts      # 应用管理文本
└── i18n.ts                 # 国际化配置
```

## Styles 模块结构

```
styles/
├── index.scss              # 样式入口文件
├── variables.scss          # 全局变量
├── mixins.scss             # 混合器
├── functions.scss          # 函数
├── reset.scss              # 重置样式
├── base.scss               # 基础样式
├── layout.scss             # 布局样式
├── components.scss         # 组件样式
├── utils.scss              # 工具类
└── themes/                 # 主题样式
    ├── default.scss        # 默认主题
    └── dark.scss           # 深色主题
```
