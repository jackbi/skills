---
title: api-examples
description: cloudsis 项目api接口定义案例
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


## 接口定义

```ts
// src/api/**.api.ts

import type { HandsonTableResponseDataType } from '@/types/index.type'

import { useAxios } from '@magustek/framework-core'

const xxxCenterApi = import.meta.env.VITE_APP_RAT_CENTER_API

const { get } = useAxios()

// ✅使用ts类型定义后端接口返回数据类型
export const getFullTimeDataDetail = (data: {
  time: string
  unitCode: string
  major: string
  indicatorName: string
}) => get<HandsonTableResponseDataType>(`${ratCenterApi}/fullTime/dataDetail`, data)

// ❌不要使用any定义
export const getFullTimeDataDetail = (data: {
  time: string
  unitCode: string
  major: string
  indicatorName: string
}) => get<any>(`${ratCenterApi}/fullTime/dataDetail`, data)
```

## Api统一出口
```ts
export * from './**.api'
```


## Api的使用

```vue
<script lang="ts" setup>
import { getFullTimeDataDetail } from '@/rtaCenter/api/index.api'
getFullTimeDataDetail(query.value)
    .then((res) => {
      //.....
    })
</script>
```


## 注意：

### 使用的是delete/put请求,要使用封装的request请求

```ts
import { useAxios } from '@magustek/framework-core'
const { request } = useAxios()

export const deleteIndicatorById = (id: string) => request(`${ratCenterApi}/indicators/${id}`, { method: 'DELETE' })

export const editRuleEvents = (id: string, datas: RuleEventsAddDataType) =>
  request<RuleEventsDataType>(`${ratCenterApi}/ruleEvents/${id}`, { data: datas, method: 'PUT' })
```