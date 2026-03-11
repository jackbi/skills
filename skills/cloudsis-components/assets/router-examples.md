---
title: cloudsis项目路由使用规范
description: cloudsis项目路由使用规范，基于@magustek/framework-core，@magustek/framework-wujie实现
---
## 项目结构

```
src/
├── api/                 # API 请求
├── components/          # 通用组件
├── common
│   ├── authCode         # 授权编码对照表
│   ├── charts           # echarts Json
│   ├── dicts            # 系统字典对照表
│   ├── pinia            # Pinia stores
│   └── websocket        # 实时请求
├── lang                 # 国际化
│   ├── index.ts
│   └── locale           # 国际化配置文件
├── composables/         # 组合式函数
├── router/              # 路由配置
├── utils/               # 自定义工具函数
├── types/               # TypeScript 类型
├── utils/               # 工具函数
├── mock/                # 本地测试数据
├── views/               # 页面组件
├── App.vue
└── main.ts
```

### 路由创建

```ts
// src/router/index.ts

import { createRouter } from '@magustek/framework-core'
import { createMainRouter } from '@magustek/framework-wujie'

const routes = [
  {
    path: '/rules',
    component: () => import('@/views/rules/index.vue'),
    name: 'rules',
    meta: { title: '规章条例' },
    children: [
      {
        path: 'page',
        name: 'rulesPage',
        component: () => import('@/views/rules/page.vue'),
      },
    ],
  },
]

const router = createRouter({
  base: import.meta.env.BASE_URL,
  routes: routes,
})

const mainRouter = createMainRouter(router)

export { router, mainRouter }
```

### 入口文件导入

```ts
// src/main.ts
import { router, mainRouter } from './router'

const app = createApp(App)
app.use(router).use(mainRouter)
```

### 本地mock菜单使用创建好的路由

```ts
// src/mock/menu.mock.ts

export const fatherMenu = {
  name: '测试',
}

export const sonMenu = [
  { path: '/rules/page', name: '规章条例' },
]
```