---
title: types-exampls
description: cloudsis项目类型定义案例
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


## 类型定义

```ts
// src/types/xxx.type.ts
export interface xxxType {
  attr1: string | number;
  attr2: string | number;
}
```

## 统一导出

``` ts
// src/types/index.type.ts

export * from "./xxx.type"
```


## 页面使用

``` vue
<script lang="ts" setup>
import type { RulesDataType } from '@/types/index.type'

const data = ref<RulesDataType[]>()
</script>
```

## API使用
``` ts
import { ImportCallBackDataType, IndicatorPositionsDataType } from '@/types/index.type'

export const importIndicatorPositionsExcel = (data: FormData) =>
  post<ImportCallBackDataType>(`${ratCenterApi}/personal/indicatorPositions/excel`, data)
```